# Docker

## 一、简介

**Docker**是一个（基于Go语言实现）开源的应用容器引擎；是一个轻量级容器技术；

Docker支持将软件编译成一个镜像；然后在镜像中各种软件做好配置，将镜像发布出去，其他使用者可以直接使用这个镜像；

运行中的这个镜像称为容器，容器启动是非常快速的。

## 二、核心概念

docker主机(Host)：安装了Docker程序的机器（Docker直接安装在操作系统之上）；

docker客户端(Client)：连接docker主机进行操作；

docker仓库(Registry)：用来保存各种打包好的软件镜像；

docker镜像(Images)：软件打包好的镜像；放在docker仓库中；

docker容器(Container)：镜像启动后的实例称为一个容器；容器是独立运行的一个或一组应用

使用Docker的步骤：

1）、安装Docker

2）、去Docker仓库找到这个软件对应的镜像；

3）、使用Docker运行这个镜像，这个镜像就会生成一个Docker容器；

4）、对容器的启动停止就是对软件的启动停止；

## 三、安装Docker

### 1）、mac 的docker 安装

```shell
1、安装docker
	 brew cask install docker
2、启动docker
systemctl start docker
docker -v
Docker version 1.12.6, build 3e8e77d/1.12.6
3、开机启动docker
systemctl enable docker
4、停止docker
systemctl stop docker
5、镜像加速

```

### 2）、Linux 的 docker 安装

```
1. 安装
2. 镜像加载
```



## 四、Docker常用命令&操作

### 1）、镜像操作

| 操作 | 命令                                                         | 说明                                                         |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 检索 | docker  search 关键字  eg：docker  search redis <br> docker search -s 30 tomcat | 我们经常去docker  hub上检索镜像的详细信息，如镜像的TAG。<br> 搜索收藏 >30 的镜像 |
| 拉取 | docker pull 镜像名:tag                                       | :tag是可选的，tag表示标签，多为软件的版本，默认是latest      |
| 列表 | docker images                                                | 查看所有本地镜像                                             |
| 删除 | docker rmi image-id                                          | 删除指定的本地镜像                                           |

https://hub.docker.com/

docker run -it tomcat /bin/bash

### 2）、帮助命令

docker info  , docker version  , docker --help 

### 3）、容器操作（有镜像才能产生容器）

软件镜像（QQ安装程序）----运行镜像----产生一个容器（正在运行的软件，运行的QQ）；

步骤：

````shell
1、搜索镜像
docker search tomcat
# -s : 列出收藏数不小于指定值的镜像。
2、拉取镜像
docker pull tomcat
3、根据镜像启动容器   
docker run --name mytomcat -d tomcat:latest
#docker run -it --name mytomcat 
#启动守护式容器 ： docker run -d 容器名
4、docker ps  
查看运行中的容器
-a :列出当前所有正在运行的容器+历史上运行过的
-l :显示最近创建的容器。
-n：显示最近n个创建的容器。
-q :静默模式，只显示容器编号。
--no-trunc :不截断输出。
5、 停止运行中的容器
docker stop  容器的id 
6、查看所有的容器
docker ps -a 
7、启动容器  （配合 docker ps -a 使用）
docker start 容器id 
8、删除一个容器
 docker rm 容器id 
 # docker rmi  -f 镜像ID
 # docker rmi -f 镜像名1:TAG 镜像名2:TAG 
 # docker rmi -f $(docker images -qa)
 
9、启动一个做了端口映射的tomcat
[root@localhost ~]# docker run -d -p 8888:8080 tomcat
-d：后台运行
-p: 将主机的端口映射到容器的一个端口    主机端口:容器内部的端口

10、为了演示简单关闭了linux的防火墙
service firewalld status ；查看防火墙状态
service firewalld stop：关闭防火墙
11、查看容器的日志
docker logs container-name/container-id

更多命令参看
https://docs.docker.com/engine/reference/commandline/docker/
可以参考每一个镜像的文档

