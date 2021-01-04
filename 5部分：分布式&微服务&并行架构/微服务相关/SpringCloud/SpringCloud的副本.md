

# 6,服务配置:

## Spring Config分布式配置中心:

==微服务面临的问题==

```java
可以看到,每个微服务都需要一个配置文件,并且,如果有几个微服务都需要连接数据库
		那么就需要配4次数据库相关配置,并且当数据库发生改动,那么需要同时修改4个微服务的配置文件才可以
```

所以有了springconfig配置中心

![](.\图片\springconfig的1.png)

![](.\图片\springconfig的2.png)

![](.\图片\springconfig的3.png)

![](.\图片\springconfig的4.png)





### 使用配置中心:

#### 0,使用github作为配置中心的仓库:

**初始化git环境:**

![](.\图片\springconfig的5.png)



#### 1,新建config模块:

名字:   cloud-config-3344

#### 2,pom

#### 3,配置文件

![](.\图片\springconfig的6.png)

#### 4,主启动类

![](.\图片\springconfig的7.png)

#### 5,修改hosts:

![](.\图片\springconfig的8.png)

#### 6,配置完成

测试,3344是否可以从github上获取配置

启动3344	(要先启动eureka)

![](.\图片\springconfig的9.png)

它实际上就是,读取到配置文件中的GitHub的地址,然后拼接上/master/config-dev.yml

#### 7,读取配置文件的规则:

![](.\图片\springconfig的10.png)



==2,==

![](.\图片\springconfig的11.png)

**这里默认会读取master分支,因为我们配置文件中配置了**

![](.\图片\springconfig的12.png)

==3==

![](.\图片\springconfig的13.png)

注意,这个方式读取到的配置是==json格式==的

**所有规则:**

![](.\图片\springconfig的14.png)



### 2,创建配置中心客户端:

#### 1,创建config客户端项目

名字: 	cloud-config-client-3355

#### 2,pom

#### 3,配置文件

注意这个配置文件就不是application.yml

​			而是bootstrap.yml

这个配置文件的作用是,先到配置中心加载配置,然后加载到application.yml中

![](.\图片\springconfig的15.png)

![](.\图片\springconfig的16.png)



#### 4,主启动类:

![](.\图片\springconfig的17.png)

#### 5,controller类

就是上面提到的,以rest风格将配置对外暴露

![](.\图片\springconfig的18.png)

![](.\图片\springconfig的19.png)

**如果客户端运行正常,就会读取到github上配置文件的,config.info下的配置**

#### 6,测试:

启动3344,3355

​	访问3355的  /configInfo

![](.\图片\springconfig的21.png)





#### 7,问题::

```java
上面3355确实获取到了配置文件,但是如果此时配置文件修改了,3355是获取不到的
		3344可以实时获取到最新配置文件,但是3355却获取不到
  	除非重启服务
```

#### **8,实现动态刷新:**

##### 1,修改3355,添加一个pom依赖:

![](.\图片\springconfig的22.png)

##### 2,修改配置文件,添加一个配置:

![](.\图片\springconfig的23.png)

##### 3,修改controller:

![](.\图片\springconfig的24.png)



##### 4,此时重启服务

**此时3355还不可以动态获取**

因为此时,还需要==外部==发送post请求通知3355

![](.\图片\springconfig的25.png)

**此时在刷新3355,发现可以获取到最新的配置文件了,这就实现了动态获取配置文件,因为3355并没有重启**



具体流程就是:

​			我们启动好服务后

​			运维人员,修改了配置文件,然后发送一个post请求通知3355

​			3355就可以获取最新配置文件





**问题:**

​		如果有多个客户端怎么办(3355,3356,3357.....)

​						虽然可以使用shell脚本,循环刷新

​		但是,可不可以使用广播,一次通知??

​					这些springconfig做不到,需要使用springcloud Bus消息总线







# 消息总线:

## SpringCloud Bus:

![](.\图片\springconfig的26.png)







![](.\图片\springconfig的27.png)

![](.\图片\springconfig的31.png)

注意,这里年张图片,就代表两种广播方式

​			图1:		**它是Bus直接通知给其中一个客户端,由这个客户端开始蔓延,传播给其他所有客户端**

​			图2:		它**是通知给配置中心的服务端,有服务端广播给所有客户端**





**为什么被称为总线?**

![](.\图片\springconfig的28.png)

```java
就是通过消息队列达到广播的效果
  		我们要广播每个消息时,主要放到某个topic中,所有监听的节点都可以获取到
```





### 使用Bus:

#### 1,配置rabbitmq环境:

![](.\图片\springconfig的29.png)



#### **2,之前只有一个配置中心客户端,这里在创建一个**

​		==**复制3355即可,创建为3366**==

![](.\图片\springconfig的30.png)

全部复制3355的即可



#### 2,使用Bus实现全局广播

**Bus广播有两种方式:**

​		==就是上面两个图片的两种方式==

![](.\图片\springconfig的32.png)

**这两种方式,第二种跟合适,因为:**

​			==第一种的缺点:==

![](.\图片\springconfig的33.png)





#### **配置第二种方式:**

##### **1,配置3344(配置中心服务端):**

###### 1,修改配置文件:

![](.\图片\Bus的1.png)

###### 2,添加pom

**springboot的监控组件,和消息总线**

![](.\图片\Bus的3.png)

![](.\图片\Bus的2.png)



##### 2,修改3355(配置中心的客户端)

###### 1,pom:

![](.\图片\Bus的3.png)

![Bus的2](.\图片\Bus的2.png)



###### 2,配置文件:

==注意配置文件的名字,要改为bootstrap.yml==

![](.\图片\Bus的5.png)

![image-20200415102708661](.\图片\Bus的4)





##### 3,修改3366(也是配置中心的客户端)

​			修改与3355是一模一样的







##### 4,测试

启动7001,3344,3355,3366

此时修改GitHub上的配置文件

==此时只需要刷新3344,即可让3355,3366动态获取最新的配置文件==

![](.\图片\Bus的6.png)



其原理就是:

![](.\图片\Bus的7.png)

**所有客户端都监听了一个rabbitMq的topic,我们将信息放入这个topic,所有客户端都可以送到,从而实时更新**









#### 配置定点通知

​		就是只通知部分服务,比如只通知3355,不通知3366

![](.\图片\Bus的8.png)

![Bus的8](.\图片\Bus的9.png)



**只通知3355**

