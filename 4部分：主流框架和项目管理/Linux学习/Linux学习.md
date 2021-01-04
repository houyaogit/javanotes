# Linux 学习

# 文件系统

Linux操作系统具有一定层次结构，由若干目录和子目录组成，不同于windows操作系统，Linux只有一个根目录，用“/”表示，它采用的是**级层式的树形结构**。
----*在Linux世界里，一切皆文件。*
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70.png)

## 具体的目录结构

- /bin[**重点**] (/usr/bin、/usr/local/bin）

> 是Binary的缩写，这个目录存放着**最经常使用的命令**

- /sbin （/usr/sbin、/usr/local/sbin）

> s就是Super User的意思，这里存放的是系统管理员使用的系统管理程序。

- /home[**重点**]

> 存放普通用户的主目录，在Linux中每个用户都有一个自己的目录，一般该目录名是以用户的账号命名的。

- /root[**重点**]

> 该目录为系统管理员，也称作超级权限者的用户主目录。

- boot[**重点**」

> 存放的是启动Linux时使用的一些核心文件，包括一些连接文件以及镜像文件T

- 下列目录和linux内核相关，慎用！

> - /proc
>   这个目录是一个虚拟的目录，它是系统内存的映射，访问这个目录来获取系统信息。
> - /srv
>   service缩写，该目录存放一些服务启动之后需要提取的数据。
> - /sys
>   这是linux2.6内核的一个很大的变化。该目录下安装了2.6内核中新出现的一个文件系统

- /tmp

> 这个目录是用来存放一些临时文件的。

- /dev

> 类似于winlows的设备管理器，把所有的硬件用文件的形式存储。

- /media[**重点**]

> linux系统会自动识别一些设备，例如U盘、光驱等等，当识别后，linux会把识别的设备挂载到这个目录下。

- /mnt[**重点**]

> 系统提供该目录是为了让用户临时挂载别的文件系统的，我们可以将外部的存储挂载在/mnt/上，然后进入该目录就可以查看里的内容了。

- /opt

> 这是给主机额外安装软件所摆放的目录。如安装ORACLE数据库就可放到该目录下。默认为空。

- /usr/local[**重点**]

> 这是别一个给主机额外安装软件所安装的目录。一般是通过编译源码方式安装的程序。

- /var[**重点**]

> 这个目录中存放着在不断扩充着的东西，习惯将经常被修改的目录放在这个目录下。包括各种日志文件。

- /selinux[security-enhanced linux] (类似于360)

> SELinux是一种安全子系统，它能控制程序只能访问特定文件。

### 小结

1. Linux的目录**有且只有**一个根目录
2. Linux的**各个目录存放的内容是规划好**的，**不能**随便乱放文件。
3. Linux以文件的形式管理我们的设备，因此Linux系统，一切皆文件。
4. Linux的文件系统是一个**树形结构**，每个分叉（子目录）有特定的功能。



# vim/vi 编辑器

vi 文件名（进入vi正常模式）
按i或a进入编辑模式
按esc+：进入命令模式（wq：保存并退出，q：不保存退出，q！：强制退出）
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126162218676.png)

## vi快捷键

在**命令模式**下使用：
拷贝当前行：（数字）yy ，粘贴 p
删除当前行：（数字）dd
查找单词：/+单词（n选择下一个单词）
设置行号：：set nu
取消行号： ：set nonu
在**正常模式**下使用：
跳转到文件首：gg，跳转到文件尾：G
撤销：u
跳转行数：输入数字+shift+g
下移几行：数字+enter

# Liunx常用命令

echo -e “\e[1;31m abc \e[0m”
打印有颜色字符
history 历史命令
-c 清空
-w保存缓存中的命令

## 关机&重启命令

**shutdown**

- shutdown -h now :立即关机
- shutdown -h 1:一分钟后关机
- shutdown -r now:立即重启

**halt**：关机
**reboot**：重启系统
**sync**：将内存上的数据写入磁盘（建议重启或关机前使用）
**logout**:注销用户（图形界面无效）

## 用户管理

/home/ 用户家目录：目录下有各个用户对应的家目录，用户登录时，会自动进入自己的家目录。
说明：

1. Linux 系统是一个**多用户多任务**的操作系统，任何一个要使用系统资源的用户，都必须首先向
   系统管理员申请一个账号，然后以这个账号的身份进入系统。
2. Linux 的用户需要**至少要属于一个组**。

**添加用户**：
**useradd** [选项] 用户名
该命令会默认创建一个与用户同名的组
在/home/目录下可查看新建用户的家目录
也可以通过 **useradd -d 指定目录 新的用户名**，给新创建的用户指定家目录
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200416161947329.png)
指定密码：passwd +用户名（密码不回显）
删除用户（要root）：
**userdel** 用户名（这种方法家目录会保留）
userdel -r 用户名（删除用户及家目录）
查询用户信息 id 用户名
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200416163148711.png)
GID 是group ID(用户组的ID)
UID是user ID（用户的ID）
组：所在组名称
切换用户：
**su** - 切换用户名（高权限到低权限不要输密码，反之需要输密码）
回退到原来的用户：exit
查看当前是哪个用户：whoami

**用户组**：
添加用户组：groupadd 组名
删除组：groupdel 组名
将用户指定到已创建组：useradd -g 组名 用户名
修改用户组：usermod -g 组名 用户名
**用户和组的相关文件**
**/etc/passwd** 文件
用户（user）的配置文件，记录用户的各种信息
每行的含义：用户名:口令:用户标识号:组标识号:注释性描述:主目录:登录Shell
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200416170619344.png)
**/etc/shadow** 文件
口令的配置文件
每行的含义：登录名:加密口令:最后一次修改时间:最小时间间隔:最大时间间隔:警告时间:不活动时间:失效时间:标志

**/etc/group** 文件
组(group)的配置文件，记录 Linux 包含的组的信息
每行含义：组名:口令:组标识号:组内用户列表
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200416170656164.png)

## Linux指定运行级别

0：关机
1：单用户
2：多用户状态无网络服务
3：多用户状态有网络服务
4：系统未使用
5：图形界面
6：系统重启
常用运行级别为3或5，保存运行级别的文件在/etc/inittab/的id:5:initdefault:这一行的数字里。
我们使用vim查看一下这个文件：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200420193613657.png)
使用init 3可使界面切换到命令行界面，其他同理。（关机重启后还是回到图形界面），那么怎么才能一直保存修改呢，其实只要修改保存配置的文件/etc/inittab/就行了。
应用：怎么才能在忘记root密码的情况下修改root密码呢？
1.在开机界面按下erter键
2.看见一个界面按e
3.到达一个新的界面，选择第二行（进入kernel内核），按e
4.到下一个界面，按空格再**输入1**，再按enter
5.按下b，这时会进入但用户界面（root身份）
6.用passwd root就可以改密码了。

