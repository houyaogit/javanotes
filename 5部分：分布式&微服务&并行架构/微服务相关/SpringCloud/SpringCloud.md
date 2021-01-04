# SpringCloud:

分布式系统原理和范型：

分布式系统是若干独立计算机的集合，这些计算机对于用户来说就像耽搁相关系统。

分布式系统是建立在网络之上的软件系统。



随着互联网的发展，网站应用的规模不断扩大，传统的垂直应用架构已经无法应对，分布式架构以及流动计算架构势在必行，急需一个治理系统确保架构有条不紊的演进。（三个臭皮匠顶个诸葛亮）

# SpringCloud

![](https://gitee.com/houyao123/my-resource/raw/master/img/image-20210103220911909.png)

Eureka 集群， 服务注册中心 （集群）， CAP 中的 AP原则，

Ribbon 客户端负载均衡 ， 负载规则，需要放在springscan扫描报外，（手写）

Feign 客户端 ， 提供负载均衡的http 客户端 ， 让调用远端端口，就系那个本地端口一样

Hystrix 熔断器

 dashboard仪表盘， 	Turbine 多面板监控 （management port 管理端口统一）

feign 使用fallbackfactory



**脑裂问题 ： 有leader ，由于网络分区问题， 产生多个leader ， 多个写的leader ， 网络恢复时 有个leader 的写丢失**

**zookeeper 没有脑裂问题， 集群半数以上才能选举 leader ，**

**注册中心 CAP原则**

*C：一致性（Consistency）、A：可用性（Availability）、P：分区容错醒（Partition tolerance）*

**P必须满足， 为什么只能 CP。或 AP** 

| 组件      | 语言 | CAP  | 服务健康检查 | 对外暴露接口 |
| --------- | ---- | ---- | ------------ | ------------ |
| Eureka    | Java | AP   | 可配支持     | HTTP         |
| Consul    | Go   | CP   | 支持         | HTTP/DNS     |
| Zookeeper | Java | CP   | 支持         | 客户端       |



**BASE原则**

BA：基本可用、S：软状态、E：最终一致性

CAP原则是三选二，BASE原则是CAP的这种，C、A、P三个都要，但是不用100%保证每一个原则，分布式系统肯定优先保证P，多书时候是在C 、 A之间权衡选择





## 0,SpringCloud升级,部分组件停用:

1,Eureka停用,可以使用zk作为服务注册中心

2,服务调用,Ribbon准备停更,代替为LoadBalance

3,Feign改为OpenFeign

4,Hystrix停更,改为resilence4j

​		或者阿里巴巴的sentienl

5.Zuul改为gateway

6,服务配置Config改为  Nacos

7,服务总线Bus改为Nacos





# 环境搭建:



## 1,创建父工程,pom依赖

```java
....
```

## 2,创建子模块,pay模块

建module、改POM、写YML、主启动、业务类

### 1,子模块名字:

​		cloud_pay_8001

### 2,pom依赖

### 3,创建application.yml

```yml
server:
	port: 8001   
spring:
	application:
		name: cloud-payment-service
	datasource:
    # 当前数据源操作类型
    type: com.alibaba.druid.pool.DruidDataSource
    # mysql驱动类
    driver-class-name: com.mysql.cj.jdbc.Driver
      url: jdbc:mysql://localhost:3306/db2019?useUnicode=true&characterEncoding=
            UTF-8&useSSL=false&serverTimezone=GMT%2B8								
    username: root
    password: root
mybatis:			
    mapper-locations: classpath*:mapper/*.xml
   	type-aliases-package: com.eiletxie.springcloud.entities
   			它一般对应我们的实体类所在的包，这个时候会自动取对应包中不包括包名的简单类名作为包括包名的别名。多个package之间可以用逗号或者分号等来进行分隔（value的值一定要是包的全）
```

### 4,主启动类    

​		....

### 5,业务类

#### 1,sql

![](https://gitee.com/houyao123/my-resource/raw/master/img/image-20210103221545211.png)

#### 2,实体类

![](.\图片\sc的5.png)

#### 3,.entity类

![](.\图片\sc的6.png)

#### 4,dao层:

![](.\图片\sc的7.png)

#### 5,mapper配置文件类

​				**在resource下,创建mapper/PayMapper.xml**

![](/Users/houyao/Documents/notes/typora/javanotes/5%E9%83%A8%E5%88%86%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F&%E5%BE%AE%E6%9C%8D%E5%8A%A1&%E5%B9%B6%E8%A1%8C%E6%9E%B6%E6%9E%84/%E5%BE%AE%E6%9C%8D%E5%8A%A1%E7%9B%B8%E5%85%B3/SpringCloud/.%5C%E5%9B%BE%E7%89%87%5Csc%E7%9A%848.png)

#### 6,写service和serviceImpl

![](/Users/houyao/Documents/notes/typora/javanotes/5%E9%83%A8%E5%88%86%EF%BC%9A%E5%88%86%E5%B8%83%E5%BC%8F&%E5%BE%AE%E6%9C%8D%E5%8A%A1&%E5%B9%B6%E8%A1%8C%E6%9E%B6%E6%9E%84/%E5%BE%AE%E6%9C%8D%E5%8A%A1%E7%9B%B8%E5%85%B3/SpringCloud/.%5C%E5%9B%BE%E7%89%87%5Csc%E7%9A%849.png)

![sc的9](.\图片\sc的10.png)

#### 7,controller

![](.\图片\sc的11.png)

![](.\图片\sc的12.png)







## 3,热部署:

![](.\图片\sc的13.png)

![](.\图片\sc的14.png)

.....

.....

....





## 4,order模块

![](.\图片\sc的3.png)

### **1,pom**		

### **2,yml配置文件**

![](.\图片\order模块1.png)

### **3,主启动类**

### **4.复制pay模块的实体类,entity类**

### **5,写controller类**

​		因为这里是消费者类,主要是消费,那么就没有service和dao,需要调用pay模块的方法

​		并且这里还没有微服务的远程调用,那么如果要调用另外一个模块,则需要使用基本的api调用

使用RestTemplate调用pay模块,

​	![](.\图片\order模块2.png)

![](.\图片\order模块3.png)



​	将restTemplate注入到容器

![](.\图片\order模块4.png)

编写controller:

![](.\图片\order模块5.png)



## 5,重构,

新建一个模块,将重复代码抽取到一个公共模块中

### 1,创建commons模块

### 2,抽取公共pom

![](.\图片\commons模块.png)

### 3,entity和实体类放入commons中

![](.\图片\commons模块2.png)

### 4,使用mavne,将commone模块打包(install),

​		其他模块引入commons







# 2,服务注册与发现



## 6,Eureka:

前面我们没有服务注册中心,也可以服务间调用,为什么还要服务注册?

当服务很多时,单靠代码手动管理是很麻烦的,需要一个公共组件,统一管理多服务,包括服务是否正常运行,等

Eureka用于**==服务注册==**,目前官网**已经停止更新**

​	![](.\图片\Eureka的1.png)



![](.\图片\Eureka的2.png)

![](.\图片\Eureka的3.png)



 ![](.\图片\Eureka的4.png)



### **单机版eureka:**

#### **1,创建项目cloud_eureka_server_7001**

#### **2,引入pom依赖**

​		eurka最新的依赖变了

![](.\图片\Eureka的5.png)

#### 3,配置文件:

![](.\图片\Eureka的6.png)

#### 4,主启动类	

![](.\图片\Eureka的7.png)

#### **5,此时就可以启动当前项目了**

#### **6,其他服务注册到eureka:**

比如此时pay模块加入eureka:

##### 1.主启动类上,加注解,表示当前是eureka客户端

![](.\图片\Eureka的10.png)

##### 2,修改pom,引入

![](.\图片\Eureka的8.png)

##### 3,修改配置文件:

![](.\图片\Eureka的9.png)

##### 4,pay模块重启,就可以注册到eureka中了





**==order模块的注册是一样的==**





### 集群版eureka:

#### 集群原理:

![](.\图片\Eureka的11.png)

 ```java
1,就是pay模块启动时,注册自己,并且自身信息也放入eureka
2.order模块,首先也注册自己,放入信息,当要调用pay时,先从eureka拿到pay的调用地址
3.通过HttpClient调用
 	并且还会缓存一份到本地,每30秒更新一次
 ```

![](.\图片\Eureka的12.png)

**集群构建原理:**

​		互相注册

![](.\图片\Eureka的13.png)



#### **构建新erueka项目**

名字:cloud_eureka_server_7002

##### 1,pom文件:

​		粘贴7001的即可

##### 2,配置文件:

​		在写配置文件前,修改一下主机的hosts文件

![](.\图片\Eureka的14.png)

首先修改之前的7001的eureka项目,因为多个eureka需要互相注册

![](.\图片\Eureka的15.png)

然后修改7002

​			**7002也是一样的,只不过端口和地址改一下**

##### 3,主启动类:

​		复制7001的即可

##### 4,然后启动7001,7002即可

*![](.\图片\Eureka的16.png)*





#### 将pay,order模块注册到eureka集群中:

##### 1,只需要修改配置文件即可:

![](.\图片\Eureka的17.png)

##### 2,两个模块都修改上面的都一样即可

​			然后启动两个模块

​			要先启动7001,7002,然后是pay模块8001,然后是order(80)



### 3,将pay模块也配置为集群模式:

#### 0,创建新模块,8002

​	名称: cloud_pay_8002

#### 1,pom文件,复制8001的

#### 2,pom文件复制8001的

#### 3,配置文件复制8001的

​		端口修改一下,改为8002

​		服务名称不用改,用一样的

#### 4.主启动类,复制8001的

#### 5,mapper,service,controller都复制一份

​		然后就启动服务即可

​		此时访问order模块,发现并没有负载均衡到两个pay,模块中,而是只访问8001

​		虽然我们是使用RestTemplate访问的微服务,但是也可以负载均衡的

​		![](.\图片\Eureka的18.png)

**注意这样还不可以,需要让RestTemplate开启负载均衡注解,还可以指定负载均衡算法,默认轮询**

![](.\图片\Eureka的19.png)







### 4,修改服务主机名和ip在eureka的web上显示

比如修改pay模块

#### 1,修改配置文件:

![](.\图片\Eureka的20.png)





### 5,eureka服务发现:

![](.\图片\Eureka的21.png)

以pay模块为例

#### 1,首先添加一个注解,在controller中

![](.\图片\Eureka的22.png)

![](.\图片\Eureka的23.png)



#### 2,在主启动类上添加一个注解

![](.\图片\Eureka的24.png)

**然后重启8001.访问/payment/discover**y





### 6,Eureka自我保护:

![](.\图片\Eureka的26.png)

![](.\图片\Eureka的27.png)

![](.\图片\Eureka的25.png)



![](.\图片\Eureka的28.png)



**eureka服务端配置:**

![](.\图片\Eureka的29.png)

![](.\图片\Eureka的30.png)

​			**设置接受心跳时间间隔**



**客户端(比如pay模块):**

![](.\图片\Eureka的31.png)





**此时启动erueka和pay.此时如果直接关闭了pay,那么erueka会直接删除其注册信息**









## 7,Zookeeper服务注册与发现:

### 1,启动zk,到linux上



### 2,创建新的pay模块,

单独用于注册到zk中  

名字 : cloud_pay_8003

#### 1,pom依赖

#### 2,配置文件

![](.\图片\zookeeper的3.png)

#### 3,主启动类

![](.\图片\zookeeper的1.png)

#### 4,controller

![](.\图片\zookeeper的2.png)

#### 5,然后就可以启动

**此时启动,会报错,因为jar包与我们的zk版本不匹配**

解决:
		修改pom文件,改为与我们zk版本匹配的jar包

![](.\图片\zookeeper的4.png)

**此时8003就注册到zk中了**

```java
我们在zk上注册的node是临时节点,当我们的服务一定时间内没有发送心跳
  	那么zk就会`将这个服务的node删除了
```



**这里测试,就不写service与dao什么的了**









### 3,创建order消费模块注册到zk

#### 1,创建项目

名字: cloud_order_zk_80

#### 2,pom

#### 3,配置文件

![](.\图片\zookeeper的5.png)

#### 4主启动类:

![](.\图片\zookeeper的1.png)

#### 5,RestTemolate

![注意,这里使用RestTemolate,要先注册它](.\图片\zookeeper的6.png)

#### 6,controller

![](.\图片\zookeeper的7.png)

**然后启动即可注册到zk**

#### 8,集群版zk注册:

只需要修改配置文件:

![](.\图片\zookeeper的5.png)

这个connect-string指定多个zk地址即可

connect-string: 1.2.3.4,2.3.4.5

#### 



















## 8,Consul:

![](.\图片\consul的1.png)



![](.\图片\consul的2.png)





### 1,按照consul

需要下载一个安装包

![](.\图片\consul的3.png)

启动是一个命令行界面,需要输入consul agen-dev启动



### 2,创建新的pay模块,8006

#### 1,项目名字

cloud_consule_pay_8006

#### 2,pom依赖

#### 3,配置文件

![](.\图片\consul的4.png)

#### 4,主启动类

![](.\图片\consul的5.png)

#### 5,controller

![](.\图片\consul的6.png)

#### 6,启动服务



#### 



### 3,创建新order模块

cloud-consul-order-80



#### 1,pom文件

#### 2,配置文件

![](.\图片\consul的7.png)

#### 3,主启动类

![](.\图片\consul的5.png)

#### 4,RestTemplate注册

配置类注册

#### 5,controller

![](.\图片\consul的8.png)

#### 6,启动服务,测试







## 9,三个注册中心的异同:

![](.\图片\consul的9.png)

![](.\图片\consul的10.png)

![](.\图片\consul的11.png)







# 3,服务调用



## 10,Ribbon负载均衡:

![](.\图片\Ribbon.png)

**Ribbon目前也进入维护,基本上不准备更新了**

![](.\图片\Ribbon的2.png)

**进程内LB(本地负载均衡)**

![](.\图片\Ribbon的5.png)





**集中式LB(服务端负载均衡)**

![](.\图片\Ribbon的4.png)







**区别**

![](.\图片\Ribbon的3.png)



**Ribbon就是负载均衡+RestTemplate**

![](.\图片\Ribbon的6.png)



![](.\图片\Ribbon的7.png)



![](.\图片\Ribbon的8.png)







### 使用Ribbon:

#### 1,默认我们使用eureka的新版本时,它默认集成了ribbon:

![](.\图片\Ribbon的9.png)

**==这个starter中集成了reibbon了==**



#### 2,我们也可以手动引入ribbon

**放到order模块中,因为只有order访问pay时需要负载均衡**

![](.\图片\Ribbon的10.png)



#### 3,RestTemplate类:

![](.\图片\Ribbon的11.png)

![](.\图片\Ribbon的12.png)

```java
RestTemplate的:
		xxxForObject()方法,返回的是响应体中的数据
    xxxForEntity()方法.返回的是entity对象,这个对象不仅仅包含响应体数据,还包含响应体信息(状态码等)
```





#### Ribbon常用负载均衡算法:

**IRule接口,Riboon使用该接口,根据特定算法从所有服务中,选择一个服务,**

**Rule接口有7个实现类,每个实现类代表一个负载均衡算法**

![](.\图片\Ribbon的14.png)







#### 使用Ribbon:

**==这里使用eureka的那一套服务==**

![](.\图片\Ribbon的15.png)

**==也就是不能放在主启动类所在的包及子包下==**

##### 1,修改order模块

##### 2,额外创建一个包

![](.\图片\Ribbon的16.png)

##### 3,创建配置类,指定负载均衡算法

![](.\图片\Ribbon的17.png)

##### 4,在主启动类上加一个注解

![](.\图片\Ribbon的18.png)

**表示,访问CLOUD_pAYMENT_SERVICE的服务时,使用我们自定义的负载均衡算法**







#### 自定义负载均衡算法:

##### 1,ribbon的轮询算法原理

![](.\图片\Ribbon的19.png)

![](.\图片\Ribbon的21.png)



##### 2,自定义负载均衡算法:

**1,给**pay模块(8001,8002),的controller方法添加一个方法,返回当前节点端口

![](.\图片\Ribbon的23.png)

![](.\图片\Ribbon的22.png)

**2,修改order模块**

去掉@LoadBalanced

![](.\图片\Ribbon的24.png)



##### 3,自定义接口

![](.\图片\Ribbon的29.png)

​					==具体的算法在实现类中实现==

##### 4,接口实现类

![](.\图片\Ribbon的25.png)

![](.\图片\Ribbon的26.png)



##### 5,修改controller:

![](.\图片\Ribbon的27.png)

![](.\图片\Ribbon的28.png)



##### 6,启动服务,测试即可	





























## 11,OpenFeign

![](.\图片\Feign的1.png)

**是一个声明式的web客户端,只需要创建一个接口,添加注解即可完成微服务之间的调用**



![](.\图片\Feign的2.png)

==就是A要调用B,Feign就是在A中创建一个一模一样的B对外提供服务的的接口,我们调用这个接口,就可以服务到B==



### **Feign与OpenFeign区别**

![](.\图片\Feign的3.png)





### 使用OpenFeign

```java
之前的服务间调用,我们使用的是ribbon+RestTemplate
		现在改为使用Feign
```

#### 1,新建一个order项目,用于feign测试

名字cloud_order_feign-80

#### 2,pom文件

#### 3,配置文件

![](.\图片\Feign的4.png)

#### 4,主启动类

![](.\图片\Feign的5.png)

#### 5,fegin需要调用的其他的服务的接口

![](.\图片\Feign的6.png)

#### 6,controller

![](.\图片\Feign的7.png)

#### 7测试:

启动两个erueka(7001,7002)

启动两个pay(8001,8002)

启动当前的order模块



**Feign默认使用ribbon实现负载均衡**





### OpenFeign超时机制:

==OpenFeign默认等待时间是1秒,超过1秒,直接报错==

#### 1,设置超时时间,修改配置文件:

**因为OpenFeign的底层是ribbon进行负载均衡,所以它的超时时间是由ribbon控制**

![](.\图片\Feign的8.png)





### OpenFeign日志:

![](.\图片\Feign的9.png)



**OpenFeign的日志级别有:**
![](.\图片\Feign的10.png)





#### 	1,使用OpenFeign的日志:

**实现在配置类中添加OpenFeign的日志类**

![](.\图片\Feign的11.png)

#### 2,为指定类设置日志级别:

![](.\图片\Feign的13.png)

**配置文件中:**

![](.\图片\Feign的12.png)



#### 	3,启动服务即可



# 4,服务降级:



## 12,Hystrix服务降级

![](.\图片\Hystrix的2.png)



![](.\图片\Hystrix的3.png)





![](.\图片\Hystrix的4.png)







### hystrix中的重要概念:

#### 1,服务降级

**比如当某个服务繁忙,不能让客户端的请求一直等待,应该立刻返回给客户端一个备选方案**



#### 2,服务熔断

**当某个服务出现问题,卡死了,不能让用户一直等待,需要关闭所有对此服务的访问**

​			**然后调用服务降级**



#### 3,服务限流

**限流,比如秒杀场景,不能访问用户瞬间都访问服务器,限制一次只可以有多少请求**





### 使用hystrix,服务降级:

#### 1,创建带降级机制的pay模块 :

名字: cloud-hystrix-pay-8007

##### 2,pom文件

##### 3,配置文件

![](.\图片\Hystrix的5.png)

##### 4,主启动类

![](.\图片\Hystrix的8.png)

##### 5,service

![](.\图片\Hystrix的6.png)

##### 6controller

![](.\图片\Hystrix的7.png)

##### 7,先测试:

```java
此时使用压测工具,并发20000个请求,请求会延迟的那个方法,
		压测中,发现,另外一个方法并没有被压测,但是我们访问它时,却需要等待
		这就是因为被压测的方法它占用了服务器大部分资源,导致其他请求也变慢了
```



##### 8,先不加入hystrix,



#### 2,创建带降级的order模块:

##### 1,名字:  cloud-hystrix-order-80

##### 2,pom

##### 3,配置文件

![](.\图片\Hystrix的9.png)

##### 4,主启动类

![](.\图片\Hystrix的11.png)

##### 5,远程调用pay模块的接口:

![](.\图片\Hystrix的12.png)

##### 6,controller:

![](.\图片\Hystrix的13.png)

##### 7,测试

​			启动order模块,访问pay

​			再次压测2万并发,发现order访问也变慢了

![](.\图片\Hystrix的14.png)



**解决:**

![](.\图片\Hystrix的15.png)

##### ![](.\图片\Hystrix的16.png)







#### 3,配置服务降级:

##### 1,修改pay模块

###### 1,为service的指定方法(会延迟的方法)添加@HystrixCommand注解

![](.\图片\Hystrix的17.png)

###### 2,主启动类上,添加激活hystrix的注解

![](.\图片\Hystrix的18.png)

###### 3,触发异常

![](.\图片\Hystrix的19.png)

![](.\图片\Hystrix的20.png)**可以看到,也触发了降级**



##### 2,修改order模块,进行服务降级

一般服务降级,都是放在客户端(order模块),

![](.\图片\Hystrix的21.png)

###### 1,修改配置文件:

![](.\图片\Hystrix的22.png)

###### **2,主启动类添加直接,启用hystrix:**

![](.\图片\Hystrix的23.png)

​	

###### 3,修改controller,添加降级方法什么的

![](.\图片\Hystrix的24.png)



###### 4,测试

启动pay模块,order模块,

**注意:,这里pay模块和order模块都开启了服务降级**

​			但是order这里,设置了1.5秒就降级,所以访问时,一定会降级

 

##### 4,重构:

**上面出现的问题:**
		1,降级方法与业务方法写在了一块,耦合度高

​		2.每个业务方法都写了一个降级方法,重复代码多

##### **解决重复代码的问题**:

**配置一个全局的降级方法,所有方法都可以走这个降级方法,至于某些特殊创建,再单独创建方法**

###### 1,创建一个全局方法

![](.\图片\Hystrix的26.png)

###### 2,使用注解指定其为全局降级方法(默认降级方法)

![](.\图片\Hystrix的27.png)

![](.\图片\Hystrix的25.png)



###### 3,业务方法使用默认降级方法:

![](.\图片\Hystrix的28.png)



###### 4,测试:

![](.\图片\Hystrix的29.png)











##### 解决代码耦合度的问题:

修改order模块,这里开始,pay模块就不服务降级了,服务降级写在order模块即可

###### 1,Payservice接口是远程调用pay模块的,我们这里创建一个类实现service接口,在实现类中统一处理异常

![](.\图片\Hystrix的30.png)

###### 2,修改配置文件:添加:

![](.\图片\Hystrix的31.png)

###### 	3,让PayService的实现类生效:

![](.\图片\Hystrix的32.png)

```java
它的运行逻辑是:
		当请求过来,首先还是通过Feign远程调用pay模块对应的方法
    但是如果pay模块报错,调用失败,那么就会调用PayMentFalbackService类的
    当前同名的方法,作为降级方法
```

###### 4,启动测试

启动order和pay正常访问--ok

==此时将pay服务关闭,order再次访问==

![](.\图片\Hystrix的33.png)

可以看到,并没有报500错误,而是降级访问==实现类==的同名方法

这样,即使服务器挂了,用户要不要一直等待,或者报错

问题:

​		**这样虽然解决了代码耦合度问题,但是又出现了过多重复代码的问题,每个方法都有一个降级方法**







### 使用服务熔断:

![](.\图片\Hystrix的34.png)

**比如并发达到1000,我们就拒绝其他用户访问,在有用户访问,就访问降级方法**



![](.\图片\Hystrix的35.png)



#### 1,修改前面的pay模块

##### **1,修改Payservice接口,添加服务熔断相关的方法:**

![](.\图片\Hystrix的37.png)

这里属性整体意思是:
			10秒之内(窗口,会移动),如果并发==超过==10个,或者10个并发中,失败了6个,就开启熔断器

![image-20200414152637247](.\图片\Hystrix的43.png)



IdUtil是Hutool包下的类,这个Hutool就是整合了所有的常用方法,比如UUID,反射,IO流等工具方法什么的都整合了





![](.\图片\Hystrix的36.png)

```java
断路器的打开和关闭,是按照一下5步决定的
  	1,并发此时是否达到我们指定的阈值
  	2,错误百分比,比如我们配置了60%,那么如果并发请求中,10次有6次是失败的,就开启断路器
  	3,上面的条件符合,断路器改变状态为open(开启)
  	4,这个服务的断路器开启,所有请求无法访问
  	5,在我们的时间窗口期,期间,尝试让一些请求通过(半开状态),如果请求还是失败,证明断路器还是开启状态,服务没有恢复
  		如果请求成功了,证明服务已经恢复,断路器状态变为close关闭状态
```



##### 2,修改controller

添加一个测试方法;

![](.\图片\Hystrix的39.png)



##### 3,测试:

启动pay,order模块

==多次访问,并且错误率超过60%:==

![](.\图片\Hystrix的40.png)

此时服务熔断,此时即使访问正确的也会报错:

![](.\图片\Hystrix的41.png)

**但是,当过了几秒后,又恢复了**

​				因为在10秒窗口期内,它自己会尝试接收部分请求,发现服务可以正常调用,慢慢的当错误率低于60%,取消熔断











### Hystrix所有可配置的属性:

**全部在这个方法中记录,以成员变量的形式记录,**

​		以后需要什么属性,查看这个类即可

![](.\图片\Hystrix的38.png)









### 总结:

![](.\图片\Hystrix的42.png)

**==当断路器开启后:==**

​	![](.\图片\Hystrix的44.png)



**==其他参数:==**

![](.\图片\Hystrix的45.png)

![](.\图片\Hystrix的46.png)

![](.\图片\Hystrix的47.png)

![](.\图片\Hystrix的48.png)

![](.\图片\Hystrix的49.png)



**熔断整体流程:**

```java
1请求进来,首先查询缓存,如果缓存有,直接返回
  	如果缓存没有,--->2
2,查看断路器是否开启,如果开启的,Hystrix直接将请求转发到降级返回,然后返回
  	如果断路器是关闭的,
				判断线程池等资源是否已经满了,如果已经满了
  					也会走降级方法
  			如果资源没有满,判断我们使用的什么类型的Hystrix,决定调用构造方法还是run方法
        然后处理请求
        然后Hystrix将本次请求的结果信息汇报给断路器,因为断路器此时可能是开启的
          			(因为断路器开启也是可以接收请求的)
        		断路器收到信息,判断是否符合开启或关闭断路器的条件,
				如果本次请求处理失败,又会进入降级方法
        如果处理成功,判断处理是否超时,如果超时了,也进入降级方法
        最后,没有超时,则本次请求处理成功,将结果返回给controller
         
 
```







### Hystrix服务监控:

#### HystrixDashboard

![](.\图片\Hystrix的51.png)

#### 2,使用HystrixDashboard:

##### 1,创建项目:

名字: cloud_hystrixdashboard_9001

##### 2,pom文件

##### 3,配置文件

![](.\图片\Hystrix的52.png)

##### 4,主启动类

![](.\图片\Hystrix的53.png)

##### 5,修改所有pay模块(8001,8002,8003...)

**他们都添加一个pom依赖:**

![](.\图片\Hystrix的54.png)

之前的pom文件中都添加过了,==这个是springboot的监控组件==

##### 6,启动9001即可

​			访问: **localhost:9001/hystrix**

##### 7,注意,此时仅仅是可以访问HystrixDashboard,并不代表已经监控了8001,8002

​							如果要监控,还需要配置:(8001为例)

==8001的主启动类添加:==

![](.\图片\Hystrix的55.png)

**其他8002,8003都是一样的**

##### 8,到此,可以启动服务

启动7001,8001,9001

**然后在web界面,指定9001要监控8001:**

##### ![](.\图片\Hystrix的56.png)



![](.\图片\Hystrix的57.png)

![](.\图片\Hystrix的59.png)

![](.\图片\Hystrix的58.png)

![](.\图片\Hystrix的60.png)

![](.\图片\Hystrix的61.png)

![](.\图片\Hystrix的62.png)



















# 5,服务网关:

zuul停更了,

## 13,GateWay



![](.\图片\gateway的1.png)

![](.\图片\gateway的2.png)

**gateway之所以性能号,因为底层使用WebFlux,而webFlux底层使用netty通信(NIO)**



![](.\图片\gateway的3.png)



### GateWay的特性:

![](.\图片\gateway的4.png)



### GateWay与zuul的区别:

![](.\图片\gateway的5.png)



### zuul1.x的模型:

![](.\图片\gateway的6.png)

![](.\图片\gateway的7.png)





### 什么是webflux:

**是一个非阻塞的web框架,类似springmvc这样的**

![](.\图片\gateway的8.png)



### GateWay的一些概念:

#### 1,路由:

![](.\图片\gateway的9.png)

就是根据某些规则,将请求发送到指定服务上



#### 2,断言:

![](.\图片\gateway的10.png)

就是判断,如果符合条件就是xxxx,反之yyyy



#### 3,过滤:

![](.\图片\gateway的11.png)

​	**路由前后,过滤请求**





### GateWay的工作原理:

![](.\图片\gateway的12.png)

![](.\图片\gateway的13.png)





### 使用GateWay:

想要新建一个GateWay的项目

名字: 	cloud_gateway_9527

#### 1,pom

#### 2,配置文件

![](.\图片\gateway的14.png)

#### 3,主启动类

![](.\图片\gateway的15.png)

#### 4,针对pay模块,设置路由:

![](.\图片\gateway的16.png)

![](.\图片\gateway的18.png)

**==修改GateWay模块(9527)的配置文件==:**

![](.\图片\gateway的17.png)

这里表示,

​			当访问localhost:9527/payment/get/1时,     

​			路由到localhost:8001/payment/get/1



#### 5,开始测试

**启动7001,8001,9527**

```java
如果启动GateWay报错
  	可能是GateWay模块引入了web和监控的starter依赖,需要移除
```

访问:

​		localhost:9527/payment/get/1

![](.\图片\gateway的19.png)







#### 6,GateWay的网关配置,

​		**GateWay的网关配置,除了支持配置文件,还支持硬编码方式**

#### 7使用硬编码配置GateWay:

##### 创建配置类:

![](.\图片\gateway的20.png)

#### 8,然后重启服务即可

 



### 重构:

上面的配置虽然首先了网关,但是是在配置文件中写死了要路由的地址

现在需要修改,不指定地址,而是根据微服务名字进行路由,我们可以在注册中心获取某组微服务的地址

需要:

​		1个eureka,2个pay模块

#### 修改GateWay模块的配置文件:

![](.\图片\gateway的21.png)



#### 然后就可以启动微服务.测试







### Pridicate断言:

![](.\图片\gateway的24.png)

**我们之前在配置文件中配置了断言:**

![](.\图片\gateway的22.png)

**这个断言表示,如果外部访问路径是指定路径,就路由到指定微服务上**

可以看到,这里有一个Path,这个是断言的一种,==断言的类型==:

![](.\图片\gateway的23.png)



```java
After:
		可以指定,只有在指定时间后,才可以路由到指定微服务
```

![](.\图片\gateway的26.png)

​				这里表示,只有在==2020年的2月21的15点51分37秒==之后,访问==才可以路由==

​				在此之前的访问,都会报404

如何获取当前时区?**

![](.\图片\gateway的25.png)



```java
before:
		与after类似,他说在指定时间之前的才可以访问
between:
		需要指定两个时间,在他们之间的时间才可以访问
```

![](.\图片\gateway的27.png)





```java
cookie:
		只有包含某些指定cookie(key,value),的请求才可以路由
```

![](.\图片\gateway的28.png)

![](.\图片\gateway的29.png)



```java
Header:
		只有包含指定请求头的请求,才可以路由
```

![](.\图片\gateway的31.png)

![](.\图片\gateway的32.png)

测试:
![](.\图片\gateway的33.png)







```java
host:
		只有指定主机的才可以访问,
		比如我们当前的网站的域名是www.aa.com
    那么这里就可以设置,只有用户是www.aa.com的请求,才进行路由
```

![](.\图片\gateway的34.png)

![gateway的34](.\图片\gateway的35.png)

![](.\图片\gateway的36.png)

![](.\图片\gateway的37.png)

可以看到,如果带了域名访问,就可以,但是直接访问ip地址.就报错了







```java
method:
		只有指定请求才可以路由,比如get请求...
```

![](.\图片\gateway的38.png)

```java
path:
		只有访问指定路径,才进行路由
     比如访问,/abc才路由
```

![](.\图片\gateway的39.png)



```java
Query:
		必须带有请求参数才可以访问
```

![](.\图片\gateway的40.png)







### Filter过滤器:

![](.\图片\gateway的41.png)



#### 生命周期:

**在请求进入路由之前,和处理请求完成,再次到达路由之前**



#### 种类:

![](.\图片\gateway的42.png)

GateWayFilter,单一的过滤器

**与断言类似,比如闲置,请求头,只有特定的请求头才放行,反之就过滤**:

![](.\图片\gateway的43.png)

GlobalFilter,全局过滤器:





#### **自定义过滤器:**

实现两个接口

![](.\图片\gateway的44.png)

​	**然后启动服务,即可,因为过滤器通过@COmponet已经加入到容器了**

![](.\图片\gateway的46.png)

![](.\图片\gateway的45.png)



