![](.\图片\Bus的11.png)

​	![](.\图片\Bus的12.png)

**可以看到,实际上就是通过==微服务的名称+端口号==进行指定**



















# 8,消息驱动:

## Spring Cloud Stream:

```java
现在一个很项目可能分为三部分:
			前端--->后端---->大数据
			而后端开发使用消息中间件,可能会使用RabbitMq
			而大数据开发,一般都是使用Kafka,
			那么一个项目中有多个消息中间件,对于程序员,因为人员都不友好
```

而Spring Cloud Stream就类似jpa,屏蔽底层消息中间件的差异,程序员主要操作Spring Cloud Stream即可

​			不需要管底层是kafka还是rabbitMq

![](.\图片\SpringCloudStream的1.png)

### ==什么是Spring Cloud Stream==

![](.\图片\SpringCloudStream的2.png)





![](.\图片\SpringCloudStream的3.png)

![](.\图片\SpringCloudStream的4.png)

![](.\图片\SpringCloudStream的5.png)





### ==**Spring Cloud Stream是怎么屏蔽底层差异的?**==

![](.\图片\SpringCloudStream的6.png)





**绑定器:**

![](.\图片\SpringCloudStream的7.png)

![](.\图片\SpringCloudStream的8.png)

![](.\图片\SpringCloudStream的9.png)





### **Spring Cloud Streamd 通信模式:**

![](.\图片\SpringCloudStream的10.png)![](.\图片\SpringCloudStream的11.png)





### Spring Cloud Stream的业务流程:

![](.\图片\SpringCloudStream的12.png)

![](.\图片\SpringCloudStream的14.png)

![](.\图片\SpringCloudStream的13.png)

```java
类似flume中的channel,source,sink  估计是借鉴(抄袭)的
  	source用于获取数据(要发送到mq的数据)
  	channel类似SpringCloudStream中的中间件,用于存放source接收到的数据,或者是存放binder拉取的数据	
```







### 常用注解和api:

![](.\图片\SpringCloudStream的15.png)





### 使用SpringCloudStream:

需要创建三个项目,一个生产者,两个消费者

![](.\图片\SpringCloudStream的16.png)

### 1,创建生产者

#### 1,pom

#### 2,配置文件

![image-20200415114816133](.\图片\SpringCloudStream的17)

![](.\图片\SpringCloudStream的18.png)

#### 3,主启动类

![](.\图片\SpringCloudStream的19.png)

#### 4,service和实现类

service定义发送消息

![](.\图片\SpringCloudStream的20.png)

![](.\图片\SpringCloudStream的21.png)

**这里,就会调用send方法,将消息发送给channel,**

​				**然后channel将消费发送给binder,然后发送到rabbitmq中**

#### 5,controller

![](.\图片\SpringCloudStream的22.png)

#### 6,可以测试

**启动rabbitmq**

**启动7001,8801**

​		确定8801后,会在rabbitmq中创建一个Exchange,就是我们配置文件中配置的exchange

**访问8801的/sendMessage**







### 创建消费者:

#### 1,pom文件

#### 2,配置文件

==**这里排版一点问题**==

**==input==就表示,当前服务是一个消费者,需要消费消息,下面就是指定消费哪个Exchange中的消息**

![](.\图片\SpringCloudStream的23.png)

![](.\图片\SpringCloudStream的24.png)

#### 3,主启动类

![](.\图片\SpringCloudStream的25.png)

#### 4,业务类(消费数据)

![](.\图片\SpringCloudStream的26.png)

**生产者发送消息时,使用send方法发送,send方法发送的是一个个Message,里面封装了数据**

#### 5,测试:

启动7001.8801.8802

**此时使用生产者生产消息**

![](.\图片\SpringCloudStream的27.png)

==可以看到,消费者已经接收到消息了==





### 创建消费者2

创建8803,

==与8802创建一模一样,就不写了==

**创建8803主要是为了演示重复消费等问题**

...

....

...





### ==重复消费问题:==

此时启动7001.8801.8802.8803

此时生产者生产一条消息

但是此时查询消费者,发现8802,8803==都消费到了同一条数据==

![](.\图片\SpringCloudStream的28.png)

![](.\图片\SpringCloudStream的29.png)

#### 1,自定义分组

**修改8802,8803的配置文件**

![](.\图片\SpringCloudStream的30.png)

![](.\图片\SpringCloudStream的31 - 副本.png)

**现在将8802,8803都分到了A组**

然后去重启02,03

**然后此时生产者生产两条消息**

![](.\图片\SpringCloudStream的33.png)

![](.\图片\SpringCloudStream的34.png)

![](.\图片\SpringCloudStream的35.png)

**可以看到,每人只消费了一条消息,并且没有重复消费**







### 持久化问题:

就是当服务挂了,怎么消费没有消费的数据??



这里,先将8802移除A组,

​		然后将02,03服务关闭

此时生产者开启,发送3条消息

​		此时重启02,03

​		可以看到,当02退出A组后,它就获取不到在它宕机的时间段内的数据

​		但是03重启后,直接获取到了宕机期间它没有消费的数据,并且消费了

总结:
		也就是,当我们没有配置分组时,会出现消息漏消费的问题

​		而配置分组后,我们可以自动获取未消费的数据















# 9,链路追踪:

## Spring Cloud Sleuth

**sleuth要解决的问题:**

![](.\图片\sleuth的1.png)

**而来sleuth就是用于追踪每个请求的整体链路**

![](.\图片\sleuth的2.png)



### 使用sleuth:

#### 1,安装zipkin:

![](.\图片\sleuth的3.png)

**运行jar包**

​			java -jar xxxx.jar

**然后就可以访问web界面,  默认zipkin监听的端口是9411**

​			localhost:9411/zipkin/

![](.\图片\sleuth的4.png)



**一条链路完整图片:**

![](.\图片\sleuth的5.png)

**精简版:**

![](.\图片\sleuth的6.png)

**可以看到,类似链表的形式**







#### 2,使用sleuth:

不需要额外创建项目,使用之前的8001和order的80即可



##### 1,修改8001

**引入pom:**

![](.\图片\sleuth的7.png)

这个包虽然叫zipkin但是,里面包含了zpikin与sleuth

**修改配置文件:**

![](.\图片\sleuth的8.png)



##### 2,修改80

**添加pom**

与上面是一样的



**添加配置**:

与上面也是一样的







##### 3,测试:

启动7001.8001,80,9411

![](.\图片\sleuth的9.png)











# 10,Spring CloudAlibaba:

**之所以有Spring CloudAlibaba,是因为Spring Cloud Netflix项目进入维护模式**

​		**也就是,就不是不更新了,不会开发新组件了**

​		**所以,某些组件都有代替版了,比如Ribbon由Loadbalancer代替,等等**

==支持的功能==

![](.\图片\Alibaba的1.png)

几乎可以将之前的Spring Cloud代替







==具体组件==:
![](.\图片\Alibaba的2.png)











## Nacos:

**服务注册和配置中心的组合**

​			Nacos=erueka+config+bus

### 安装Nacos:

需要java8  和 Mavne

**1,到github上下载安装包**

​		解压安装包

**2,启动Nacos**

​		在bin下,进入cod

​		./startup.cmd

**3,访问Nacos**

​		Nacos默认监听8848

​		localhost:8848/nacos

​		账号密码:默认都是nacos





### 使用Nacos:

新建pay模块

​		**现在不需要额外的服务注册模块了,Nacos单独启动了**

名字: cloudalibaba-pay-9001

#### 1,pom

父项目管理alibaba的依赖:

![](.\图片\Alibaba的4.png)

![](.\图片\Alibaba的3.png)

==9001的pom==:

​			另外一个文件.....

#### 2,配置文件

![](.\图片\Alibaba的5.png)

#### 3,启动类

![](.\图片\Alibaba的6.png)

#### 4,controller:

![](.\图片\Alibaba的7.png)

#### 5,测试

启动9001

然后查看Nacos的web界面,可以看到9001已经注册成功



### 





### 创建其他Pay模块

​		额外在创建9002,9003

​		直接复制上面的即可

### 创建order模块

名字:  cloudalibaba-order-83

#### 1,pom

**为什么Nacos支持负载均衡?**

​				Nacos直接集成了Ribon,所以有负载均衡

#### 2,配置文件

![](.\图片\Alibaba的8.png)

**这个server-url的作用是,我们在controller,需要使用RestTempalte远程调用9001,**

​		**这里是指定9001的地址**



#### 3,主启动类

![](.\图片\Alibaba的9.png)

#### 4,编写配置类

​	==因为Naocs要使用Ribbon进行负载均衡,那么就需要使用RestTemplate==

![](.\图片\Alibaba的10.png)

#### 5,controller:

![](.\图片\Alibaba的11.png)



#### 6,测试

启动83,访问9001,9002,可以看到,实现了负载均衡





### Nacos与其他服务注册的对比

Nacos它既可以支持CP,也可以支持AP,可以切换

![](.\图片\Alibaba的12.png)

![](.\图片\Alibaba的13.png)

==下面这个curl命令,就是切换模式==





### 使用Nacos作为配置中心:

![](.\图片\Alibaba的14.png)

**==需要创建配置中心的客户端模块==**

cloudalibaba-Nacos-config-client-3377

#### 1,pom

#### 2,配置文件

这里需要配置两个配置文件,application.ymk和bootstarp.yml

​			主要是为了可以与spring clodu config无缝迁移

![](.\图片\Alibaba的15.png)

```java
可以看到
```

![](.\图片\Alibaba的16.png)



#### 3,主启动类

![](.\图片\Alibaba的18.png)

#### 4,controller

![](.\图片\Alibaba的17.png)

```java
可以看到,这里也添加了@RefreshScope
  		之前在Config配置中心,也是添加这个注解实现动态刷新的	
  
```

![](.\图片\Alibaba的19.png)

#### 5,在Nacos添加配置信息:

==**Nacos的配置规则:**==

![](.\图片\Alibaba的20.png)

**配置规则,就是我们在客户端如何指定读取配置文件,配置文件的命名的规则**

默认的命名方式:

![](.\图片\Alibaba的21.png)

```java
prefix:
		默认就是当前服务的服务名称
 		也可以通过spring.cloud.necos.config.prefix配置
spring.profile.active:
		就是我们在application.yml中指定的,当前是开发环境还是测试等环境
    这个可以不配置,如果不配置,那么前面的 -  也会没有
file-extension
     就是当前文件的格式(后缀),目前只支持yml和properties
```

![](.\图片\Alibaba的24.png)

![](.\图片\Alibaba的25.png)

==在web UI上创建配置文件:==

![](.\图片\Alibaba的22.png)

![](.\图片\Alibaba的23.png)

注意,DataId就是配置文件名字:

​		名字一定要按照上面的==规则==命名,否则客户端会读取不到配置文件

#### 6,测试

重启3377客户端

访问3377

![](.\图片\Alibaba的26.png)

**拿到了配置文件中的值**



#### 7,注意默认就开启了自动刷新

此时我们修改了配置文件

客户端是可以立即更新的

​			因为Nacos支持Bus总线,会自动发送命令更新所有客户端





### Nacos配置中心之分类配置:

![](.\图片\Alibaba的27.png)





![](.\图片\Alibaba的28.png)

![](.\图片\Alibaba的29.png)

NameSpace默认有一个:public名称空间

这三个类似java的: 包名 + 类名 + 方法名

![](.\图片\Alibaba的30.png)



![](.\图片\Alibaba的31.png)





#### 1,配置不同DataId:

![](.\图片\Alibaba的32.png)

![](.\图片\Alibaba的33.png)



​	==通过配置文件,实现多环境的读取:==

![](.\图片\Alibaba的34.png)

```java
此时,改为dev,就会读取dev的配置文件,改为test,就会读取test的配置文件
```





#### 2,配置不同的GroupID:

直接在新建配置文件时指定组

![](.\图片\Alibaba的35.png)

![](.\图片\Alibaba的36.png)



==在客户端配置,使用指定组的配置文件:==

![](.\图片\Alibaba的37.png)

**这两个配置文件都要修改**

![](.\图片\Alibaba的38.png)

​	

重启服务,即可







#### 配置不同的namespace:

![](.\图片\Alibaba的39.png)

![](.\图片\Alibaba的42.png)

==客户端配置使用不同名称空间:==

![](.\图片\Alibaba的41.png)

**要通过命名空间id指定**

OK,测试









### Nacos集群和持久化配置:

![](.\图片\Alibaba的45.png)

Nacos默认有自带嵌入式数据库,derby,但是如果做集群模式的话,就不能使用自己的数据库

​			不然每个节点一个数据库,那么数据就不统一了,需要使用外部的mysql