## 帮助指令

通过Linux提供的帮助指令了解这个命令。
**man**[命令]
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200420202734936.png)
然后会跳到这个页面
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163118008.png)
help[命令]
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163118076.png)

## 文件目录

绝对路径：以root目录为基准点
相对路径：以当前目录为基准点
**pwd**：显示当前目录
**ls** [选项] [文件或目录]

- ls -a:显示隐藏文件
- ls -l:以列表显示文件

**cd** [目录]：切换目录

- cd …:回到上一级目录
- cd /root/以绝对路径切换到root目录下
- cd /…/…/home：以相对路径切换到home目录下（假设当前目录为/usr/lib）

**mkdir**:创建目录

- -p创建多级目录

**rmdir**：删除目录（rmdir删除的是空目录，目录下有文件无法删除）
**rm**：用于删除文件或目录

- -r：表示递归，用于删除目录
- -f：表示强制执行
- -i：表示删除前询问
- -r *:表示删除当前目录下的所有目录，需谨慎使用！

**touch** 文件名：创建文件
例如：touch hello.txt
也可同时创建多个文件，只要在文件之间加上空格
例如：touch a.txt b.txt

**cp** :拷贝文件
cp [选项] source dest
例如：cp a.txt dir1
这里将a.txt文件拷贝到dir1目录下面

- -r 递归拷贝整个文件夹
  下面不使用-r选项将无法拷贝，因为目录下带有文件
  ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200422192511166.png)
  当目标目录下已存在相同文件时，在命令前加 \ 将不提示直接覆盖
  例如：\cp -r dir/ dir1/

**rm**：删除文件
例如：rm aaa.txt

- -r：递归删除目录下所有文件
- -f：强制删除不询问
  例如：rm -rf dir
  该命令强制删除dir目录下的所有文件

**mv**：移动文件或目录及重命名
语法：mv 旧文件或目录 新文件或目录
相当于将就文件剪切，然后粘贴到当前目录，因为当前目录不存在这个新文件，系统就将旧文件重命名然后粘贴到当前目录，实现了重命名。当我们想移动到的目录下存在同名文件时，将会**覆盖**原来的文件。
例如：

> mv a.txt hello.txt

这里将a.txt**重命名**为hello.txt

> mv dir/ dir2/

这里将dir目录**移动**到dir1目录下了

**cat**：以只读方式打开文件

- -n显示行号
  cat命令常与管道符|more连用，方便分页浏览
  例如：
  ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200422203602894.png)
  ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200422203636723.png)
  ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163118048.png)
  **more** 指令是一个基于 VI 编辑器的文本过滤器，它以全屏幕的方式按页显示文本文件的内容。
  ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163118099.png)

**less** 指令用来分屏查看文件内容，它的功能与 more 指令类似，但是比 more 指令更加强大，支持各种显示终端。less 指令在显示文件内容时，并不是一次将整个文件加载之后才显示，而是**根据显示需要加载内容**，对于显示大型文件具有较高的效率。
对于以上三个查看文件的目录，以下快捷键大部分能通用：

| 命令       | 说明       |
| ---------- | ---------- |
| 空白键     | 向下翻一页 |
| 方向键向上 | 上一行     |
| 方向键向下 | 下一行     |
| q          | 离开       |
| ctrl+b     | 返回上一页 |
| ctrl+f     | 下一页     |
| =或:f      | 当前进度   |

**[>和 >>指令]**
覆盖指令：>
ls -l > a.txt
将当前列表的信息覆盖到a.txt文件中，如果不存在则会创建。
追加指令：>>
ll >> b.txt
将当前列表的信息追加到b.txt文件中，如果不存在则会创建。
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200422211717275.png)
**echo**指令：输出内容到控制台
例如：
echo “hello world”
输出hello world到控制台
echo $PATH
输出环境变量
**head**指令：显示文件开头部分，默认10行

- -n指定显示行数
  head -n 5 /etc/profile
  显示/etc/profile的前5行

**tail**指令：显示文件结尾部分

- -n指定显示行数
  tail -n 5 /etc/profile
  显示/etc/profile的后5行
- -f 实时追踪文档的更新信息，比较**常用**

**ln**指令:建立链接，存放链接其他文件的路径

- -s：创建软链接，类似Windows的快捷方式

例如：
ln -s /etc link
创建一个软链接链接到/etc目录
rm -rf link
删除link软链接
说明：使用软链接访问目录时，通过pwd目录查看的仍然是link所在目录。
**history**指令：查看历史命令
例如：
history
查看历史所以命令
history 10
查看最近使用的10个命令
!556
直接使用历史中第556次使用的命令

## 日期类

**date**指令：显示，设置时间日期
语法：

1. date（显示当前时间）
2. date +%Y（显示当前年份）
3. date +%m（显示当前月份）
4. date +%d（显示当前是哪一天）
5. date “+%Y-%m-%d %H:%M:%S”（显示年月日时分秒）

例如：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200423093300969.png)
这里%Y、%m或其他之间可以加任意的连接符
设置日期：date -s 日期
例如：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200423093610729.png)
cal指令：查看日历
语法：
cal：查看本月日历
cal 年份：查看全年日历
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200423093821967.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163118096.png)

## 查询

**find**指令：从指定目录向下递归遍历查找指定文件
语法：find [搜索范围] [选项]

- -name按指定文件名查找
- -user按文件拥有者查找
- -size按文件大小查找

> find /home/ -name hello.txt

查找/home/目录下名为hello.txt的文件

> find /home/ -user xiaoming

查找/home/目录下拥有者为xiaoming的文件

> find /home/ -size +2M

查找/home/目录下大于2M的文件（-2M为小于，不加符号为等于，kb用小写k表示）
**locate**指令:locate 指令可以快速定位文件路径。locate 指令**利用事先建立的系统中所有文件名称及路径的locate 数据库**实现快速定位给定的文件。Locate指令无需遍历整个文件系统，查询速度较快。为了保证查询结果的准确度，管理员必须**定期更新** locate 时刻。