12、退出容器
# 两种退出容器 exit：容器停止退出 ， ctrl+P+Q ：容器退出不停止
13、 强制停止
# docker kill 容器ID或者容器名
14、查看日志
#docker logs -f -t --tail 容器ID
-- -t 是加入时间戳 ， -f 跟随最新的日志打印， --tail 数字 显示最后多少条
15、查看容器内运行的进程
# docker top 容器ID
16、查看容器内部细节
# __mindmap__topicdocker inspect 容器ID
17、进入正在运行的容器并以命令行交互
# exec -it 容器ID bashShell
# 重新进入docker attach 容器ID
# attach 直接进入容器启动命令的终端，不会启动新的进程
# exec 是在容器中打开新的终端，并且可以启动新的进程
18、从容器内拷贝文件到主机上
# docker cp  容器ID:容器内路径 目的主机路径

19、Docker镜像commit操作补充
# docker commit -m=“提交的描述信息” -a=“作者” 容器ID 要创建的目标镜像名:[标签名]
````

### 4）、Help 命令翻译

``` 
attach    Attach to a running container                 # 当前 shell 下 attach 连接指定运行镜像 
build     Build an image from a Dockerfile              # 通过 Dockerfile 定制镜像 
commit    Create a new image from a container changes   # 提交当前容器为新的镜像 
cp        Copy files/folders from the containers filesystem to the host path   #从容器中拷贝指定文件或者目录到宿主机中 
create    Create a new container                        # 创建一个新的容器，同 run，但不启动容器 
diff      Inspect changes on a container's filesystem   # 查看 docker 容器变化 
events    Get real time events from the server          # 从 docker 服务获取容器实时事件 
exec      Run a command in an existing container        # 在已存在的容器上运行命令 
export    Stream the contents of a container as a tar archive   # 导出容器的内容流作为一个 tar 归档文件[对应 import ] 
history   Show the history of an image                  # 展示一个镜像形成历史 
images    List images                                   # 列出系统当前镜像 
import    Create a new filesystem image from the contents of a tarball # 从tar包中的内容创建一个新的文件系统映像[对应export] 
info      Display system-wide information               # 显示系统相关信息 
inspect   Return low-level information on a container   # 查看容器详细信息 
kill      Kill a running container                      # kill 指定 docker 容器 
load      Load an image from a tar archive              # 从一个 tar 包中加载一个镜像[对应 save] 
login     Register or Login to the docker registry server    # 注册或者登陆一个 docker 源服务器 
logout    Log out from a Docker registry server          # 从当前 Docker registry 退出 
logs      Fetch the logs of a container                 # 输出当前容器日志信息 
port      Lookup the public-facing port which is NAT-ed to PRIVATE_PORT    # 查看映射端口对应的容器内部源端口 
pause     Pause all processes within a container        # 暂停容器 
ps        List containers                               # 列出容器列表 
pull      Pull an image or a repository from the docker registry server   # 从docker镜像源服务器拉取指定镜像或者库镜像 
push      Push an image or a repository to the docker registry server    # 推送指定镜像或者库镜像至docker源服务器 
restart   Restart a running container                   # 重启运行的容器 
rm        Remove one or more containers                 # 移除一个或者多个容器 
rmi       Remove one or more images             # 移除一个或多个镜像[无容器使用该镜像才可删除，否则需删除相关容器才可继续或 -f 强制删除] 
run       Run a command in a new container              # 创建一个新的容器并运行一个命令 
save      Save an image to a tar archive                # 保存一个镜像为一个 tar 包[对应 load] 
search    Search for an image on the Docker Hub         # 在 docker hub 中搜索镜像 
start     Start a stopped containers                    # 启动容器 
stop      Stop a running containers                     # 停止容器 
tag       Tag an image into a repository                # 给源中镜像打标签 
top       Lookup the running processes of a container   # 查看容器中运行的进程信息 
unpause   Unpause a paused container                    # 取消暂停容器 
version   Show the docker version information           # 查看 docker 版本号 
wait      Block until a container stops, then print its exit code   # 截取容器停止时的退出状态值 
  

```



### 5）、安装MySQL示例

```shell
docker pull mysql
```



错误的启动

