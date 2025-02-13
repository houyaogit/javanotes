# Redis+Lua脚本实现点赞功能demo

![img](https://cdn.jsdelivr.net/gh/houyaogit/Pictures@master/PicGo/1682415047267-872dee6e-287c-4057-9d51-1d73846d9167.jpeg)

## 需求场景

用户可以对每篇文章点赞，也可以对文章下的评论点赞，页面需要展示文章点赞的总数和评论点赞的总数，点赞后可以取消点赞，取消点赞后点赞统计数量相应地减少，要求点赞功能不能直接打到数据库层。

## demo代码

可以先跳过代码直接先看最下面的测试···环节。 首先目录结构如下![img](https://cdn.nlark.com/yuque/0/2023/png/2923566/1682415022440-5615c05f-3349-483e-9b2c-f31a5cc9e1d9.png)redisson配置类：



```java
package com.leo.like.config;

import org.redisson.Redisson;
import org.redisson.api.RedissonClient;
import org.redisson.client.codec.Codec;
import org.redisson.codec.JsonJacksonCodec;
import org.redisson.config.Config;
import org.springframework.context.annotation.Bean;
import org.springframework.stereotype.Component;

@Component
public class RedissonConfig {

    @Bean
    public RedissonClient RedissonClient(){
        Config config = new Config();
        config.useSingleServer()
                .setAddress("redis://127.0.0.1:6379")
                .setPassword("redisnopass");
        //使用json序列化方式（这里不设置的话，后面使用lua脚本就会出错，提示格式不对）
        Codec codec = new JsonJacksonCodec();
        config.setCodec(codec);
        return Redisson.create(config);
    }
}
```

Copy

启动类：



```java
package com.leo.like;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class LikeDemo {
    public static void main(String[] args) {
        SpringApplication.run(LikeDemo.class, args);
    }
}
```

Copy

先看LikeController接口：



```java
package com.leo.like.controller;

import com.leo.like.service.LikeService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("like")
public class LikeController {
    @Autowired
    LikeService likeService;

    @GetMapping("hit")
    public String like(@RequestParam("productId") Long productId,
                       @RequestParam("commentId") Long commentId,
                       @RequestParam("userId") Long userId) {
        Integer integer = likeService.like(commentId, productId, userId);
        return "success: " + integer;
    }
}
```

Copy

然后是LikeService



```java
package com.leo.like.service;

public interface LikeService {
    Integer like(Long commentId, Long productId, Long userId);
}
```

Copy

然后是具体的实现类LikeServiceImpl



```java
package com.leo.like.service.impl;

import com.google.common.collect.ImmutableList;
import com.google.common.io.Resources;
import com.leo.like.enums.RedisKeys;
import com.leo.like.service.LikeService;
import lombok.extern.slf4j.Slf4j;
import org.redisson.api.RScript;
import org.redisson.api.RedissonClient;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.io.IOException;
import java.net.URL;
import java.nio.charset.StandardCharsets;
import java.util.List;

@Slf4j
@Service
public class LikeServiceImpl implements LikeService {
    @Autowired
    private RedissonClient redissonClient;

    private static String userCommentLikeScript = "";


    static {
        //获取lua脚本
        URL url = Resources.getResource("lua/like.lua");
        try {
            userCommentLikeScript = Resources.toString(url, StandardCharsets.UTF_8);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }


    @Override
    public Integer like(Long commentId, Long productId, Long userId) {
        Integer integer = updateMemoryAgreeAndCancelAgree(commentId, productId, userId);
        log.info("updateMemoryAgreeAndCancelAgree res:{}", integer);
        return integer;
    }

    public Integer updateMemoryAgreeAndCancelAgree(Long commentId, Long productId, Long userId) {
        List<Object> keys = ImmutableList.of(
                String.format(RedisKeys.USER_COMMENT_LIKES.getKeyPrefix(), userId, productId),
                String.format(RedisKeys.COMMENT_LIKES.getKeyPrefix(), productId),
                RedisKeys.COMMENT_LIKE_LOGS.getKeyPrefix()
        );

        Long resp = redissonClient.getScript().eval(
                (String) keys.get(0),
                RScript.Mode.READ_WRITE,
                userCommentLikeScript,
                RScript.ReturnType.INTEGER,
                keys,
                commentId,
                productId
        );
        log.info("resp 的结果是：{}", resp);
        if (resp == -1) {
            log.info("数据不存在, 插入缓存:{}, commentId:{}", keys.get(1), commentId);
            //下面这个数据一般是从数据库读出来，这里为了方便手动直接写死一个值，就假设数据库中这个评论的点赞数是23
            redissonClient.getMap((String) keys.get(1)).put(commentId, 23);
            resp = redissonClient.getScript().eval(
                    (String) keys.get(0),
                    RScript.Mode.READ_WRITE,
                    userCommentLikeScript,
                    RScript.ReturnType.INTEGER,
                    keys,
                    commentId,
                    productId
            );

        }
        return resp.intValue();

    }
}
```

Copy

然后是lua脚本文件：like.lua



```lua
-- 这个脚本用户操作点赞、取消点赞，记录点赞操作，统计点赞数量， 输出值是
-- redis lua 脚本可以输出日志 redis.log(redis.LOG_WARNING,"日志内容"),
-- 在redis.conf里面可以配置 loglevel 默认的是notice,具体有哪些选择可以直接去看conf文件
-- 通过配置logfile可以指定日志文件保存地址，默认是""，我这里本地的redis设置的是/data/redis.log，也可以通过docker logs直接查看redis日志，
-- 因为我这里这个docker redis的容器的data文件夹是挂载在我本地的 /Users/apple/dockerData/redis/data 的，
-- 所以可以直接在本地查看redis.log

--USER_COMMENT_LIKES("user_comment_likes:{%d}:%d","【用户侧评论赞成数量维护】（set结构，{%d}=userId,%d=productId,value=commentId）"),
local user_comment_likes_key = KEYS[1]
--COMMENT_LIKES("product_comment_likes:{%d}","【评论赞成数量维护】（hash结构，%d=productId，key=评论id,value=评论赞成数量）"),
local comment_like_key = KEYS[2]
--COMMENT_LIKE_LOGS("comment_like_logs","评论赞成数量日志】（list结构，value结构=productId:commentId|memoryAgreeCount（评论赞成数量）)");
local comment_like_logs_ley = KEYS[3]
--评论id
local commentId = ARGV[1]
--商品id
local productId = ARGV[2]
--flag: 0取消点赞 1点赞 -1不存在商品，从数据库查
local flag = 0

local memoryAgreeCount = 0

redis.log(redis.LOG_WARNING,
'用户侧评论点赞数量维护链:', user_comment_likes_key,
'评论点赞数量维护链', comment_like_key,
'评论点赞数量日志', comment_like_logs_ley)

redis.log(redis.LOG_WARNING,
'评论id', commentId)

-- 评论点赞数量维护链不存在，查询数据库设置
if redis.call("HEXISTS", comment_like_key, commentId) ~= 1 then
    flag = -1
    return flag
end

--判断该用户之前是否点赞过
if redis.call("SISMEMBER", user_comment_likes_key, commentId) == 1 then
    redis.log(redis.LOG_WARNING, "该用户已经点赞过，执行取消点赞按钮")
    -- 从set中移除
    redis.call("SREM",user_comment_likes_key,commentId)
    memoryAgreeCount = redis.call("HINCRBY", comment_like_key, commentId,-1)
    flag = 0
else
    redis.log(redis.LOG_WARNING,"该用户没有点击过点赞按钮或者取消点赞了，执行点赞操作，将内存中的评论点赞数量进行加1")
    --加入到已点赞的set集合
    redis.call("SADD",user_comment_likes_key, commentId)
    --不为空将内存中的评论点赞数量即逆行加减
    redis.call("HINCRBY",comment_like_key, commentId, 1)
    memoryAgreeCount = redis.call("HGET", comment_like_key,commentId)
    flag = 1
end
redis.log(redis.LOG_WARNING, "获取内存中的点赞数量", memoryAgreeCount)
-- 记录日志列表
redis.call("RPUSH",comment_like_logs_ley, productId .. ":" .. commentId .. "|" .. memoryAgreeCount)
return flag
```

Copy

## 测试

我们来实际跑一下看看结果：

### 第一次请求

id为1的用户对id为13的商品下面的id为104的评论进行点赞



```bash
curl -X GET http://127.0.0.1:8080/like/hit?productId=13&commentId=104&userId=1
```

Copy

接口返回结果：success:1,表示点赞成功1次 然后查看一下redis记录的值

- 首先是查看该商品下的每条评论的点赞总数， 数据结构是hash



```bash
#查询id=13的商品下面所有评论的总量
127.0.0.1:6379> HGETALL product_comment_likes:{13}
1) "[\"java.lang.Long\",104]"
2) "24"
127.0.0.1:6379>
```

Copy

由于product_comment_likes:{13}这个key的存储结构是hash，所以可以看到上面查出来的结果是键值对的形式，结果显示评论id为104的点赞数量是24.为什么是24？因为上面代码写了，为了方便，直接假设从数据库读出来的历史点赞数量是23，所以加上这次点赞是24个点赞。

- 然后查看该用户对商品id为13的下面的评论的点赞记录,数据结构是set：



```bash
# key是user_comment_likes:{1}:13, 代表id为1的用户对id为13的商品下面的评论的点赞记录
127.0.0.1:6379> SMEMBERS user_comment_likes:{1}:13
1) "[\"java.lang.Long\",104]"
127.0.0.1:6379>
```

Copy

上面看出，id=1的用户对id=13的商品下面的评论的点赞只有一条记录，就是id为104的那条评论。

- 然后查看点赞记录, 数据结构为List：



```bash
# LRANGE key startindex endindex, 下面表示获取comment_like_logs列表里面的所有元素
127.0.0.1:6379> LRANGE comment_like_logs 0 -1
1) "[\"java.lang.Long\",13]:[\"java.lang.Long\",104]|24"
127.0.0.1:6379>
```

Copy

可以看到目前只有一条记录，就是商品13下面的评论104的点赞总数是24。

### 第二次请求

接下来，同样是用户1，对商品13下面id为105的评论进行点赞：



```bash
#请求
curl -X GET http://127.0.0.1:8080/like/hit?prodcutId=13&commentId=105&userId=1
#响应结果
success:1
```

Copy

然后来看一下结果：

- 商品评论点赞数量统计



```bash
127.0.0.1:6379> HGETALL product_comment_likes:{13}
1) "[\"java.lang.Long\",104]"
2) "24"
3) "[\"java.lang.Long\",105]"
4) "24"
127.0.0.1:6379>
```

Copy

可以看到评论105的点赞数量也是24了（前面说过了所有评论的初始点赞数量都设定了是23）。

- 用户点赞记录：



```bash
127.0.0.1:6379> SMEMBERS user_comment_likes:{1}:13
1) "[\"java.lang.Long\",105]"
2) "[\"java.lang.Long\",104]"
127.0.0.1:6379>
```

Copy

可以看到该用户对105的评论进行点赞了

- 点赞日志：



```bash
127.0.0.1:6379> LRANGE comment_like_logs 0 -1
1) "[\"java.lang.Long\",13]:[\"java.lang.Long\",104]|24"
2) "[\"java.lang.Long\",13]:[\"java.lang.Long\",105]|24"
127.0.0.1:6379>
```

Copy

可以看到记录了评论105的点赞数量

### 第三次请求

同样是用户1，对商品13下面id为104的评论取消点赞。点赞还是取消点赞是在lua脚本里面进行判断的，不清楚的话可以再回上面看一下代码。



```bash
#请求
curl -X GET http://127.0.0.1:8080/like/hit?prodcutId=13&commentId=104&userId=1
#响应结果
success:0
```

Copy

我们看到已经去掉点赞了，然后在看看redis

商品评论点赞数量统计：



```bash
127.0.0.1:6379> HGETALL product_comment_likes:{13}
1) "[\"java.lang.Long\",104]"
2) "23"
3) "[\"java.lang.Long\",105]"
4) "24"
127.0.0.1:6379>
```

Copy

可以看到104的点赞已经变成23了。

- 用户点赞记录：



```bash
127.0.0.1:6379> SMEMBERS user_comment_likes:{1}:13
1) "[\"java.lang.Long\",105]"
127.0.0.1:6379>
```

Copy

可以看到这个set只剩下105这条评论的点赞了

- 点赞日志：



```bash
127.0.0.1:6379> LRANGE comment_like_logs 0 -1
1) "[\"java.lang.Long\",13]:[\"java.lang.Long\",104]|24"
2) "[\"java.lang.Long\",13]:[\"java.lang.Long\",105]|24"
3) "[\"java.lang.Long\",13]:[\"java.lang.Long\",104]|23"
127.0.0.1:6379>
```

Copy

可以看到记录了评论104的点赞数量变回23了

这样就实现了点赞的日志记录，点赞的数量统计，以及取消点赞

## 总结

从上面我们可以看出，通过利用redis的lua的原子性操作，高效的实现了点赞取消点赞的功能，然后只需要写个定时任务，每天比如说半夜把数据写入数据库就行了，点赞的反复操作基本不会打到数据库