> updatedb
> locate hello.txt

这俩条指令先更新数据库，然后快速定位hello.txt文件的位置。
**grep 指令和 管道符号 |**
grep 过滤查找 ， 管道符，“|”，表示将前一个命令的处理结果输出传递给后面的命令处理。
语法：grep [选项] 查找内容 源文件

- -n显示行号
- -i忽略大小写

示例：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163118089.png)

## 压缩与解压

gzip/gunzip指令
说明：
gzip 文件名（将文件压缩成*.gz文件）
gunzip 文件名（解压*.gz文件）
这两个指令使用后原来文件都会不存在
zip/unzip指令
zip用于压缩

- -r 递归压缩，即压缩目录

> zip -r a.zip a
> 这里将a目录解压当当前目录下并命名为a.zip

unzip用于解压

- d 指定解压后文件的存放目录

> unzip -d a hhh.zip
> 这里将hhh.zip解压到a目录下

tar指令：打包指令，打包后的文件以.tar.gz结尾
语法：tar [选项] xxx.tar.gz 打包的内容

- -c：产生.tar打包文件
- -v：显示详细信息
- -f：指定压缩后的文件名
- -z：打包同时压缩
- -x：解包.tar文件
- -t 列出档案文件的内容，查看已经备份了哪些文件
  例如：

> tar -zcvf txt.tar.gz hhh.txt hello.txt
> 这里将hhh.txt和hello.txt打包压缩为txt.tar.gz文件

> tar -zxvf num.tar.gz -C 233 
> 这里将num.tar.gz解压到233这个目录下

注意：对于解压的选项，f必须放最后，其他的位置任意，但是建议按照规范，解压到指定某个目录必须加上-C选项，不然会出错。



# Linux组管理和权限管理

Linux 组基本介绍
在 linux 中的每个用户必须属于一个组，不能独立于组外。在 linux 中每个文件有所有者、所在组、其它组的概念。一般为文件的创建者,谁创建了该文件，就自然的成为该文件的所有者。

查看文件的所有者

- ls -ahl
  ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200429202412917.png)

## 更改用户组和文件主

**修改文件的所有者**

- chown 用户名 文件名

   

  - -R：如果是目录 则使其下所有子文件或目录递归生效
    ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200429202952726.png)
    ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200429202938133.png)
    这里将a.txt文件的拥有者从tom该为root

**修改文件所在组**

- chgrp 组名 文件名

   

  - -R：如果是目录 则使其下所有子文件或目录递归生效
    ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200429203256604.png)
    这里将a.txt文件的组名该为root

**该用户所在组**

- usermod -g 组名 用户名

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200429203751873.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200429203813649.png)
tom用户本来在police组，通过命令变为dancer组
**修改用户的附加组**
usermod -G 附加组名 用户

**修改用户的uid**
usermod -u 2005 用户名

**修改用户组的gid**
groupmod -g 3000 组名

**其它组**：除文件的所有者和所在组的用户外，系统的其它用户所属组都是文件的其它组。

## 权限的基本介绍

ls -l显示如下：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200429205032952.png)
说明：

- 第0位：文件类型（d,-,l,c,b）

   

  - d：表示目录
  - -：表示普通文件
  - l：软链接
  - c：字符设备（鼠标、键盘）
  - b：块文件（硬盘）

- 1-3位：文件拥有者所有权限

- 4-6位：文件所属组拥有的权限

- 7-9位：其他组拥有的权限

### rwx 权限详解

**rwx 作用到文件**

1. [ r ]代表可读(read): 可以读取,查看
2. [ w ]代表可写(write): 可以修改,但是不代表可以删除该文件,删除一个文件前提条件是对该文件所在的目录有写权限，才能删除该文件.
3. [ x ]代表可执行(execute):可以被执行

**rwx 作用到目录**
\5. [ r ]代表可读(read): 可以读取，ls 查看目录内容
\6. [ w ]代表可写(write): 可以修改,目录内创建+删除+重命名目录
\7. [ x ]代表可执行(execute):可以进入该目录

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200429205930629.png)
后面

- 1：如果是文件则表示硬链接数，如果是文件表示子目录数
- root：用户名
- root：组名
- 0：文件大小（字节），若是文件夹显示4096
- 4月 29 20：26 ：最后修改时间
- a.txt：文件名

**修改权限**-chmod
通过 chmod 指令，可以修改文件或者目录的权限

- 第一种方式：+ 、-、= 变更权限
  u:所有者 g:所有组 o:其他人 a:所有人(u、g、o 的总和)

1. chmod u=rwx,g=rx,o=x 文件目录名
2. chmod o+w 文件目录名
3. chmod a-x 文件目录名
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200429214852404.png)
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200429214819417.png)
   这里将aaa.txt用户权限该为rwx，用户组权限改为x，其他组权限改为w

- 第二种方式：通过数字变更权限
  规则：r=4 w=2 x=1,rwx=4+2+1=7
  chmod u=rwx,g=rx,o=x 文件目录名相当于 chmod 751 文件目录名
  ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200429215155488.png)
  使用数字更改权限较为简单

### 修改密码

**chage [选项] 登录**

选项：

- -d, --lastday 最近日期 将最近一次密码设置时间设为“最近日期”
- -E, --expiredate 过期日期 将帐户过期时间设为“过期日期”
- -h, --help 显示此帮助信息并推出
- -I, --inactive INACITVE 过期 INACTIVE 天数后，设定密码为失效状态
- -l, --list 显示帐户年龄信息
- -m, --mindays 最小天数 将两次改变密码之间相距的最小天数设为“最小天数”
- -M, --maxdays 最大天数 将两次改变密码之间相距的最大天数设为“最大天数”
- -R, --root CHROOT_DIR chroot 到的目录
- -W, --warndays 警告天数 将过期警告天数设为“警告天数”



# Linux cron任务调度



任务调度：是指系统在某个时间执行的特定的命令或程序。相当于Windows任务管理器。
任务调度分类：

1. 系统工作：有些重要的工作必须周而复始地执行。如病毒扫描等
2. 个别用户工作：个别用户可能希望执行某些程序，比如对 mysql 数据库的备份

语法：crontab [选项]

- -e：编辑定时crontab任务
- -l：查询crontab任务
- -r：删除当前用户所有的crontab任务
- service crond restart：[重启任务调度]

设置任务调度文件：/etc/crontab
设置个人任务调度。执行 crontab –e 命令。接着输入任务到调度文件
例如：