![](.\图片\Alibaba的43.png)



#### 1,单机版,切换mysql数据库:

​					**将nacos切换到使用我们自己的mysql数据库:**

**1,nacos默认自带了一个sql文件,在nacos安装目录下**

​			将它放到我们的mysql执行

**2,修改Nacos安装目录下的安排application.properties,添加:**

![](.\图片\Alibaba的46.png)





**3,此时可以重启nacos,那么就会改为使用我们自己的mysql**







#### Linux上配置Nacos集群+Mysql数据库

==官方架构图:==

![](.\图片\Alibaba的45.png)

**需要一个Nginx作为VIP**



1,下载安装Nacos的Linux版安装包

2,进入安装目录,现在执行自带的sql文件

​			进入mysql,执行sql文件

3.修改配置文件,切换为我们的mysql

​			就是上面windos版要修改的几个属性

4,修改cluster.conf,指定哪几个节点是Nacos集群

​			这里使用3333,4444,5555作为三个Nacos节点监听的端口

![](.\图片\Alibaba的47.png)

5,我们这里就不配置在不同节点上了,就放在一个节点上

​			既然要在一个节点上启动不同Nacos实例,就要修改startup.sh,使其根据不同端口启动不同Nacos实例

![](.\图片\Alibaba的48.png)

![](.\图片\Alibaba的49.png)

可以看到,这个脚本就是通过jvm启动nacos

​		所以我们最后修改的就是,nohup java -Dserver.port=3344





6,配置Nginx:

​			![](.\图片\Alibaba的50.png)

7,启动Nacos:
			./startup.sh -p 3333

​			./startup.sh -p 4444

​			./startup.sh -p 5555

7,启动nginx

8,测试:

​		访问192.168.159.121:1111

​		如果可以进入nacos的web界面,就证明安装成功了





9,将微服务注册到Nacos集群:
![](.\图片\Alibaba的51.png)

10,进入Nacos的web界面

​		可以看到,已经注册成功

![](.\图片\Alibaba的52.png)











## Sentinel:

实现熔断与限流,就是Hystrix

![](.\图片\Alibaba的53.png)

​	![](.\图片\Alibaba的54.png)

### ==使用sentinel:==



1,下载sentinel的jar包

2,运行sentinel

​		由于是一个jar包,所以可以直接java -jar运行	

​		注意,默认sentinel占用8080端口

3,访问sentinel

​		localhost:8080





### 微服务整合sentinel:

##### 1,启动Nacos

##### 2,新建一个项目,8401,主要用于配置sentinel

1.  pom

2.   配置文件

    ![](.\图片\Alibaba的55.png)

3.   主启动类

    ![](.\图片\Alibaba的56.png)

4.   controller\

    ![](.\图片\sentinel的1.png)

5.   到这里就可以启动8401

    ​	此时我们到sentinel中查看,发现并8401的任何信息

    ​	是因为,sentinel是懒加载,需要我们执行一次访问,才会有信息

    ​	访问localhost/8401/testA

    ![](.\图片\sentinel的2.png)

6.   可以看到.已经开始监听了

​    



### sentinel的流控规则

流量限制控制规则

![](.\图片\sentinel的7.png)

![](.\图片\sentinel的3.png)



![](.\图片\sentinel的4.png)

==流控模式==:

1.   直接快速失败

    ![](.\图片\sentinel的9.png)

    ![](.\图片\sentinel的5.png)

       ==直接失败的效果:==

    ![](.\图片\sentinel的6.png)

2.  线程数:

    ​		![](.\图片\sentinel的8.png)

    ​	![](.\图片\sentinel的10.png)

    ```
    比如a请求过来,处理很慢,在一直处理,此时b请求又过来了
    		此时因为a占用一个线程,此时要处理b请求就只有额外开启一个线程
    		那么就会报错
    ```

    ![](.\图片\sentinel的11.png)

    

3.   关联:

     ![](.\图片\sentinel的12.png)

     ==应用场景:  比如**支付接口**达到阈值,就要限流下**订单的接口**,防止一直有订单==

     ![](.\图片\sentinel的13.png)

     **当testA达到阈值,qps大于1,就让testB之后的请求直接失败**

     可以使用postman压测

​    

4.   链路:
     多个请求调用同一个微服务

5.   预热Warm up:

    ​	 ![](.\图片\sentinel的14.png)

      ![](.\图片\sentinel的15.png)

     ![](.\图片\sentinel的16.png)

     ==应用场景==

     ![](.\图片\sentinel的17.png)

6.   排队等待:

    ![](.\图片\sentinel的18.png)

    ![](.\图片\sentinel的19.png)









### 降级规则:

**就是熔断降级**

![](.\图片\sentinel的21.png)

![](.\图片\sentinel的20.png)





![](.\图片\sentinel的22.png)

![](.\图片\sentinel的23.png)



#### 1,RT配置:

新增一个请求方法用于测试

![](.\图片\sentinel的24.png)

==配置RT:==

​				这里配置的PT,默认是秒级的平均响应时间

![](.\图片\sentinel的25.png)

默认计算平均时间是: 1秒类进入5个请求,并且响应的平均值超过阈值(这里的200ms),就报错]

​			1秒5请求是Sentinel默认设置的

==测试==

![](.\图片\sentinel的27.png)

![](.\图片\sentinel的26.png)

**默认熔断后.就直接抛出异常**







#### 2,异常比例:

![](.\图片\sentinel的28.png)

修改请求方法

![](.\图片\sentinel的29.png)

配置:

![](.\图片\sentinel的31.png)



==如果没触发熔断,这正常抛出异常==:

![](.\图片\sentinel的32.png)

==触发熔断==:

![](.\图片\sentinel的33.png)









#### 3, 异常数:

![](.\图片\sentinel的34.png)

![](.\图片\sentinel的35.png)

一分钟之内,有5个请求发送异常,进入熔断









### 热点规则:

![](.\图片\sentinel的36.png)

​	![](.\图片\sentinel的37.png)

比如:

​			localhost:8080/aa?name=aa

​			localhost:8080/aa?name=b'b

​			加入两个请求中,带有参数aa的请求访问频次非常高,我们就现在name==aa的请求,但是bb的不限制



==如何自定义降级方法,而不是默认的抛出异常?==

![](.\图片\sentinel的38.png)

**使用@SentinelResource直接实现降级方法,它等同Hystrix的@HystrixCommand**

