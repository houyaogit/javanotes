

# 常用命令

### LInux白皮书：基础命令

history

last		

使用wc命令 具体通过wc --help 可以查看。

如：wc -l filename 就是查看文件里有多少行

​       wc -w filename 看文件里有多少个word。

​       wc -L filename 文件里最长的那一行是多少个字。





切割文件 split --help

split -C 1000k imt.log splog





**查看端口号：**netstat -apn|grep 80  	 netstat -an | grep 23 (查看是否打开[23端口](https://www.baidu.com/s?wd=23端口&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1Yzny7brj9-PjP-njD1n16L0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3En1fLP10YPjn3)) 

nc -lp 23 &(打开[23端口](https://www.baidu.com/s?wd=23端口&tn=44039180_cpr&fenlei=mv6quAkxTZn0IZRqIHckPjm4nH00T1Yzny7brj9-PjP-njD1n16L0ZwV5Hcvrjm3rH6sPfKWUMw85HfYnjn4nH6sgvPsT6KdThsqpZwYTjCEQLGCpyw9Uz4Bmy-bIi4WUvYETgN-TLwGUv3En1fLP10YPjn3)，即telnet)

找到进程号以后，再使用以下命令查看详细信息：ps -aux|grep <进程号>  

杀掉进程； kill -9 <pid> 





**基础命令**

mkdir 123 创建目录123, 	mkdir a.txt 创建文件

rm -rf 删除命令。 rm -rf 目录名称，-r 递归向下删除，-f 强制删除且没有提示 

pwd 		查看当前路径

uname 	查看系统，	uname -a 查看系统位数

重启生效:

chkconfig iptables on 开启防火墙

chkconfig iptables off 关闭防火墙

及时生效：

service iptables start 	 开启防火墙

service iptables stop 	 关闭防火墙

\>/dev/null 2>&1 标准输出和标准错误都重定向到了/dev/null ， /dev/null是linux的无底洞，进去了出不来哦。 

&>/dev/null 不管你是啥玩意儿文件描述符，通通重定向到/dev/null

赋权

chmod 755 赋予可执行可读写权限（chmod 777） chmod -x 赋予可执行

find . -mtime -1 -type f -print

 用来查找过去24小时（-mtime –2则表示过去48小时）内修改过的文件。如果您想将所有查		 			 

 找到的文件打一个包，则可以使用以下脚本：tar -zcvf lastmod.tar.gz 

将目录 ./scala 及其下面的所有文件、子目录的文件主改成 hadoop 用户（超级用户执行）

sudo chown -R hadoop ./scala





echo [-ne][字符串]或 echo [--help][--version]

参数：-n 不要在最后自动换行,-e 若字符串中出现以下字符，则特别加以处理，而不会将它当成				 

一般文字输出：

   	\a 发出警告声；

   	\b 删除前一个字符；

   	\c 最后不加上换行符号；

   	\f 换行但光标仍旧停留在原来的位置；

   	\n 换行且光标移至行首；

  	\r 光标移至行首，但不换行；

  	\t 插入tab；

 	\v 与\f相同；

 	\\ 插入\字符；

**查CPU**

\# 总核数 = 物理CPU个数 X 每颗物理CPU的核数

\# 总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

\# 查看物理CPU个数

cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l

\# 查看每个物理CPU中core的个数(即核数)	

cat /proc/cpuinfo| grep "cpu cores"| uniq

\# 查看逻辑CPU的个数

cat /proc/cpuinfo| grep "processor"| wc -l

\# 查看CPU信息（型号）

cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c

**SSH远程**

ssh [cassandra@200.31.147.10](mailto:cassandra@200.31.147.10) 远程连接服务器。 ssh 用户名@ip_

scp -rp xxx mps@199.31.177.215:/home/mps/product 远程复制





**定时任务crontab**

crontab -r 清楚定时任务；

**编辑经典定时任务：** （crontab -e,-l）

30 0 * * * . ~/.bash_profile; cd /home/cassandra/app/dpsquoteapp/; sh ./start_server.sh

（每天凌晨0：30 cd 进目录， 运行start_server.sh脚本）

10 0 * * * . ~/.bash_profile; find /home/mps/log/ -mtime +30 -name *.log.* -exec rm -rf {} \;

(每天删除一个月前生成的日志文件)

**增加定时任务（不予许使用不安全的vi命令情况下使用 echo等命令，*****.log.\*** **日志格式）：**

crontab -l > /tmp/crontab.txt

echo "10 0 * * * . ~/.bash_profile; find /home/mps/log/ -mtime +30 -name "*.log.*" -exec rm -rf {} \;" >> /tmp/crontab.txt

crontab /tmp/crontab.txt

rm -f /tmp/crontab.txt

**删除定时任务(通过txt文件 制空crontab )**

crontab -l > /tmp/crontab.txt

sed -i '/mtime/d' /tmp/crontab.txt

crontab /tmp/crontab.txt

rm -f /tmp/crontab.txt





**搜索查看：**

grep -o world a.txt | wc -l 查world出现多少次

$grep -5 'parttern' inputfile //打印匹配行的前后5行 

$grep -C 5 'parttern' inputfile //打印匹配行的前后5行 

$grep -A 5 'parttern' inputfile //打印匹配行的后5行 

$grep -B 5 'parttern' inputfile //打印匹配行的前5行





grep condition1 a.txt |grep condition2	或

egrep "condition1|condition2" a.txt	且	

egrep -e "condition1|condition2" a.txt	正则

more ，cat (cat)more log.log |grep ERROR > a.txt

awk '//{print}' /etc/hosts

全局搜索：find -name 'java*' , whereis java

实时查看：tail -f catalina.out 

vi搜索

1、vi搜索 vi命令模式下输入“/字符串”，例如“/Section”。

2、如果查找下一个，按“n”即可。

vi 编辑命令:输入i 可以进行编辑文件,退出编辑：按下Esc键，在按下:q!。 :wq 保存





**sed命令**

1 .替换：sed -i 's/abc/def/g' a.txt

2 .增加：sed -i '$a something' .bash_profile

3 .在一文件中指定的字符后插入内容

sed -i 's/指定的字符/&要插入的字符/'  文件

4 .用sed某一行之前加入一些内容

sed -i '/指定内容/i\要插入的字符' text.html





**编译成Unix格式**

首先确保 text.html 为unix格式，如无法确认，请先执行下面这条语句：	

dos2unix text.html





**关于进程**

jps （kill -9 进程号 --杀死进程）

taskkill /f /im excel.exe 杀进程。

查看java进程 ps -ef|grep java

查看8080端口那个进程再用

netstat -apn | grep 8080

ctrl+z 暂停进程

fg 再启动 暂停的进程。

**关于内存**

**ls -lht**

du -sh *  文件占内存大小 ，看物理内存df -h ，free -g/-m （如果要看/data目录下各个子目录的大小，不包括子目录的子目录，可以用以下命令  du -sh * ）

df -h 查看所有内存分配使用情况

看内存：

free -g 

grep MemTotal /proc/meminfo # 查看内存总量

grep MemFree /proc/meminfo # 查看空闲内存量

du -sh wenjian 查看文件夹内存大小

reboot 重启命令

top -c 看进程运行内存

再按: M , P . T

 P：按%CPU使用率排行

 T：按MITE+排行 (CPU使用时间)

 		M：按%MEM排行

**日期时间修改**

date -s 修改时间

date -s 2015-10-25

date -s 15:00:00





**用户操作**

**赋权**

chown -R cassandra /home/oracle

密码超过次数，被锁：

查看：pam_tally2 --user username --reset

解锁：pam_tally2 --user=username --reset

不用下面的命令

chmod -R 777 /home/oracle





update passwd:

sudo passwd - root (update root passwd)

sudo passwd root (update root passwd)

[root@Centos6 /]# passwd yonghu (update normal user passwd)

su - root 切换root用户

cat /etc/passwd

update user available time：（99999 无限期）

passwd -x 90 euser  执行操作后，euser用户的密码时间改为90天有效期

userdel houyao ;delete user (强制删除 userdel -f houyao)

new user:

**useradd –d /usr/sam -m sam**

此命令创建了一个用户sam，其中-d和-m选项用来为登录名sam产生一个主目	

 录/usr/sam（/usr为默认的用户主目录所在的父目录）。

 		假设当前用户是sam，则下面的命令修改该用户自己的口令：

**passwd**

Old password:****** 

如果是超级用户，可以用下列形式指定任何用户的口令：

**passwd sam**

New password:*******





**文件操作**

**打压解压**

打包tar 文件：	tar -cvf dpsquoteapp.tar dpsquoteapp/

解压gz文件：	tar zxvf gz 文件路径 

解压tart文件：	tar xvf dpsquoteapp.tar 

tar -zcvf lastmod.tar.gz 





ulimit -a

修改/etc/security/limits.conf文件，在文件中添加如下行：

\* soft noproc 65535

\* hard noproc 65535

\* soft nofile 65535

\* hard nofile 65535



## Linux 分析工具

- **vmstat**：进程、虚拟内存、页面交换、IO读写、CPU活动等
- iostat & iotop：系统IO状态信息
- ifstat & iftop：实时网络流量监控
- **netstat**：查看网络相关信息,各种网络协议套接字状态
- dstat：全能型实时系统信息统计
- strace：诊断、调试程序的系统调用
- GDB：程序调试、coredump分析
- Isof：查看系统当前打开的文件信息
- tcpdump：网络抓包工具
- traceroute：网络路由分析工具

## Linux面试题

如今程序员生产的代码99%都部署在linux环境下，代码发现缺陷，程序员的第一反应是到 Linux 上拉日志下来看。程序员不是运维，不需要掌握非常多复杂命令。

## 推荐终端工具

- SecureCRT
- xshell

## 常用命令（重要）

ls/ll、cd、mkdir、rm-rf、cp、mv、ps -ef | grep xxx、kill、free-m、tar -xvf file.tar

**查看进程：**（例：如何查看所有xx进程）

ps -ef | grep xxx

​	ps -aux | grep xxx（-aux显示所有状态）

**编辑 vi/vim ：** 

**vi x.log**  编辑你的日志文件

i  写入

:wq 保存退出

:q! 或者 ctrl+c 退出不保存

Shift+g 跳至当前文本最后一行，看最新的日志，都在最下面

## top⭐

显示系统中各个进程的资源占用状况，可以看是否有 CPU 占用过大的进程。

## less 和 more

less 与 more 类似，但使用 less 可以随意浏览文件，而 more 仅能向前移动，却不能向后移动，而且 less 在查看之前不会加载整个文件。

## tail⭐

**查看日志：**

tail -f  *.log ： 适用于实时查看日志,开发环境还行，生产就算了，日志会很多。

**tail -f error.log**  ：生产中一般用这个实时看异常日志

**-f ：循环读取 ，用于查阅正在改变的日志文件。**

## netstat⭐

用于显示网络状态。

```plain
-a (all)显示所有选项，netstat默认不显示LISTEN相关
-n 拒绝显示别名，能显示数字的全部转化成数字。(重要)
-r 显示路由信息，路由表
-e 显示扩展信息，例如uid等
-s 按各个协议进行统计 (重要)
```

## grep 查找⭐

**grep 是必备日志分析命令**

**grep -r '关键字如商品ID' \*.log （使用频率最高）**

**grep '关键字如商品ID' \*.log | grep 免费商品（在管道符前条件结果中，在加条件筛选下)** 

**grep '关键字如商品ID' \*.log >> anan.txt 【相关日志输入到一个txt中，下载到本地慢慢看，我最喜欢】**

grep "被查找的字符串" 文件名

```
grep -n 2019-10-24 00:01:11' *.log
```

可以查找 *.log文件中，查到时间内的所有信息

## 查找特定文件 find

**find ~ -name "需要查找的文件名"**

比如：`find ~ -name "本机ip.txt"` 就可以得到文件名所在的目录

### 管道操作符    |

可将指令连接起来,前一个指令的输出作为后一个指令的输入

### 杀僵尸进程

部分程序员，肯定喜欢下面命令

ps -ef | grep java 【先查java进程ID】

kill -9 java进程ID 【生产环境谨慎使用】

## 对文件内容做统计 awk ⭐

依次处理文件的每一行，并读取里面的每一个字段，可用作统计。

$ awk 动作 文件名

## 批量替换 sed

sed 配合正则表达式批量替换文本内容

## 你经常使用哪些 Linux 命令，主要用来解决什么问题？









# Linux面试题





如今程序员生产的代码99%都部署在linux环境下，代码发现缺陷，程序员的第一反应是到 Linux 上拉日志下来看。程序员不是运维，不需要掌握非常多复杂命令。

# 推荐终端工具

- SecureCRT
- xshell

## 常用命令（重要）

ls/ll、cd、mkdir、rm-rf、cp、mv、ps -ef | grep xxx、kill、free-m、tar -xvf file.tar

**查看进程：**（例：如何查看所有xx进程）

ps -ef | grep xxx

​	ps -aux | grep xxx（-aux显示所有状态）

**编辑 vi/vim ：** 

**vi x.log** 编辑你的日志文件

i 写入

:wq 保存退出

:q! 或者 ctrl+c 退出不保存

Shift+g 跳至当前文本最后一行，看最新的日志，都在最下面

## top⭐

显示系统中各个进程的资源占用状况，可以看是否有 CPU 占用过大的进程。

## less 和 more

less 与 more 类似，但使用 less 可以随意浏览文件，而 more 仅能向前移动，却不能向后移动，而且 less 在查看之前不会加载整个文件。

## tail⭐

**查看日志：**

tail -f *.log ： 适用于实时查看日志,开发环境还行，生产就算了，日志会很多。

**tail -f error.log** ：生产中一般用这个实时看异常日志

**-f ：循环读取 ，用于查阅正在改变的日志文件。**

## netstat⭐

用于显示网络状态。

```plain
-a (all)显示所有选项，netstat默认不显示LISTEN相关
-n 拒绝显示别名，能显示数字的全部转化成数字。(重要)
-r 显示路由信息，路由表
-e 显示扩展信息，例如uid等
-s 按各个协议进行统计 (重要)
```

## grep 查找⭐

**grep 是必备日志分析命令**

**grep -r '关键字如商品ID' \*.log （使用频率最高）**

**grep '关键字如商品ID' \*.log | grep 免费商品（在管道符前条件结果中，在加条件筛选下)** 

**grep '关键字如商品ID' \*.log >> anan.txt 【相关日志输入到一个txt中，下载到本地慢慢看，我最喜欢】**

grep "被查找的字符串" 文件名
`grep -n 2019-10-24 00:01:11' *.log`
可以查找 *.log文件中，查到时间内的所有信息

## 查找特定文件 find

**find ~ -name "需要查找的文件名"**

比如：`find ~ -name "本机ip.txt"` 就可以得到文件名所在的目录

### 管道操作符 |

可将指令连接起来,前一个指令的输出作为后一个指令的输入

### 杀僵尸进程

部分程序员，肯定喜欢下面命令

ps -ef | grep java 【先查java进程ID】

kill -9 java进程ID 【生产环境谨慎使用】

## 对文件内容做统计 awk ⭐

依次处理文件的每一行，并读取里面的每一个字段，可用作统计。

$ awk 动作 文件名

## 批量替换 sed

sed 配合正则表达式批量替换文本内容

## 你经常使用哪些 Linux 命令，主要用来解决什么问题？