> */1 * * * * ls –l /etc/ > /tmp/to.txt

意思说每小时的每分钟执行 ls –l /etc/ > /tmp/to.txt 命令
参数说明：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163609020.png)
特殊符号说明：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163609110.png)
案例：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163609057.png)
案例：每隔 1 分钟，就将当前的日期信息，追加到 /tmp/mydate 文件
中

1. 先编写一个文件 /home/mytask1.sh
   date >> /tmp/mydate
2. 给 mytask1.sh 一个可以执行权限chmod 744 /home/mytask1.sh
3. crontab -e
4. */1 * * * * /home/mytask1.sh





# Linux磁盘分区、挂载

## 分区基础知识

分区的方式：

1. mbr 分区:
   1.最多支持四个主分区
   2.系统只能安装在主分区
   3.扩展分区要占一个主分区
   4.MBR 最大只支持 2TB，但拥有最好的兼容性
2. gpt 分区:
   1.支持无限多个主分区（但操作系统可能限制，比如 windows 下最多 128 个分区）
   2.最大支持 18EB 的大容量（1EB=1024 PB，1PB=1024 TB ）
   3.windows7 64 位以后支持 gpt

## window下的磁盘分区

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163704739.png)

## linux分区

**原理：**

1. Linux 来说无论有几个分区，分给哪一目录使用，它归根结底就只有一个根目录，一个独立且唯一的文件结构 , Linux 中每个分区都是用来组成整个文件系统的一部分。
2. Linux 采用了一种叫“载入”的处理方法，它的整个文件系统中包含了一整套的文件和目录， 且将一个分区和一个目录联系起来。这时要载入的一个分区将使它的存储空间在一个目录下获得。

**硬盘说明：**

1. Linux 硬盘分 IDE 硬盘和 SCSI 硬盘，目前基本上是 SCSI 硬盘
2. 对于 IDE 硬盘，驱动器标识符为“hdx~”,其中“hd”表明分区所在设备的类型，这里是指 IDE 硬盘了。“x”为盘号（a 为基本盘，b 为基本从属盘，c 为辅助主盘，d 为辅助从属盘）,“~”代表分区，前四个分区用数字 1 到 4 表示，它们是主分区或扩展分区，从 5 开始就是逻辑分区。例，hda3 表示为第一个 IDE 硬盘上的第三个主分区或扩展分区,hdb2 表示为第二个 IDE 硬盘上的第二个主分区或扩展分区。
3. 对于 SCSI 硬盘则标识为“sdx~”，SCSI 硬盘是用“sd”来表示分区所在设备的类型的，其余则和 IDE 硬盘的表示方法一样

通过lsblk指令可查看系统分区和挂载情况：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200505162356989.png)

## 在虚拟机上挂载一块硬盘

1. **虚拟机添加硬盘**
2. **分区**
   fdisk /dev/sdb
3. **格式化**
   mkfs -t ext4 /dev/sdb1
4. **挂载**
   先创建一个 /home/newdisk ,
   挂载指令： mount /dev/sdb1 /home/newdisk
   卸载指令：umount 挂载目录或硬盘目录
5. 设置可以自动挂载(**永久挂载**，当你重启系统，仍然可以挂载到/home/newdisk) 。
   vim /etc/fstab
   /dev/sdb1 /home/newdisk ext4 defaults 0 0

步骤：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163704673.png)

- 输入命令：fdisk /dev/sdb1
  依次输入n，新增分区，p，默认（按回车），默认，w（保存）
- 格式化磁盘：
  mkfs -t ext4 /dev/sdb1
  ext4为分区类型
- 创建目录，使用mount 命令将磁盘分区挂载到目录下
- 修改/etc/fstab 文件添加下面这行，然后mount -a生效
  ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200505170519639.png)

## 磁盘情况查询

命令：df -lh
查询系统磁盘总体占用情况
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200505173222331.png)
查询指定目录的磁盘占用情况
• 基本语法
du -h /目录
查询指定目录的磁盘占用情况，默认为当前目录

- -s 指定目录占用大小汇总
- -h 带计量单位
- -a 含文件
- –max-depth=1 子目录深度

实例：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200505173426134.png)
**实用指令**：

1. 统计/home 文件夹下文件的个数
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200505173645818.png)
2. 统计/home 文件夹下目录的个数
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/2020050517380962.png)
3. 统计/home 文件夹下文件的个数，包括子文件夹里的
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200505173846353.png)
4. 统计文件夹下目录的个数，包括子文件夹里的
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200505173905189.png)
5. 以树状显示目录结构
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163704737.png)



# linux网络环境配置

## 自动获取

点击系统，首选项，网络连接，点击网卡，编辑，点击自动连接，应用
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163957540.png)
缺点是每次自动获取的 ip 地址可能不一样。这个不适用于做服务器，因为我们的服务器的 ip 需要时固定的。

## 固定IP

直 接 修 改 配 置 文 件 来 指 定 IP, 并 可 以 连 接 到 外 网 ( 程 序 员 推 荐 ) ， 编 辑vi /etc/sysconfig/network-scripts/ifcfg-eth0
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163957532.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126163957625.png)
修改后，重启服务，使用service network restart这条命令。





# Linux进程管理

## 进程的基本介绍：

1. 在 LINUX 中，每个**执行的程序（代码）都称为一个进程**。每一个进程都分配一个 ID 号（**进程号PID**）。
2. 每一个进程，都会对应一个父进程（**父进程号PPID**），而这个父进程可以复制多个子进程例如 www 服务器。
3. 每个进程都可能以两种方式存在的。**前台与后台**，所谓前台进程就是用户目前的屏幕上可以进行操作的。后台进程则是实际在操作，但由于屏幕上无法看到的进程，通常使用后台方式执行。
4. 一般系统的服务都是**以后台进程**的方式存在，而且都会常驻在系统中。直到关机才才结束。

## 查看进程的命令

**ps，一般使用的参数是ps -aux**

- ps -a：显示当前终端所有进程信息
- ps -u：以用户的格式显示进程
- ps -x：显示后台进程运行的参数