```shell
[root@localhost ~]# docker run --name mysql01 -d mysql
42f09819908bb72dd99ae19e792e0a5d03c48638421fa64cce5f8ba0f40f5846

mysql退出了
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                           PORTS               NAMES
42f09819908b        mysql               "docker-entrypoint.sh"   34 seconds ago      Exited (1) 33 seconds ago                            mysql01
538bde63e500        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       compassionate_
goldstine
c4f1ac60b3fc        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       lonely_fermi
81ec743a5271        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       sick_ramanujan


//错误日志
[root@localhost ~]# docker logs 42f09819908b
error: database is uninitialized and password option is not specified 
  You need to specify one of MYSQL_ROOT_PASSWORD, MYSQL_ALLOW_EMPTY_PASSWORD and MYSQL_RANDOM_ROOT_PASSWORD；这个三个参数必须指定一个
```

正确的启动

```shell
[root@localhost ~]# docker run --name mysql01 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
b874c56bec49fb43024b3805ab51e9097da779f2f572c22c695305dedd684c5f
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
b874c56bec49        mysql               "docker-entrypoint.sh"   4 seconds ago       Up 3 seconds        3306/tcp            mysql01
```

做了端口映射

```shell
[root@localhost ~]# docker run -p 3306:3306 --name mysql02 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
ad10e4bc5c6a0f61cbad43898de71d366117d120e39db651844c0e73863b9434
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
ad10e4bc5c6a        mysql               "docker-entrypoint.sh"   4 seconds ago       Up 2 seconds        0.0.0.0:3306->3306/tcp   mysql02
```



几个其他的高级操作

```
docker run --name mysql03 -v /conf/mysql:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
把主机的/conf/mysql文件夹挂载到 mysqldocker容器的/etc/mysql/conf.d文件夹里面
改mysql的配置文件就只需要把mysql配置文件放在自定义的文件夹下（/conf/mysql）

docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
指定mysql的一些配置参数
```



## 五、Docker容器数据卷

### 1）、Docker容器数据卷的理念：

#### 描述：

​		将运用与运行的环境打包形成容器运行 ，运行可以伴随着容器，但是我们对数据的要求希望是持久化，容器之间希望有可能共享数据  。Docker容器产生的数据，如果不通过docker commit生成新的镜像，使得数据做为镜像的一部分保存下来，那么当容器删除后，数据自然也就没有了，为了能保存数据在docker中我们使用卷

> 一句话：有点类似我们Redis里面的rdb和aof文件

#### 功能：

* 容器的持久化
* 容器间继承+共享数据

### 2）、数据卷

容器内添加, 关联宿主机&容器目录：

**docker run -it -v /宿主机绝对路径目录:/容器内目录      镜像名**

>  docker run -it -v /Users/houyao/Downloads/myDockerDataVolume:/dataVolumeContainer tomcat  /bin/bash



查看数据卷是否挂载成功 : docker inspect 容器ID	

### 3）、数据卷容器

​		命名的容器挂载数据卷，其它容器通过挂载这个(父容器)实现数据共享，挂载数据卷的容器，称之为数据卷容器

**总体介绍**

​		以上一步新建的镜像zzyy/centos为模板并运行容器dc01/dc02/dc03

它们已经具有容器卷 ， /dataVolumeContainer1 ，/dataVolumeContainer2

#### 容器间传递共享(--volumes-from)

先启动一个父容器dc01 ， dataVolumeContainer2新增内容

dc02/dc03继承自dc01

>  --volumes-from
>
> 命令： docker run -it --name dc02 --volumes-from dc01 zzyy/centos
>
> dc02/dc03分别在dataVolumeContainer2各自新增内容

回到dc01可以看到02/03各自添加的都能共享了

删除dc01，dc02修改后dc03可否访问 ： （可以）

删除dc02后dc03可否访问：（可以）

新建dc04继承dc03后再删除dc03：（存在）

#### 结论：

容器之间配置信息的传递，数据卷的生命周期一直持续到没有容器使用它为止



## 六、DockerFile解析

### 1、DockerFile 是什么

DockerFile 是用来构建Docker 镜像的构建文件、是由一系列命令和参数构成的脚本。

构建三步骤： 编写Docker File文件、docker build、docker run 

### 2、DockerFile 构建解析过程

**DockerFile 内容基础知识**

1. 每条保留字指令都必须为大写字母且后面要跟随至少一个参数
2. 指令按照从上到下， 顺序执行
3. #表示注释
4. 每条指令都会常见一个新的镜像层，并对镜像进行提交

**执行DockerFile的大致流程**

