

# Redis

redis是一个基于内存的key-value数据库， 可用于缓存，事件发布订阅，高速队列等场景。

redis全称为 remote dictionary server （远程字典服务器），用c语言编写，当下热门的NoSql数据库。

redis默认有16个数据库，从0开始，select 数据库号 ，默认使用0数据库， redis默认没有密码。

## 五大基本数据类型：

String 、 list 、hash 、 set 、zset 

String：

set/get/mset/mget/incr/decr/incrby/decrby 等

List :

Lpush/Rpush/LPOP/RPOP/LRange

可以当作队列  lpush、 rpop，可以当作栈 lpush 、 lpop

Hash:

hset/hget/h/hmset/hmget/hgetAll/hDel

Set:

sAdd/smembers/spop/srem/sdiff/sunion

zSet:

zAdd/zrem/zscore/zRange



## 三种特殊数据类型

hyperloglog  基数，在误差可接受范围内，快速计算基数

geo 存储地理位置信息



bitmap 位图场景，

setbit / getbit / bitcount 



## redis 支持（部分支持）事务

redis执行事务的过程：

* 开启事务 multi
* 命令入队
* 执行事务 exec

redis的单个命令执行都是遵循原子性的，

然而**redis的事务却不遵循原子性**（要么都执行，要么都不执行）。

``` bash
127.0.0.1:6379> set k a
OK
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set k1 1
QUEUED
127.0.0.1:6379> incr k
QUEUED
127.0.0.1:6379> get k1
QUEUED
127.0.0.1:6379> get k
QUEUED
127.0.0.1:6379> exec
1) OK
2) (error) ERR value is not an integer or out of range
3) "1"
4) "a"
127.0.0.1:6379>
```

Watch , unwatch 实现乐观锁





## SpringBoot 整合

SpringBoot2.X之后，原来使用的jedis（被舍弃）被替换为两lettuce

jedis：采用的直连，多个线程操作是不安全的，如果想避免不安全，使用jedis pool 连接池，像BIO模式

lettuce：采用netty，实例可以在多个线程中进行共享不存在线程安全的情况，可以减少线程数据量，像NIO模式





redisTemplate 传输对象，所有对象需要序列化 

要么序列化对象， 要么转换成 String json = new ObjectMapper().writeValueAsString(new Object());



redis conf 详解 



redis持久化

redis rdb 快照

redis aof ， 如果appendonly.aof 破环了可以用redis-check-aof 修复



redis发布订阅



redis主从复制

主机写，从机读 

哨兵模式，配置比较复杂



redis缓存







redis常用命令

```
keys * // 查看当前库的所有key
exists key // 判断是否存在key
ttl key  // 查看key的过期时间 返回-2表示已过期或者不存在-1表示永不过期
expire key 秒  // 为key设置过期时间
type key // 判读key的类型

// 事务
multi   // 事务开启
exec    // 事务执行
watch key [key]  // 监视一个或多个key，如果在事务执行之前，这些key被操作，事务将被打断
unwatch  // 取消watch命令对所有key的监视
discard  // 取消事务

select 1    //选择redis的1号数据库config get  //获得服务配置confi get dir //获取redis的安装位置flushdb     //删除当前选择的数据库中的keyflushall       //删除所有数据库中的键infoinfo replication // 查看当前角色，主机，从机redsi-benchmark --help  //redis自带的性能测试工具，此命令不是在redis里面使用
```