ps指令详解：
1)指令：ps –aux|grep xxx ，比如我看看有没有 sshd 服务
2）指令说明
• System V 展示风格
• USER：用户名称
• PID：进程号
• %CPU：进程占用 CPU 的百分比
• %MEM：进程占用物理内存的百分比
• VSZ：进程占用的虚拟内存大小（单位：KB）
• RSS：进程占用的物理内存大小（单位：KB）
• TTY：终端名称
• STAT：进程状态，其中 S-睡眠，s-表示该进程是会话的先导进程，N-表示进程拥有比普通优先级更低的优先级，R-正在运行，D-短期等待，Z-僵死进程，T-被跟踪或者被停止等等
• START：进程的启动时间
• TIME：CPU 时间，即进程使用 CPU 的总时间
• COMMAND：启动进程所用的命令和参数，如果过长会被截断显示
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165652977.png)
应用案例：
以全格式显示当前所有的进程，查看进程的父进程
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507095556587.png)
-e 显示所有进程。-f 全格式

- C：CPU 用于计算执行优先级的因子。数值越大，表明进程是 CPU 密集型运算，执行优先级会降低；数值越小，表明进程是 I/O 密集型运算，执行优先级会提高
- STIME：进程启动的时间
- TTY：完整的终端名称
- TIME：CPU 时间
- CMD：启动进程所用的命令和参数

## 终止进程kill和killall

kill [选项] 进程号（功能描述：通过进程号杀死进程）
killall 进程名称（功能描述：通过进程名称杀死进程，也支持通配符，这在系统因负载过大而变得很慢时很有用）

常用选项：

- -9 :表示强迫进程立即停止

案例：

1. 踢掉非法登录用户
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165652806.png)
2. 终止远程登录服务 sshd, 在适当时候再次重启 sshd 服务，重启命令为
   （service sshd start）
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165653016-6381013.png)
3. 终止多个 gedit 编辑器 【killall , 通过进程名称来终止进程】
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507102825840.png)
4. 强制杀掉一个终端
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165652979.png)

## 查看进程树pstree

pstree [选项] ,可以更加直观的来看进程信息

- -p :显示进程的 PID
- -u :显示进程的所属用户

## 服务（service）管理

服务(service) 本质就是进程，但是是运行在后台的，通常都会监听某个端口，等待其它程序的请求，比如(mysql , sshd 防火墙等)，因此我们又称为守护进程，是 Linux 中非常重要的知识点。
在 CentOS7.0 后 不再使用 service ,而是 systemctl

service 管理指令：
service 服务名 [start | stop | restart | reload | status]
案例：
查看当前防火墙的状况，关闭防火墙和重启防火墙

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165652780.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507111725863.png)
这种方式只是**临时生效**，当重启系统后，还是回归以前对服务的设置。如果希望设置某个服务自启动或关闭永久生效，要使用 chkconfig 指令

注意：可以在windows下使用telnet测试linux端口是否有效
用法：telnet ip 端口号
下图表示连接成功
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507111937398.png)

## 查看服务名

1. 使用setup命令，选择系统服务，进入可查看及修改
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165652864.png)
2. 查看/etc/init.b/目录 ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165652923.png)

## 服务的运行级别(runlevel):

查看或者修改默认级别： vi /etc/inittab
Linux 系统有 7 种运行级别(runlevel)：常用的是级别 3 和 5
• 运行级别 0：系统停机状态，系统默认运行级别不能设为 0，否则不能正常启动
• 运行级别 1：单用户工作状态，root 权限，用于系统维护，禁止远程登陆
• 运行级别 2：多用户状态(没有 NFS)，不支持网络
• 运行级别 3：完全的多用户状态(有 NFS)，登陆后进入控制台命令行模式
• 运行级别 4：系统未使用，保留
• 运行级别 5：X11 控制台，登陆后进入图形 GUI 模式
• 运行级别 6：系统正常关闭并重启，默认运行级别不能设为 6，否则不能正常启动

开机流程
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/2020050711260240.png)

## chkconfig指令

通过 chkconfig 命令可以给每个服务的各个运行级别设置自启动/关闭
查看服务 chkconfig --list|grep xxx
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507112722262.png)
查看某个服务
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507112820851.png)
在某个运行级下设置服务是否自启动：chkconfig --level 5 服务名 on/off
案例：

1. 案例 1： 请显示当前系统所有服务的各个运行级别的运行状态

> bash> chkconfig --list

1. 案例 2 ：请查看 sshd 服务的运行状态

> bash> service sshd status

1. 案例 3： 将 sshd 服务在运行级别 5 下设置为不自动启动

> bash> chkconfig --level 5 sshd off

1. 案例 4： 当运行级别为 5 时，关闭防火墙。

> bash> chkconfig --level 5 iptables off

1. 案例 5： 在所有运行级别下，关闭防火墙

> bash> chkconfig iptables off

1. 案例 6： 在所有运行级别下，开启防火墙

> bash> chkconfig iptables on

chkconfig 重新设置服务后自启动或关闭，需要**重启机器 reboot 才能生效**

## 动态监控进程

top 与 ps 命令很相似。它们都用来显示正在执行的进程。Top 与 ps 最大的不同之处，在于 top 在执行一段时间可以更新正在运行的的进程。
语法：
top [选项]

- -d：秒数，指定top命令间隔多少秒更新，默认3秒
- -i：使top不显示闲置或僵死进程
- -p：通过指定进程PID来显示相应进程状态

交互操作（在top里使用的指令）

- P：以CPU使用率排序（默认此项）
- M：以内存使用率来排序
- N：以PID排序
- q：退出top

案例

1. 监听特定用户
   输入top进入监听，在输入u，选择监听用户
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165653010.png)
2. 终止指定的进程
   输入k，然后输入进程号
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165653039.png)
3. 指定系统状态更新的时间(每隔 10 秒自动更新， 默认是 3 秒)：
   top -d 10

**查看系统网络情况 netstat**
基本语法
netstat [选项]
常用：netstat -anp
选项说明

- -an 按一定顺序排列输出
- -p 显示哪个进程在调用
  ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165653022.png)
  查看具体某个服务
  ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165653016.png)





# linux软件安装RPM与YUM



## rpm包管理

一种用于互联网下载包的打包及安装工具，它包含在某些 Linux 分发版中。它生成具有.RPM扩展名的文件。RPM 是 RedHat PackageManager（RedHat 软件包管理工具）的缩写，类似 windows的 setup.exe，这一文件格式名称虽然打上了 RedHat 的标志，但理念是通用的。
Linux 的分发版本都有采用（suse,redhat, centos 等等），可以算是公认的行业标准了。

## rpm包查询