1. docker 从基础镜像运行一个容器
2. 执行一条指令并对容器做出修改
3. 执行类似docker commit 的操作提交一个新的镜像层
4. docker再基于提交的镜像运行一个新的容器
5. 执行dockerfile 中的下一条指令直到所有的指令都执行完成

**小总结：**

从应用软件的角度来看，Dockerfile、Docker镜像与Docker容器分别代表软件的三个不同阶段，

*  Dockerfile是软件的原材料

*  Docker镜像是软件的交付品

*  Docker容器则可以认为是软件的运行态。

Dockerfile面向开发，Docker镜像成为交付标准，Docker容器则涉及部署与运维，三者缺一不可，合力充当Docker体系的基石。

1 Dockerfile，需要定义一个Dockerfile，Dockerfile定义了进程需要的一切东西。Dockerfile涉及的内容包括执行代码或者是文件、环境变量、依赖包、运行时环境、动态链接库、操作系统的发行版、服务进程和内核进程(当应用进程需要和系统服务和内核进程打交道，这时需要考虑如何设计namespace的权限控制)等等;

2 Docker镜像，在用Dockerfile定义一个文件之后，docker build时会产生一个Docker镜像，当运行 Docker镜像时，会真正开始提供服务; 

3 Docker容器，容器是直接提供服务的。 

### 3、DockerFile 体系结构，保留字指令

* FROM ： 基础镜像，当前镜像是基于哪个镜像的

* MAINTAINER ： 镜像维护者的姓名和邮箱

* RUN ：构建镜像时需要运行的命令

* EXPOSE ：当前容器对外暴露出的端口

* WORKDIR ：指定在创建容器后，终端默认登录进来的工作目录，一个落脚点

* ENV ：用来在构建镜像过程中设置环境变量

  ``` 
  ENV MY_PATH /usr/mytest
  这个环境变量可以在后续的任何RUN指令中使用，这就如同在命令前面指定了环境变量前缀一样；
  也可以在其它指令中直接使用这些环境变量，
  比如：WORKDIR $MY_PATH
  ```

* ADD : 将宿主机目录下的文件拷贝进镜像且ADD命令会自动处理URL和解压tar包
* COPY ：类似ADD，拷贝文件和目录到镜像中， <源路径>  到 <目标路径>
* VOLUME ：容器数据卷、用于数据保存和持久化工作
* CMD ：指定一个容器启动时要运行的命令
* ONBUILD ：当构建一个被继承的DockerFile时运行命令，父镜像在被子继承后父镜像的onbuild 触发



Dockerfile:

| BUILD         | Both    | RUN        |
| ------------- | ------- | :--------- |
| FROM          | WORKDIR | CMD        |
| MAINTAINER    | USER    | ENV        |
| COPY          |         | EXPOSE     |
| ADD           |         | VOLUME     |
| RUN           |         | ENTRYPOINT |
| ONBUILD       |         |            |
| .dockerignore |         |            |















## 七、Docker 常用安装



## 八、本地镜像发布到阿里云







# Docker

## 1、简介

**Docker**是一个开源的应用容器引擎；是一个轻量级容器技术；

Docker支持将软件编译成一个镜像；然后在镜像中各种软件做好配置，将镜像发布出去，其他使用者可以直接使用这个镜像；

运行中的这个镜像称为容器，容器启动是非常快速的。

## 2、核心概念

docker主机(Host)：安装了Docker程序的机器（Docker直接安装在操作系统之上）；

docker客户端(Client)：连接docker主机进行操作；

docker仓库(Registry)：用来保存各种打包好的软件镜像；

docker镜像(Images)：软件打包好的镜像；放在docker仓库中；

docker容器(Container)：镜像启动后的实例称为一个容器；容器是独立运行的一个或一组应用

使用Docker的步骤：

1）、安装Docker

2）、去Docker仓库找到这个软件对应的镜像；

3）、使用Docker运行这个镜像，这个镜像就会生成一个Docker容器；

4）、对容器的启动停止就是对软件的启动停止；

## 3、安装Docker



#### 1）、mac 的docker 安装

```shell
1、安装docker
	 brew cask install docker

2、启动docker
systemctl start docker
docker -v
Docker version 1.12.6, build 3e8e77d/1.12.6
3、开机启动docker
systemctl enable docker
4、停止docker
systemctl stop docker
```