![](.\图片\sentinel的39.png)



==定义热点规则:==

 ![](.\图片\sentinel的40.png)

![](.\图片\sentinel的42.png)

**此时我们访问/testHotkey并且带上才是p1**

​			如果qps大于1,就会触发我们定义的降级方法

![](.\图片\sentinel的41.png)

**但是我们的参数是P2,就没有问题**

![](.\图片\sentinel的44.png)



只有带了p1,才可能会触发热点限流

![](.\图片\sentinel的43.png)





#### 2,设置热点规则中的其他选项:

![](.\图片\sentinel的45.png)

**需求:**

![](.\图片\sentinel的46.png)



![](.\图片\sentinel的47.png)

==测试==

![](.\图片\sentinel的48.png)

![](.\图片\sentinel的49.png)



**注意:**

参数类型只支持,8种基本类型+String类





==注意:==

如果我们程序出现异常,是不会走blockHander的降级方法的,因为这个方法只配置了热点规则,没有配置限流规则

我们这里配置的降级方法是sentinel针对热点规则配置的

只有触发热点规则才会降级

![](.\图片\sentinel的50.png)









### 3,系统规则:

系统自适应限流:
			从整体维度对应用入口进行限流

对整体限流,比如设置qps到达100,这里限流会限制整个系统不可以

*![](.\图片\sentinel的51.png)*



![](.\图片\sentinel的52.png)

==测试==:
![](.\图片\sentinel的53.png)

![](.\图片\sentinel的54.png)











### @SentinelResource注解:

**用于配置降级等功能**

1,环境搭建

1.  为8401添加依赖

    添加我们自己的commone包的依赖

    ![](.\图片\sentinel的55.png)

2.   额外创建一个controller类

    ​	 ![](.\图片\sentinel的56.png)

     

3.   配置限流

    **注意,我们这里配置规则,资源名指定的是@SentinelResource注解value的值,**

    **这样也是可以的,也就是不一定要指定访问路径**

    ![](.\图片\sentinel的57.png)

4.   测试.

    可以看到已经进入降级方法了

    ![](.\图片\sentinel的58.png)

5.   ==此时我们关闭8401服务==

    可以看到,这些定义的规则是临时的,关闭服务,规则就没有了

    ![](.\图片\sentinel的59.png)



**可以看到,上面配置的降级方法,又出现Hystrix遇到的问题了**

​			降级方法与业务方法耦合

​			每个业务方法都需要对应一个降级方法

#### 自定义限流处理逻辑:

1.  ==单独创建一个类,用于处理限流==

    ![](.\图片\sentinel的的1.png)

2.  ==在controller中,指定使用自定义类中的方法作为降级方法==

    ![](.\图片\sentinel的的2.png)

3.   ==Sentinel中定义流控规则==:

     这里资源名,是以url指定,也可以使用@SentinelResource注解value的值指定

     ![](.\图片\sentinel的的5.png)

     

4.  ==测试==:

    ![](.\图片\sentinel的的3.png)

5.  ==整体==:

    ![](.\图片\sentinel的的4.png)

6.   





#### @SentinelResource注解的其他属性:



![](.\图片\sentinel的的7.png)

![](.\图片\sentinel的的6.png)













### 服务熔断:

1.  **启动nacos和sentinel**

2.   **新建两个pay模块  9003和9004**

    1.   pom

    2.   配置文件

        ![](.\图片\sentinel的的8.png)*

    3.   主启动类 

        ```java
        @SpringBootApplication
        @EnableDiscoveryClient
        public class PaymentMain9003 {
        
            public static void main(String[] args) {
                SpringApplication.run(PaymentMain9003.class,args);
            }
        }
         
        
        ```

    4.   controller

        ![](.\图片\sentinel的的9.png)

         **然后启动9003.9004**

3.   **新建一个order-84消费者模块:**

    1.   pom

        与上面的pay一模一样

    2.   配置文件

        ![](.\图片\sentinel的的10.png)

    3.   主启动类

        ![](.\图片\sentinel的的11.png)

    4.  配置类

        ![](.\图片\sentinel的的12.png)

    5.   controller

        ![](.\图片\sentinel的的13.png)

        

    6.   **==为业务方法添加fallback来指定降级方法==**:

        ![](.\图片\sentinel的的14.png)

        ​	==重启order==

        测试:

        ![](.\图片\sentinel的的15.png)

         

         ==所以,fallback是用于管理异常的,当业务方法发生异常,可以降级到指定方法==

        ​			注意,我们这里==并没有使用sentinel配置任何规则==,但是却降级成功,就是因为

        ​			fallback是用于管理异常的,当业务方法发生异常,可以降级到指定方法==

        

    7.   **==为业务方法添加blockHandler,看看是什么效果==**

         ![](.\图片\sentinel的的16.png)

         **重启84,访问业务方法:**

        ![](.\图片\sentinel的的17.png)

         可以看到.,直接报错了,并没有降级

        ​				也就是说,blockHandler==只对sentienl定义的规则降级==

         

    8.   **==如果fallback和blockHandler都配置呢?==**]

         ![](.\图片\sentinel的的18.png)

         **设置qps规则,阈值1**

         ![](.\图片\sentinel的的19.png)

         ==测试:==

        ![](.\图片\sentinel的的20.png)

         

         可以看到,当两个都同时生效时,==blockhandler优先生效==

    9.  **==@SentinelResource还有一个属性,exceptionsToIgnore==**

         ![](.\图片\sentinel的的21.png)

         **exceptionsToIgnore指定一个异常类,**

        ​					**表示如果当前方法抛出的是指定的异常,不降级,直接对用户抛出异常**

         ![](.\图片\sentinel的的22.png)

         

         

    



### sentinel整合ribbon+openFeign+fallback



1.  修改84模块,使其支持feign

    1.  pom

        ![](.\图片\sentinel的的23.png)

    2.  配置文件

        ![](.\图片\sentinel的的24.png)

    3.  主启动类,也要修改

        ![](.\图片\sentinel的的25.png)

    4.  创建远程调用pay模块的接口

        ![](.\图片\sentinel的的26.png)

    5.  创建这个接口的实现类,用于降级

        ![](.\图片\sentinel的的27.png)

    6.   再次修改接口,指定降级类

        ![](.\图片\sentinel的的28.png)

    7.   controller添加远程调用

        ![](.\图片\sentinel的的29.png)

    8.  测试

        启动9003,84

    9.   测试,如果关闭9003.看看84会不会降级

        ![](.\图片\sentinel的的30.png)

        **可以看到,正常降级了**

        