**rpm 包的简单查询指令**：
rpm –qa|grep xx：查询已安装的 rpm 列表
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507164730402.png)
rpm 包名**基本格式**：
rpm 包名：firefox-45.0.1-1.el6.centos.x86_64.rpm

- 名称:firefox
- 版本号：45.0.1-1
- 适用操作系统: el6.centos.x86_64，表示 centos6.x 的 64 位系统

rpm -qi 软件包名 ：查询软件包信息
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165826940.png)
rpm -ql 软件包名 :查询软件包中的文件
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165826935.png)
rpm -qf 文件全路径名：查询文件所属的软件包
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507165518674.png)

## rpm包卸载

rpm -e RPM 包的名称
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507165908552.png)
火狐被卸载了
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507165856736.png)

- 如果其它软件包依赖于您要卸载的软件包，卸载时则会产生错误信息。如： rpm -e foo
  removing these packages would break dependencies:foo is needed by bar-1.0-1
- 如果我们就是要删除 foo 这个 rpm 包，可以增加参数 --nodeps ,就可以强制删除，但是一般不推荐这样做，因为依赖于该软件包的程序可能无法运行
  如： rpm -e --nodeps foo
  带上 --nodeps 就是强制删除。

## rpm包安装

rpm -ivh RPM 包全路径名称

- i=install 安 装
- v=verbose 提 示
- h=hash 进度条

步骤：
首先需要将iso文件挂载，然后在去/media/，进入镜像文件里的Packages目录，然后将安装包复制到/opt/目录下，最后进行安装。
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165826957.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507170756443.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507170914728.png)

## yum

Yum 是一个 Shell 前端软件包管理器。基于 RPM 包管理，能够从指定的服务器自动下载 RPM 包并且安装，可以自动处理依赖性关系，并且一次安装所有依赖的软件包。使用 yum 的前提是可以联网。

- yum list|grep xx 软件列表：查询 yum 服务器是否有需要安装的软件
- yum install xxx 下载安装：安装指定的 yum 包

安装firefox:
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507172324207.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200507172704676.png)
安装完毕！







# Linux——javaEE环境搭建（jdk、tomcat、eclipse和mysql安装教程）



**准备压缩包**：

1. apache-tomcat-7.0.70.tar
2. eclipse-jee-mars-2-linux-gtk-x86_64.tar
3. jdk-7u79-linux-x64
4. mysql-5.6.14.tar
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200513202516590.png)
   **下载地址**：
   链接：https://pan.baidu.com/s/19qK8FU-ylnYNxTaGbru_Sw
   提取码：l3ch

先将安装包从Windows传送到Linux环境下的opt目录里，在这里我们使用的是ftp软件
ftp的使用可参考：

> https://blog.csdn.net/qq_45260767/article/details/105972350

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165956812.png)

## JDK的安装

## 解压jdk压缩包

先进入/opt目录，然后使用tar -zxvf 文件名 将文件解压，得到如下文件：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514101650342.png)

## 配置环境变量

进入/etc/profile文件，在文件末尾添加上下面三行（JAVA_HOME，PATH路径和输出变量）

JAVA_HOME=/opt/jdk1.7.0_79
PATH=/opt/jdk1.7.0_79/bin:$PATH
export JAVA_HOME PATH
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/2020051410234023.png)
这时候使用java命令，但是没有用，因为配置文件还未生效
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514102627411.png)
可以选择注销用户，然后重新登录让文件生效，也可以使用source命令直接将文件生效
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165956807.png)

## 测试使用

配置完毕我们创建一个Hello.java文件打印Hello World试试效果
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514103257882.png)
使用javac编译然后使用java运行，成功打印Hello world，说明jdk配置成功~
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514103337592.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514103355120.png)

## tomcat安装

## 解压tomcat安装包

同样在/opt命令下使用tar命令解压，得到以下目录
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514104735122.png)

## 启动tomcat

要想运行tomcat，**必须先进入apache-tomcat文件的bin目录**下，然后启动startup.sh文件，当然也可以将该路径加入环境变量里。
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165956936.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514105049899.png)

## 防火墙配置

启动tomcat之后，我们通过Linux本地浏览器可以访问tomcat
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165957075-6381197.png)
但是在Windows环境下面，无法访问tomcat
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165956885.png)
分析原因是防火墙没有打开8080端口，我们通过service iptables status命令可知只有22号端口开启。
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165956847.png)
我们进入/etc/sysconfig/iptables，对文件进行配置，添加上8080端口（对22号端口那行使用yy p 复制粘贴）
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165956846.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165956993.png)
文件配置完毕，重启防火墙服务，查看防火墙状态发现端口已经打开
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165956894.png)

## 测试访问

这时外网也可以访问tomcat，这里我们使用浏览器和telnet命令（需在管理员身份下运行cmd）测试都可顺利访问
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165957066.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514111613108.png)

## eclipse安装

## 解压eclipse

使用tar -zxvf 文件名

## 启动eclipse

1. 创建一个快捷方式
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514115823343.png)
2. 进入eclipse目录下，执行eclipse
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165957075.png)

## 测试jsp小项目

创建一个demo，输出 hello from Linux（注意，这里运行会发生错误，因为我们在安装tomcat时启动了tomcat，这里我们要将其关闭，方法是进入tomcat的bin目录下运行shutdown.sh文件）
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165957003.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514120220909.png)
运行jsp文件
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165957067.png)
在url后面加上/hello.jsp，就能成功访问，同样我们也可以在外网使用浏览器访问
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165957084.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514120601762.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165957087.png)

## mysql安装（源码安装）

## 安装步骤：

1.**查看是否存在旧版本，并卸载**
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514121528392.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514121546472.png)
2.**安装编译代码需要的包**

> yum -y install make gcc-c++ cmake bison-devel ncurses-devel

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514121618807.png)
\3. **解压mysql压缩包**
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514121813451.png)
\4. **编译安装**（直接复制下列代码）

> cmake -DCMAKE_INSTALL_PREFIX=/usr/local/mysql
> -DMYSQL_DATADIR=/usr/local/mysql/data -DSYSCONFDIR=/etc -DWITH_MYISAM_STORAGE_ENGINE=1 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DWITH_MEMORY_STORAGE_ENGINE=1 -DWITH_READLINE=1 -DMYSQL_UNIX_ADDR=/var/lib/mysql/mysql.sock -DMYSQL_TCP_PORT=3306 -DENABLED_LOCAL_INFILE=1 -DWITH_PARTITION_STORAGE_ENGINE=1 -DEXTRA_CHARSETS=all -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514122419818.png)
\5. **编译并安装**（花费时间较长）