## 4、Docker常用命令&操作

### 1）、镜像操作

| 操作 | 命令                                                         | 说明                                                         |
| ---- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 检索 | docker  search 关键字  eg：docker  search redis <br> docker search -s 30 tomcat | 我们经常去docker  hub上检索镜像的详细信息，如镜像的TAG。<br> 搜索收藏 >30 的镜像 |
| 拉取 | docker pull 镜像名:tag                                       | :tag是可选的，tag表示标签，多为软件的版本，默认是latest      |
| 列表 | docker images                                                | 查看所有本地镜像                                             |
| 删除 | docker rmi image-id                                          | 删除指定的本地镜像                                           |

https://hub.docker.com/

docker run -it tomcat /bin/bash



### 2）、容器操作

软件镜像（QQ安装程序）----运行镜像----产生一个容器（正在运行的软件，运行的QQ）；

步骤：

````shell
1、搜索镜像
[root@localhost ~]# docker search tomcat
2、拉取镜像
[root@localhost ~]# docker pull tomcat
3、根据镜像启动容器   
docker run --name mytomcat -d tomcat:latest
docker run -it --name mytomcat 
4、docker ps  
查看运行中的容器
> <br>-a :列出当前所有正在运行的容器+历史上运行过的<br>-l :显示最近创建的容器。<br>-n：显示最近n个创建的容器。<br>-q :静默模式，只显示容器编号。<br>--no-trunc :不截断输出。
5、 停止运行中的容器
docker stop  容器的id 
6、查看所有的容器
docker ps -a 
7、启动容器 
docker start 容器id 
8、删除一个容器
 docker rm 容器id 
9、启动一个做了端口映射的tomcat
[root@localhost ~]# docker run -d -p 8888:8080 tomcat
-d：后台运行
-p: 将主机的端口映射到容器的一个端口    主机端口:容器内部的端口

10、为了演示简单关闭了linux的防火墙
service firewalld status ；查看防火墙状态
service firewalld stop：关闭防火墙
11、查看容器的日志
docker logs container-name/container-id

更多命令参看
https://docs.docker.com/engine/reference/commandline/docker/
可以参考每一个镜像的文档

````



### 3）、安装MySQL示例

```shell
docker pull mysql
```



错误的启动

```shell
[root@localhost ~]# docker run --name mysql01 -d mysql
42f09819908bb72dd99ae19e792e0a5d03c48638421fa64cce5f8ba0f40f5846

mysql退出了
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                           PORTS               NAMES
42f09819908b        mysql               "docker-entrypoint.sh"   34 seconds ago      Exited (1) 33 seconds ago                            mysql01
538bde63e500        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       compassionate_
goldstine
c4f1ac60b3fc        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       lonely_fermi
81ec743a5271        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       sick_ramanujan


//错误日志
[root@localhost ~]# docker logs 42f09819908b
error: database is uninitialized and password option is not specified 
  You need to specify one of MYSQL_ROOT_PASSWORD, MYSQL_ALLOW_EMPTY_PASSWORD and MYSQL_RANDOM_ROOT_PASSWORD；这个三个参数必须指定一个
```

正确的启动

```shell
[root@localhost ~]# docker run --name mysql01 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
b874c56bec49fb43024b3805ab51e9097da779f2f572c22c695305dedd684c5f
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
b874c56bec49        mysql               "docker-entrypoint.sh"   4 seconds ago       Up 3 seconds        3306/tcp            mysql01
```

做了端口映射

```shell
[root@localhost ~]# docker run -p 3306:3306 --name mysql02 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
ad10e4bc5c6a0f61cbad43898de71d366117d120e39db651844c0e73863b9434
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
ad10e4bc5c6a        mysql               "docker-entrypoint.sh"   4 seconds ago       Up 2 seconds        0.0.0.0:3306->3306/tcp   mysql02
```



几个其他的高级操作

```
docker run --name mysql03 -v /conf/mysql:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
把主机的/conf/mysql文件夹挂载到 mysqldocker容器的/etc/mysql/conf.d文件夹里面
改mysql的配置文件就只需要把mysql配置文件放在自定义的文件夹下（/conf/mysql）

docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
指定mysql的一些配置参数
```