**熔断框架比较**

![](.\图片\sentinel的的31.png)









### sentinel持久化规则

默认规则是临时存储的,重启sentinel就会消失

![](.\图片\sentinel的的32.png)

**这里以之前的8401为案例进行修改:**

1.  修改8401的pom

    ```xml
    添加:
    <!-- SpringCloud ailibaba sentinel-datasource-nacos 持久化需要用到-->
    <dependency>
        <groupId>com.alibaba.csp</groupId>
        <artifactId>sentinel-datasource-nacos</artifactId>
    </dependency>
     
    ```

    

2.   修改配置文件:

    添加:

     ![](.\图片\sentinel的的33.png)

     **实际上就是指定,我们的规则要保证在哪个名称空间的哪个分组下**

     			这里没有指定namespace, 但是是可以指定的

    ​			**注意,这里的dataid要与8401的服务名一致**

3.   **在nacos中创建一个配置文件,dataId就是上面配置文件中指定的**

     ![](.\图片\sentinel的的34.png)

     ==json中,这些属性的含义:==

    ​	![](.\图片\sentinel的的35.png)

     

    

4.   启动8401:

     ![](.\图片\sentinel的的36.png)

     可以看到,直接读取到了规则

5.   关闭8401

    ![](.\图片\sentinel的的37.png)

6.   此时重启8401,如果sentinel又可以正常读取到规则,那么证明持久化成功

    可以看到,又重新出现了

     ![](.\图片\sentinel的的38.png)

    























## Seata:

是一个分布式事务的解决方案,

**分布式事务中的一些概念,也是seata中的概念:**

​		![](.\图片\seala.png)

![](.\图片\seala的2.png)

![](.\图片\seala的3.png)





### seata安装:

1.  **下载安装seata的安装包**

2.  **修改file.conf**

     ![](.\图片\seala的4.png)

     ![](.\图片\seala的5.png)

     ![](.\图片\seala的6.png)

3.   **mysql建库建表**

     1,上面指定了数据库为seata,所以创建一个数据库名为seata

     2,建表,在seata的安装目录下有一个db_store.sql,运行即可

4.   **继续修改配置文件,修改registry.conf**

    配置seata作为微服务,指定注册中心

    ![](.\图片\seala的7.png)

5.   启动

    先启动nacos

    在启动seata-server(运行安装目录下的,seata-server.bat)

    

**业务说明**

![](.\图片\seala的8.png)

下单--->库存--->账号余额



1.  创建三个数据库

    ![](.\图片\seala的9.png)

2.   创建对应的表

    ![](.\图片\seala的10.png)

3.   创建回滚日志表,方便查看

    ![](.\图片\seala的11.png)

    **注意==每个库都要执行一次==这个sql,生成回滚日志表**