> make && make install

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514122538696.png)

## 配置mysql

创建mysql用户及mysql用户组，现在mysql目录用户及用户组还是root用户，我们需要将其改为mysql用户
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165957118.png)
进入安装路径/usr/local/mysql目录下，然后执行**初始化指令**（下面这条脚本）

> scripts/mysql_install_db --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data --user=mysql

## 启动mysql

**添加服务**，拷贝服务脚本到init.d目录，并设置开机启动
**注意在 /usr/local/mysql 下执行**

> cp support-files/mysql.server /etc/init.d/mysql

**设置默认自启动**：

> 使用chkconfig mysql on命令

**启动服务**：

> 使用service mysql start命令

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514135142766.png)
进入/usr/local/mysql/bin目录下，执行mysql，默认密码为空
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514135604522.png)
我们设置一下密码：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200514135757849.png)
现在mysql就可以使用了
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165957292.png)

## 配置环境变量

编辑etc/profile文件，在之前jdk的路径后加上mysql的路径，然后重新使用配置文件，mysql就能在其他目录下使用了。
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/2020051414115514.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126165957129.png)





# Linux——shell编程



Shell 是一个命令行解释器，它为用户提供了一个向 Linux 内核发送请求以便运行程序的界面系统级程序，用户可以用 Shell 来启动、挂起、停止甚至是编写一些程序。
示意图：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200513204226499.png)
脚本格式要求：

1. 脚本以#!/bin/bash开头
2. 文件必须有可执行权限

## 创建第一个shell脚本

在这里我们先创建一个hello.sh文件，然后用vi编辑器输入内容
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200513204851666.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200513204750550.png)

执行方式：

1. 通过sh/bash命令执行
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/2020051320571019.png)
2. 先给文件赋予可执行权限，然后通过相对路径或绝对路径的方式执行
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200513210201421.png)
   绝对路径执行：
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200513210219109.png)

## shell变量

## 介绍

1）Linux Shell 中的变量分为，**系统变量**和用**户自定义变量**。
2）系统变量：$ HOME、$ PWD、$ SHELL、$ USER 等等

**定义变量**：变量=值
**撤销变量**：unset 变量
**声明静态变量**：readonly 变量，注意：不能 unset

案例 1：定义变量 A，然后撤销变量 A
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200513224454420.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200513224541214.png)

案例 2：显示当前 shell 中所有变量：set
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170130611.png)

## 定义变量的规则

1. 变量名称可以由**字母、数字和下划线**组成，但是**不能以数字**开头。
2. 等号两侧**不能有空格**
3. 变量名称一般**习惯为大写**

## 将命令的返回值赋值给变量（重点）

也就是在shell文件里使用终端的命令，并将命令返回的字符串返回给变量。
方法：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200513225044196.png)
注意：反引号在键盘esc键的下面，一般我们习惯使用上述第二种方式赋值。
案例：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200513225640901.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170130559.png)
上面我们将ls -l /home 命令的返回值赋给了LS变量，date命令赋值给了MY_DATE变量，然后输出。

## 设置环境变量

1. export 变量名=变量值 （功能描述：将 shell 变量输出为环境变量）
2. source 配置文件（功能描述：让修改后的配置信息**立即生效**）

Linux里将环境变量存放在/etc/profile文件里

案例：设置tomcat的环境变量
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518195154519.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518195325445.png)
使用**vim**编辑/etc/profile文件，在**文件末尾**添加export语句，然后使用**source命令**让文件生效，最后使用**echo**测试输出TOMCAT_HOME成功。

## 位置参数变量

当我们执行一个 shell 脚本时，如果希望获取到命令行的参数信息，就可以使用到位置参数变量。比如我们执行一个shell脚本，可以在执行命令后面跟参数作为变量传入文件内使用，这就是位置参数变量。
语法：

- $ n （功能描述：n 为数字，$ 0 代表命令本身，$ 1-到$ 9 代表第一到第九个参数，十以上的参数，十以上的参数需要用大括号包含，如$ {10}）
- $ * （功能描述：这个变量代表命令行中所有的参数，$ *把所有的参数看成一个整体）
- $ @（功能描述：这个变量也代表命令行中所有的参数，不过$ @把每个参数区分对待）
- $ #（功能描述：这个变量代表命令行中所有参数的个数）

案例：
$ 0表示命令名，$ 1代表第一个参数…，在执行脚本前应为文件添加可执行权限，在这里$ @与$ *都代表全部参数，但是两种间有略微差别，将他们用引号引起来分别对他们进行迭代时，$ *一次会输出全部参数$ @则会一个个输出。

> 参考https://blog.csdn.net/miachen520/article/details/98661139

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518201758508.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/2020051820182867.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518201839158.png)

## 预定义变量

就是 shell 设计者**事先已经定义好的变量**，可以直接在 shell 脚本中使用
语法：

- $$ ：当前进程的进程号（PID）
- $! ：后台运行的最后一个进程的进程号（PID）
- $？：最后一次执行的命令的返回状态。0表示成功，非0表示不成功。

案例：
创建preVar.sh文件，在第二行进行后台执行position.sh(在命令后面加&),最后返回成功执行状态
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518203920953.png)![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170130636.png)

## 运算符

语法：

1. ((算术表达式))
2. [算术表达式]
3. expr 算术表达式（运算符直接必须加空格）

案例：

- 计算（3+2）*4
- 2.计算两个参数的和

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170130794.png)![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518210116690.png)

## 条件判断

语法：
[ condition ]（注意 condition 前后要有空格）
非空返回 true，可使用$?验证（0 为 true，>1 为 false）

常用判断条件

1. 两个整数的比较
   = 字符串比较
   -lt 小 于
   -le 小于等于
   -eq 等 于
   -gt 大 于
   -ge 大于等于
   -ne 不等于
2. 按照文件权限进行判断
   -r 有读的权限 [ -r 文件 ]
   -w 有写的权限
   -x 有执行的权限
   3)按照文件类型进行判断
   -f 文件存在并且是一个常规的文件
   -e 文件存在
   -d 文件存在并是一个目录

案例：

1. “ok"是否等于"ok”
2. 23是否大于22
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518212908687.png)
   结果：
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518212822704.png)
3. aaa.txt文件是否存在
4. aaa.txt文件是否有读写权限
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170130684.png)
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518213113529.png)