4.   ==每个业务都创建一个微服务,也就是要有三个微服务,订单,库存,账号==

    ​     ==订单==,seta-order-2001

    1.   pom

    2.   配置文件

        ```yaml
        server:
          port: 2001
        
        spring:
          application:
            name: seata-order-service
          cloud:
            alibaba:
              seata:
                # 自定义事务组名称需要与seata-server中的对应,我们之前在seata的配置文件中配置的名字
                tx-service-group: fsp_tx_group
            nacos:
              discovery:
                server-addr: 127.0.0.1:8848
          datasource:
            # 当前数据源操作类型
            type: com.alibaba.druid.pool.DruidDataSource
            # mysql驱动类
            driver-class-name: com.mysql.cj.jdbc.Driver
            url: jdbc:mysql://localhost:3306/seata_order?useUnicode=true&characterEncoding=UTF-8&useSSL=false&serverTimezone=GMT%2B8
            username: root
            password: root
        feign:
          hystrix:
            enabled: false
        logging:
          level:
            io:
              seata: info
        
        mybatis:
          mapperLocations: classpath*:mapper/*.xml
        
         
         
        
        ```

        还要额外创建其他配置文件,创建一个file.conf:

         ```.conf
        transport {
          # tcp udt unix-domain-socket
          type = "TCP"
          #NIO NATIVE
          server = "NIO"
          #enable heartbeat
          heartbeat = true
          #thread factory for netty
          thread-factory {
            boss-thread-prefix = "NettyBoss"
            worker-thread-prefix = "NettyServerNIOWorker"
            server-executor-thread-prefix = "NettyServerBizHandler"
            share-boss-worker = false
            client-selector-thread-prefix = "NettyClientSelector"
            client-selector-thread-size = 1
            client-worker-thread-prefix = "NettyClientWorkerThread"
            # netty boss thread size,will not be used for UDT
            boss-thread-size = 1
            #auto default pin or 8
            worker-thread-size = 8
          }
          shutdown {
            # when destroy server, wait seconds
            wait = 3
          }
          serialization = "seata"
          compressor = "none"
        }
        service {
          #vgroup->rgroup
          # 事务组名称
          vgroup_mapping.fsp_tx_group = "default"
          #only support single node
          default.grouplist = "127.0.0.1:8091"
          #degrade current not support
          enableDegrade = false
          #disable
          disable = false
          #unit ms,s,m,h,d represents milliseconds, seconds, minutes, hours, days, default permanent
          max.commit.retry.timeout = "-1"
          max.rollback.retry.timeout = "-1"
        }
         
        client {
          async.commit.buffer.limit = 10000
          lock {
            retry.internal = 10
            retry.times = 30
          }
          report.retry.count = 5
          tm.commit.retry.count = 1
          tm.rollback.retry.count = 1
        }
         
        ## transaction log store
        store {
          ## store mode: file、db
          #mode = "file"
          mode = "db"
         
          ## file store
          file {
            dir = "sessionStore"
         
            # branch session size , if exceeded first try compress lockkey, still exceeded throws exceptions
            max-branch-session-size = 16384
            # globe session size , if exceeded throws exceptions
            max-global-session-size = 512
            # file buffer size , if exceeded allocate new buffer
            file-write-buffer-cache-size = 16384
            # when recover batch read size
            session.reload.read_size = 100
            # async, sync
            flush-disk-mode = async
          }
         
          ## database store
          db {
            ## the implement of javax.sql.DataSource, such as DruidDataSource(druid)/BasicDataSource(dbcp) etc.
            datasource = "dbcp"
            ## mysql/oracle/h2/oceanbase etc.
            db-type = "mysql"
            driver-class-name = "com.mysql.jdbc.Driver"
            url = "jdbc:mysql://127.0.0.1:3306/seata"
            user = "root"
            password = "root"
            min-conn = 1
            max-conn = 3
            global.table = "global_table"
            branch.table = "branch_table"
            lock-table = "lock_table"
            query-limit = 100
          }
        }
        lock {
          ## the lock store mode: local、remote
          mode = "remote"
         
          local {
            ## store locks in user's database
          }
         
          remote {
            ## store locks in the seata's server
          }
        }
        recovery {
          #schedule committing retry period in milliseconds
          committing-retry-period = 1000
          #schedule asyn committing retry period in milliseconds
          asyn-committing-retry-period = 1000
          #schedule rollbacking retry period in milliseconds
          rollbacking-retry-period = 1000
          #schedule timeout retry period in milliseconds
          timeout-retry-period = 1000
        }
         
        transaction {
          undo.data.validation = true
          undo.log.serialization = "jackson"
          undo.log.save.days = 7
          #schedule delete expired undo_log in milliseconds
          undo.log.delete.period = 86400000
          undo.log.table = "undo_log"
        }
         
        ## metrics settings
        metrics {
          enabled = false
          registry-type = "compact"
          # multi exporters use comma divided
          exporter-list = "prometheus"
          exporter-prometheus-port = 9898
        }
         
        support {
          ## spring
          spring {
            # auto proxy the DataSource bean
            datasource.autoproxy = false
          }
        }
        
         ```

        创建registry.conf:

        ```conf
        registry {
          # file 、nacos 、eureka、redis、zk、consul、etcd3、sofa
          type = "nacos"
         
          nacos {
            #serverAddr = "localhost"
            serverAddr = "localhost:8848"
            namespace = ""
            cluster = "default"
          }
          eureka {
            serviceUrl = "http://localhost:8761/eureka"
            application = "default"
            weight = "1"
          }
          redis {
            serverAddr = "localhost:6379"
            db = "0"
          }
          zk {
            cluster = "default"
            serverAddr = "127.0.0.1:2181"
            session.timeout = 6000
            connect.timeout = 2000
          }
          consul {
            cluster = "default"
            serverAddr = "127.0.0.1:8500"
          }
          etcd3 {
            cluster = "default"
            serverAddr = "http://localhost:2379"
          }
          sofa {
            serverAddr = "127.0.0.1:9603"
            application = "default"
            region = "DEFAULT_ZONE"
            datacenter = "DefaultDataCenter"
            cluster = "default"
            group = "SEATA_GROUP"
            addressWaitTime = "3000"
          }
          file {
            name = "file.conf"
          }
        }
         
        config {
          # file、nacos 、apollo、zk、consul、etcd3
          type = "file"
         
          nacos {
            serverAddr = "localhost"
            namespace = ""
          }
          consul {
            serverAddr = "127.0.0.1:8500"
          }
          apollo {
            app.id = "seata-server"
            apollo.meta = "http://192.168.1.204:8801"
          }
          zk {
            serverAddr = "127.0.0.1:2181"
            session.timeout = 6000
            connect.timeout = 2000
          }
          etcd3 {
            serverAddr = "http://localhost:2379"
          }
          file {
            name = "file.conf"
          }
        }
        
        ```

        ==实际上,就是要将seata中的我们之前修改的两个配置文件复制到这个项目下==

    3.   **主启动类**

        ```java
        @SpringBootApplication(exclude = DataSourceAutoConfiguration.class) //取消数据源的自动创建
        @EnableDiscoveryClient
        @EnableFeignClients
        public class SeataOrderMain2001 {
        
            public static void main(String[] args) {
                SpringApplication.run(SeataOrderMain2001.class,args);
            }
        }
        ```

        

    4.   **service层**

         ```xml
        public interface OrderService {
        
            /**
             * 创建订单
             * @param order
             */
            void create(Order order);
        }
        ```

        ```xml
        @FeignClient(value = "seata-storage-service")
        public interface StorageService {
        
            /**
             * 减库存
             * @param productId
             * @param count
             * @return
             */
            @PostMapping(value = "/storage/decrease")
            CommonResult decrease(@RequestParam("productId") Long productId, @RequestParam("count") Integer count);
        }
        ```

        ```xml
        @FeignClient(value = "seata-account-service")
        public interface AccountService {
        
            /**
             * 减余额
             * @param userId
             * @param money
             * @return
             */
            @PostMapping(value = "/account/decrease")
            CommonResult decrease(@RequestParam("userId") Long userId, @RequestParam("money") BigDecimal money);
        }
         
         
        
        ```

        ```xml
        @Service
        @Slf4j
        public class OrderServiceImpl implements OrderService {
        
            @Resource
            private OrderDao orderDao;
            @Resource
            private AccountService accountService;
            @Resource
            private StorageService storageService;
        
            /**
             * 创建订单->调用库存服务扣减库存->调用账户服务扣减账户余额->修改订单状态
             * 简单说:
             * 下订单->减库存->减余额->改状态
             * GlobalTransactional seata开启分布式事务,异常时回滚,name保证唯一即可
             * @param order 订单对象
             */
            @Override
            ///@GlobalTransactional(name = "fsp-create-order", rollbackFor = Exception.class)
            public void create(Order order) {
                // 1 新建订单
                log.info("----->开始新建订单");
                orderDao.create(order);
        
                // 2 扣减库存
                log.info("----->订单微服务开始调用库存,做扣减Count");
                storageService.decrease(order.getProductId(), order.getCount());
                log.info("----->订单微服务开始调用库存,做扣减End");
        
                // 3 扣减账户
                log.info("----->订单微服务开始调用账户,做扣减Money");
                accountService.decrease(order.getUserId(), order.getMoney());
                log.info("----->订单微服务开始调用账户,做扣减End");
        
                // 4 修改订单状态,从0到1,1代表已完成
                log.info("----->修改订单状态开始");
                orderDao.update(order.getUserId(), 0);
        
                log.info("----->下订单结束了,O(∩_∩)O哈哈~");
            }
        }
        ```

        

         

         

         

         

         

         

    5.   **dao层,也就是接口**

        ```java
        @Mapper
        public interface OrderDao {
            /**
             * 1 新建订单
             * @param order
             * @return
             */
            int create(Order order);
        
            /**
             * 2 修改订单状态,从0改为1
             * @param userId
             * @param status
             * @return
             */
            int update(@Param("userId") Long userId, @Param("status") Integer status);
        }
        ```

         ==在resource下创建mapper文件夹,编写mapper.xml==

        ```xml
        <?xml version="1.0" encoding="UTF-8" ?>
        <!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
                "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
        <mapper namespace="com.eiletxie.springcloud.alibaba.dao.OrderDao">
        
            <resultMap id="BaseResultMap" type="com.eiletxie.springcloud.alibaba.domain.Order">
                <id column="id" property="id" jdbcType="BIGINT"></id>
                <result column="user_id" property="userId" jdbcType="BIGINT"></result>
                <result column="product_id" property="productId" jdbcType="BIGINT"></result>
                <result column="count" property="count" jdbcType="INTEGER"></result>
                <result column="money" property="money" jdbcType="DECIMAL"></result>
                <result column="status" property="status" jdbcType="INTEGER"></result>
            </resultMap>
        
            <insert id="create" parameterType="com.eiletxie.springcloud.alibaba.domain.Order" useGeneratedKeys="true"
                    keyProperty="id">
                insert into t_order(user_id,product_id,count,money,status) values (#{userId},#{productId},#{count},#{money},0);
            </insert>
        
            <update id="update">
                update t_order set status =1 where user_id =#{userId} and status=#{status};
           </update>
        </mapper>
         
        ```

    6.   **controller层**

        ```java
        @RestController
        public class OrderController {
            @Resource
            private OrderService orderService;
        
        
            /**
             * 创建订单
             *
             * @param order
             * @return
             */
            @GetMapping("/order/create")
            public CommonResult create(Order order) {
                orderService.create(order);
                return new CommonResult(200, "订单创建成功");
            }
        
        
        }
        ```

        

    7.   **entity类(也叫domain类)**

         ```java
        @Data
        @AllArgsConstructor
        @NoArgsConstructor
        public class CommonResult<T> {
            private Integer code;
            private String message;
            private T data;
        
            public CommonResult(Integer code, String message) {
                this(code, message, null);
            }
        }
         
        ```

        ![](.\图片\seala的12.png)

         

         

        

    8.   config配置类

        ```java
        @Configuration
        @MapperScan({"com.eiletxie.springcloud.alibaba.dao"})		指定我们的接口的位置
        public class MyBatisConfig {
        
        
        }
         
         
        
        ```

        ```java
        
        
        /**
         * @Author EiletXie
         * @Since 2020/3/18 21:51
         * 使用Seata对数据源进行代理
         */
        @Configuration
        public class DataSourceProxyConfig {
        
            @Value("${mybatis.mapperLocations}")
            private String mapperLocations;
        
            @Bean
            @ConfigurationProperties(prefix = "spring.datasource")
            public DataSource druidDataSource() {
                return new DruidDataSource();
            }
        
            @Bean
            public DataSourceProxy dataSourceProxy(DataSource druidDataSource) {
                return new DataSourceProxy(druidDataSource);
            }
        
            @Bean
            public SqlSessionFactory sqlSessionFactoryBean(DataSourceProxy dataSourceProxy) throws Exception {
                SqlSessionFactoryBean bean = new SqlSessionFactoryBean();
                bean.setDataSource(dataSourceProxy);
                ResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
                bean.setMapperLocations(resolver.getResources(mapperLocations));
                return bean.getObject();
            }
        }
         
         
        
        ```

        

    9.   

    10.   

    11.  

      ==库存==,seta-storage-2002

    **==看脑图==**

    1.    pom   
    2.   配置文件
    3.   主启动类
    4.    service层
    5.    dao层
    6.    controller层
    7.   
    8.   

     

     ==账号==,seta-account-2003

    **==看脑图==**

    1.    pom     
    2.   配置文件
    3.   主启动类
    4.   service层
    5.    dao层
    6.   controller层
    7.   
    8.   