## 流程控制

### if判断

语法

1. if [ 条件判断式 ];then
   程序
   fi
2. if [ 条件判断式 ]
   then
   程序
   elif [ 条件判断式 ]
   then
   程序
   fi
3. if [ 条件判断式 ]
   then
   程序
   else [ 条件判断式 ]
   then
   程序
   fi

**注意**：中括号与条件判断式之间必须要有空格
案例：判断输入参数大于等于80优秀，大于等于60及格，否则不及格
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518214739879.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518214714300.png)

### case语句

语法：
case $变量名 in
“值 1”）
如果变量的值等于值 1，则执行程序 1
;;
“值 2”）
如果变量的值等于值 2，则执行程序 2
;;
…省略其他分支…
*）
如果变量的值都不是以上的值，则执行此程序
;;
esac
案例：当命令行参数是 1 时，输出 “周一”, 是 2 时，就输出"周二"， 其它情况输出 “其他”
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518215856831.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/2020051821593415.png)

### for循环

有两种形式

1. for 变 量 in 值 1 值 2 值 3…
   do
   程序
   done
   案例1:打印命令行参数
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170130745.png)
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518221412345.png)
2. for (( 初始值;循环控制条件;变量变化 ))
   do
   程序
   done
   案例2：1到100累加并输出
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/2020051822175417.png)
   ![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518221803654.png)

### while循环

语法
while [ 条件判断式 ]
do
程序
done
案例：命令行输入一个数n，计算1+…+n的值
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170130840.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200518222724390.png)

## read读取控制台

read(选项)(参数)
选项：

- -p：指定读取值时的提示符；
- -t：指定读取值时等待的时间（秒），如果没有在指定的时间内输入，就不再等待了

参数变量：指定读取值的变量名

案例：
1.读取控制台输入一个 num 值
\2. 读取控制台输入一个 num 值，在 10 秒内输入。
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170130850.png)
测试案例2时第一次十秒内未输入，第二次输入
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/2020051919332339.png)

## 函数

shell 编程和其它编程语言一样，有系统函数，也可以自定义函数。这里介绍两个系统函数。

### 两个系统函数

- basename
  功能：返回完整路径后面的部分，常用于获取路径名
  语法：
  basename[路径名][后缀]

注意：basename 命令会删掉所有的前缀包括最后一个（‘/’）字符，然后将字符串显示出来，如果选项带有后缀，返回值将会把后缀删掉

案例：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200519194721854.png)

- dirname
  功能描述：从给定的包含绝对路径的文件名中去除文件名（非目录的部分），然后返回剩下的路径（目录的部分）

案例：
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200519194911829.png)

### 自定义函数

语法：
[ function ] funname[()]
{
Action;
[return int;]
}
案例：就是输入的两个数之和
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170130875.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200519200139756.png)







# Shell编程综合案例



要求：

1. 每天凌晨 2:10 备份 数据库test 到 /data/backup/db
2. 备份开始和备份结束能够给出相应的提示信息
3. 备份后的文件要求以备份时间为文件名，并打包成 .tar.gz 的形式，比如：2018-03-12_230201.tar.gz
4. 在备份的同时，检查是否有 10 天前备份的数据库文件，如果有就将其删除。

分析：
首先我们要编写一个shell脚本，功能将数据库备份并打包，然后使用cron任务调度设置每天执行的时间。

![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170257734.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200519221429336.png)
shell代码：

```powershell
#!/bin/bash

#备份的路径
BACKUP=/data/backup/db
#当前时间
DATETIME=$(date +%Y_%m_%d_%H%M%S)
echo "===========开始备份============"
#主机
HOST=localhost
#用户名
DB_USER=root
#密码
DB_PWD=123456
#备份数据库
DATABASE=test
#如果备份的路径文件存在就使用，否则就创建
[ ! -d "$BACKUP/$DATETIME" ] && mkdir -p "$BACKUP/$DATETIME"
#备份数据库到临时文件
mysqldump -u${DB_USER} -p${DB_PWD} --host=$HOST $DATABASE | gzip > $BACKUP/$DATETIME.sql.gz
#打包备份文件
cd $BACKUP
tar -zcvf $DATETIME.tar.gz $DATETIME
#删除临时文件
rm -rf $BACKUP/$DATETIME

#删除10天前的文件
find $BACKUP -mtime +10 -name "*.tar.gz" -exec rm -rf {} \;
echo "===========备份文件成功============"
```







# Ubuntu--apt软件管理



**介绍**：apt 是 Advanced Packaging Tool 的简称，是一款安装包管理工具。在 Ubuntu 下，我们可以使用 apt命令可用于软件包的安装、删除、清理等，类似于 Windows 中的软件管理工具。

# Ubuntu 软件操作的相关命令

**sudo apt-get update** 更新源
**sudo apt-get install package** 安装包
**sudo apt-get remove package** 删除包
sudo apt-cache search package 搜索软件包
sudo apt-cache show package 获取包的相关信息，如说明、大小、版本等
sudo apt-get install package --reinstall 重新安装包
sudo apt-get -f install 修复安装
sudo apt-get remove package --purge 删除包，包括配置文件等
sudo apt-get build-dep package 安装相关的编译环境
**sudo apt-get upgrade** 更新已安装的包
sudo apt-get dist-upgrade 升级系统
sudo apt-cache depends package 了解使用该包依赖那些包
sudo apt-cache rdepends package 查看该包被哪些包依赖
**sudo apt-get source package** 下载该包的源代码

# 更新 Ubuntu 软件下载地址

**原理**：
在ubuntu的/etc/apt/sources.list文件里指定了官方的软件仓库，apt软件下载都是通过里面的网址进行下载，但是这个网址在美国，因此下载的速度比较慢。我们可以通过修改source.list文件，将下载点转换到国内的镜像站。
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170401853.png)
先进入/etc/apt/目录
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170401725.png)
使用cp命令对sources.list文件进行备份
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200516174318561.png)
将sources.list文件内容清空，将站点的代码粘贴到文件内
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200516174714204.png)
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170401789.png)
更新服务器列表
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200516175149620.png)

# vim卸载安装案例

卸载
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170401820.png)
安装
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/20200516182203226.png)
显示安装软件信息
![在这里插入图片描述](https://gitee.com/houyao123/my-resource/raw/master/img/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQ1MjYwNzY3,size_16,color_FFFFFF,t_70-20201126170401875.png)