5.   **全局创建完成后,首先测试不加seata**

     ![](.\图片\seala的14.png)

     

     ![](.\图片\seala的13.png)

​    

​    

​     

6.   使用seata:

     **在==订单模块==的serviceImpl类中的==create方法==添加启动分布式事务的注解**

     ```java
    /**
    	这里添加开启分布式事务的注解,name指定当前全局事务的名称
    	rollbackFor表示,发生什么异常需要回滚
    	noRollbackFor:表示,发生什么异常不需要回滚
    */
    @GlobalTransactional(name = "fsp-create-order",rollbackFor = Exception.class)
    ///@GlobalTransactional(name = "fsp-create-order", rollbackFor = Exception.class)
    public void create(Order order) {
        // 1 新建订单
        log.info("----->开始新建订单");
        orderDao.create(order);
    
        // 2 扣减库存
        log.info("----->订单微服务开始调用库存,做扣减Count");
        storageService.decrease(order.getProductId(), order.getCount());
        log.info("----->订单微服务开始调用库存,做扣减End");
    
        // 3 扣减账户
        log.info("----->订单微服务开始调用账户,做扣减Money");
        accountService.decrease(order.getUserId(), order.getMoney());
        log.info("----->订单微服务开始调用账户,做扣减End");
    
        // 4 修改订单状态,从0到1,1代表已完成
        log.info("----->修改订单状态开始");
        orderDao.update(order.getUserId(), 0);
    
        log.info("----->下订单结束了,O(∩_∩)O哈哈~");
    }
    
    ```

     

7.   此时在测试

    发现,发生异常后,直接回滚了,前面的修改操作都回滚了

 



### setat原理:

![](.\图片\seala的15.png)

![](.\图片\seala的16.png)



**seata提供了四个模式:**

![](.\图片\seala的17.png)



![](.\图片\seala的18.png)

==第一阶段:==

![](.\图片\seala的20.png)

​	![](.\图片\seala的19.png)





==二阶段之提交==:

![](.\图片\seala的21.png)



==二阶段之回滚:==

![](.\图片\seala的22.png)

![](.\图片\seala的23.png)







==断点==:

![](.\图片\seala的24.png)

**可以看到,他们的xid全局事务id是一样的,证明他们在一个事务下**





![](.\图片\seala的25.png)

**before 和 after的原理就是**

![](.\图片\seala的26.png)

**在更新数据之前,先解析这个更新sql,然后查询要更新的数据,进行保存**



































































































































































