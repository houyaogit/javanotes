# JVM核心知识点



# Java8  JVM内存结构

基本结构与之前类似，只是Java8取消了之前的“永久代”，取而代之的是“元空间”——**Metaspace**，两者本质是一样的。“永久代”使用的是JVM的堆内存，而“元空间”是直接使用的本机物理内存。

![img](https://gitee.com/houyao123/my-resource/raw/master/img/JVMMem-20201215110159087.png)

# GC Roots

## 如何判断一个对象可以被回收？

### 引用计数算法

维护一个计数器，如果有对该对象的引用，计数器+ 1，反之-1。无法解决循环引用的问题。

### 可达性分析算法

从一组名为“GC Roots”的根节点对象出发，向下遍历。那些没有被遍历到、与GC Roots形成通路的对象，会被标记为“回收”。

## 哪些对象可以作为GC Roots？

1. 虚拟机栈（栈帧中的局部变量）中引用的对象。
2. 本地方法栈（native）中引用的对象。
3. 方法区中常量引用的对象。
4. 方法区中类静态属性引用的对象。

# JVM参数

## JVM 三种类型参数

### 标配参数

比如`-version`、`-help`、`-showversion`等，几乎不会改变。

### X参数

用得不多，比如`-Xint`，解释执行模式；`-Xcomp`，编译模式；`-Xmixed`，开启混合模式（默认）。

![img](https://gitee.com/houyao123/my-resource/raw/master/img/InkedJVMXParam_LI.jpg)

### XX参数

重要，用于JVM调优。

## JVM XX参数

### 布尔类型

**公式**：`-XX:+某个属性`、`-XX:-某个属性`，开启或关闭某个功能。比如`-XX:+PrintGCDetails`，开启GC详细信息。

### KV键值类型

**公式**：`-XX:属性key=值value`。比如`-XX:Metaspace=128m`、`-XX:MaxTenuringThreshold=15`。

## JVM Xms/Xmx参数

`-Xms`和`-Xmx`十分常见，用于设置**初始堆大小**和**最大堆大小**。第一眼看上去，既不像X参数，也不像XX参数。实际上`-Xms`等价于`-XX:InitialHeapSize`，`-Xmx`等价于`-XX:MaxHeapSize`。所以`-Xms`和`-Xmx`属于XX参数。

## JVM 查看参数

### 查看某个参数

- flag

使用`jps -l`配合`jinfo -flag JVM参数 pid` 。先用`jsp -l`查看java进程，选择某个进程号。

```java
17888 org.jetbrains.jps.cmdline.Launcher
5360 org.jetbrains.idea.maven.server.RemoteMavenServer
18052 demo3.demo3
```

`jinfo -flag PrintGCDetails 18052`可以查看18052 Java进程的`PrintGCDetails`参数信息。

```java
-XX:-PrintGCDetails
```

### 查看**所有**参数

使用`jps -l`配合`jinfo -flags pid`可以查看所有参数。

也可以使用`java -XX:+PrintFlagsInitial`

```java
[Global flags]
     intx ActiveProcessorCount                      = -1            {product}
    uintx AdaptiveSizeDecrementScaleFactor          = 4             {product}
    uintx AdaptiveSizeMajorGCDecayTimeScale         = 10            {product}
    uintx AdaptiveSizePausePolicy                   = 0             {product}
······
    uintx YoungPLABSize                             = 4096          {product}
     bool ZeroTLAB                                  = false         {product}
     intx hashCode                                  = 5             {product}
```

### 查看**修改**后的参数

使用`java -XX:PrintFlagsFinal`可以查看修改后的参数，与上面类似。只是修改过后是`:=`而不是`=`。

### 查看**常见**参数

如果不想查看所有参数，可以用`-XX:+PrintCommandLineFlags`查看常用参数。

```java
-XX:InitialHeapSize=132375936 -XX:MaxHeapSize=2118014976 -XX:+PrintCommandLineFlags -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
```

## JVM 常用参数

### -Xmx/-Xms

最大和初始堆大小。最大默认为物理内存的1/4，初始默认为物理内存的1/64。

### -Xss

等价于`-XX:ThresholdStackSize`。用于设置单个栈的大小，系统默认值是0，**不代表栈大小为0**。而是根据操作系统的不同，有不同的值。比如64位的Linux系统是1024K，而Windows系统依赖于虚拟内存。

### -Xmn

新生代大小，一般不调。

### -XX:MetaspaceSize

设置元空间大小。

### -XX:+PrintGCDetails

输出GC收集信息，包含`GC`和`Full GC`信息。

读GC信息，分析内存

### -XX:SurvivorRatio

新生代中，`Eden`区和两个`Survivor`区的比例，默认是`8:1:1`。通过`-XX:SurvivorRatio=4`改成`4:1:1`

### -XX:NewRatio

老生代和新年代的比列，默认是2，即老年代占2，新生代占1。如果改成`-XX:NewRatio=4`，则老年代占4，新生代占1。

### -XX:MaxTenuringThreshold

新生代设置进入老年代的时间，默认是新生代逃过15次GC后，进入老年代。如果改成0，那么对象不会在新生代分配，直接进入老年代。

# 四大引用

- 强、软、弱、虚

以下Demo都需要设置`-Xmx`和`-Xms`，不然系统默认很大，很难演示。

## 强引用

使用`new`方法创造出来的对象，默认都是强引用。GC的时候，就算**内存不够**，抛出`OutOfMemoryError`也不会回收对象，**死了也不回收**。详见[StrongReferenceDemo](https://github.com/MaJesTySA/JVM-JUC-Core/blob/master/src/jvm/StrongReferenceDemo.java)。

## 软引用

需要用`Object.Reference.SoftReference`来显示创建。**如果内存够**，GC的时候**不回收**。**内存不够**，**则回收**。常用于内存敏感的应用，比如高速缓存。详见[SoftReferenceDemo](https://github.com/MaJesTySA/JVM-JUC-Core/blob/master/src/jvm/SoftReferenceDemo.java)。

## 弱引用

需要用`Object.Reference.WeakReference`来显示创建。**无论内存够不够，GC的时候都回收**，也可以用在高速缓存上。详见[WeakReferenceDemo](https://github.com/MaJesTySA/JVM-JUC-Core/blob/master/src/jvm/WeakReferenceDemo.java)

### WeakHashMap

传统的`HashMap`就算`key==null`了，也不会回收键值对。但是如果是`WeakHashMap`，一旦内存不够用时，且`key==null`时，会回收这个键值对。详见[WeakHashMapDemo](https://github.com/MaJesTySA/JVM-JUC-Core/blob/master/src/jvm/WeakHashMapDemo.java)。

## 虚引用

软应用和弱引用可以通过`get()`方法获得对象，但是虚引用不行。虚引形同虚设，在任何时候都可能被GC，不能单独使用，必须配合**引用队列（ReferenceQueue）**来使用。设置虚引用的**唯一目的**，就是在这个对象被回收时，收到一个**通知**以便进行后续操作，有点像`Spring`的后置通知。详见[PhantomReferenceDemo](https://github.com/MaJesTySA/JVM-JUC-Core/blob/master/src/jvm/PhantomReferenceDemo.java)。

## 引用队列

弱引用、虚引用被回收后，会被放到引用队列里面，通过`poll`方法可以得到。关于引用队列和弱、虚引用的配合使用，见[ReferenceQueueDemo](https://github.com/MaJesTySA/JVM-JUC-Core/blob/master/src/jvm/ReferenceQueueDemo.java)。

# OutOfMemoryError

## StackOverflowError

栈满会抛出该错误。无限递归就会导致StackOverflowError，是`java.lang.Throwable`→`java.lang.Error`→`java.lang.VirtualMachineError`下的错误。详见[StackOverflowErrorDemo](https://github.com/MaJesTySA/JVM-JUC-Core/blob/master/src/jvm/StackOverflowErrorDemo.java)。

## OOM—Java head space

栈满会抛出该错误。详见[JavaHeapSpaceDemo](https://github.com/MaJesTySA/JVM-JUC-Core/blob/master/src/jvm/JavaHeapSpaceDemo.java)。

## OOM—GC overhead limit exceeded

这个错误是指：GC的时候会有“Stop the World"，STW越小越好，正常情况是GC只会占到很少一部分时间。但是如果用超过98%的时间来做GC，而且收效甚微，就会被JVM叫停。下例中，执行了多次`Full GC`，但是内存回收很少，最后抛出了`OOM:GC overhead limit exceeded`错误。详见[GCOverheadDemo](https://github.com/MaJesTySA/JVM-JUC-Core/blob/master/src/jvm/GCOverheadDemo.java)。

```java
[GC (Allocation Failure) [PSYoungGen: 2048K->496K(2560K)] 2048K->960K(9728K), 0.0036555 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[GC (Allocation Failure) [PSYoungGen: 2544K->489K(2560K)] 3008K->2689K(9728K), 0.0060306 secs] [Times: user=0.08 sys=0.00, real=0.01 secs] 
[GC (Allocation Failure) [PSYoungGen: 2537K->512K(2560K)] 4737K->4565K(9728K), 0.0050620 secs] [Times: user=0.00 sys=0.00, real=0.00 secs] 
[GC (Allocation Failure) [PSYoungGen: 2560K->496K(2560K)] 6613K->6638K(9728K), 0.0064025 secs] [Times: user=0.00 sys=0.00, real=0.01 secs] 
[Full GC (Ergonomics) [PSYoungGen: 2048K->860K(2560K)] [ParOldGen: 6264K->7008K(7168K)] 8312K->7869K(9728K), [Metaspace: 3223K->3223K(1056768K)], 0.1674947 secs] [Times: user=0.63 sys=0.00, real=0.17 secs] 
[Full GC (Ergonomics) [PSYoungGen: 2048K->2006K(2560K)] [ParOldGen: 7008K->7008K(7168K)] 9056K->9015K(9728K), [Metaspace: 3224K->3224K(1056768K)], 0.1048666 secs] [Times: user=0.45 sys=0.00, real=0.10 secs] 
[Full GC (Ergonomics) [PSYoungGen: 2047K->2047K(2560K)] [ParOldGen: 7082K->7082K(7168K)] 9130K->9130K(9728K), [Metaspace: 3313K->3313K(1056768K)], 0.0742516 secs] [Times: user=0.28 sys=0.00, real=0.07 secs] 
·······
[Full GC (Ergonomics) [PSYoungGen: 2047K->2047K(2560K)] [ParOldGen: 7084K->7084K(7168K)] 9132K->9132K(9728K), [Metaspace: 3313K->3313K(1056768K)], 0.0738461 secs] [Times: user=0.36 sys=0.02, real=0.07 secs] 
Exception in thread "main" [Full GC (Ergonomics) [PSYoungGen: 2047K->0K(2560K)] [ParOldGen: 7119K->647K(7168K)] 9167K->647K(9728K), [Metaspace: 3360K->3360K(1056768K)], 0.0129597 secs] [Times: user=0.11 sys=0.00, real=0.01 secs] 
java.lang.OutOfMemoryError: GC overhead limit exceeded
	at java.lang.Integer.toString(Integer.java:401)
	at java.lang.String.valueOf(String.java:3099)
	at jvm.GCOverheadDemo.main(GCOverheadDemo.java:12)
```

## OOM—GC Direct buffer memory

在写`NIO`程序的时候，会用到`ByteBuffer`来读取和存入数据。与Java堆的数据不一样，`ByteBuffer`使用`native`方法，直接在**堆外分配内存**。当堆外内存（也即本地物理内存）不够时，就会抛出这个异常。详见[DirectBufferMemoryDemo](https://github.com/MaJesTySA/JVM-JUC-Core/blob/master/src/jvm/DirectBufferMemoryDemo.java)。

## OOM—unable to create new native thread

在高并发应用场景时，如果创建超过了系统默认的最大线程数，就会抛出该异常。Linux单个进程默认不能超过1024个线程。**解决方法**要么降低程序线程数，要么修改系统最大线程数`vim /etc/security/limits.d/90-nproc.conf`。详见[UnableCreateNewThreadDemo](https://github.com/MaJesTySA/JVM-JUC-Core/blob/master/src/jvm/UnableCreateNewThreadDemo.java)

## OOM—Metaspace

元空间满了就会抛出这个异常。

# JVM垃圾收集器

## 四大垃圾收集算法

### 标记整理

![img](https://gitee.com/houyao123/my-resource/raw/master/img/GCbq.png)

![img](https://gitee.com/houyao123/my-resource/raw/master/img/GCbz.png)

### 标记清除

![img](https://gitee.com/houyao123/my-resource/raw/master/img/GCbq-20210103212409964.png)

![img](https://gitee.com/houyao123/my-resource/raw/master/img/GCbq2.png)

### 复制算法

![img](https://gitee.com/houyao123/my-resource/raw/master/img/GCfz.png)

![img](https://gitee.com/houyao123/my-resource/raw/master/img/GCfz2.png)

### 分代收集算法

准确来讲，跟前面三种算法有所区别。分代收集算法就是根据对象的年代，采用上述三种算法来收集。

1. 对于新生代：每次GC都有大量对象死去，存活的很少，常采用复制算法，只需要拷贝很少的对象。
2. 对于老年代：常采用标整或者标清算法。

## （Java8）四种垃圾收集器

Java 8可以将垃圾收集器分为四类。

### 串行收集器Serial

为单线程环境设计且**只使用一个线程**进行GC，会暂停所有用户线程，不适用于服务器。就像去餐厅吃饭，只有一个清洁工在打扫。

### 并行收集器Parrallel

使用**多个线程**并行地进行GC，会暂停所有用户线程，适用于科学计算、大数据后台，交互性不敏感的场合。多个清洁工同时在打扫。

### 并发收集器CMS

用户线程和GC线程同时执行（不一定是并行，交替执行），GC时不需要停顿用户线程，互联网公司多用，适用对响应时间有要求的场合。清洁工打扫的时候，也可以就餐。

### G1收集器

对内存的划分与前面3种很大不同，将堆内存分割成不同的区域，然后并发地进行垃圾回收。

## 默认垃圾收集器

### 默认收集器有哪些？

有`Serial`、`Parallel`、`ConcMarkSweep`（CMS）、`ParNew`、`ParallelOld`、`G1`。还有一个`SerialOld`，快被淘汰了。

### 查看默认垃圾修改器

使用`java -XX:+PrintCommandLineFlags`即可看到，Java 8默认使用`-XX:+UseParallelGC`。

```java
-XX:InitialHeapSize=132375936 -XX:MaxHeapSize=2118014976 -XX:+PrintCommandLineFlags -XX:+UseCompressedClassPointers -XX:+UseCompressedOops -XX:-UseLargePagesIndividualAllocation -XX:+UseParallelGC
```

## 七大垃圾收集器

### 体系结构（重要哪些老年代，哪些用在新生代）

`Serial`、`Parallel Scavenge`、`ParNew`用户回收新生代；`SerialOld`、`ParallelOld`、`CMS`用于回收老年代。而`G1`收集器，既可以回收新生代，也可以回收老年代。

![img](https://gitee.com/houyao123/my-resource/raw/master/img/GCqi.png)

连线表示可以搭配使用，红叉表示不推荐一同使用，比如新生代用`Serial`，老年代用`CMS`。

![img](https://gitee.com/houyao123/my-resource/raw/master/img/GCqi2-20210103212653484.png)

### Serial收集器

年代最久远，是`Client VM`模式下的默认新生代收集器，使用**复制算法**。**优点**：单个线程收集，没有线程切换开销，拥有最高的单线程GC效率。**缺点**：收集的时候会暂停用户线程。

使用`-XX:+UseSerialGC`可以显式开启，开启后默认使用`Serial`+`SerialOld`的组合。

![img](https://gitee.com/houyao123/my-resource/raw/master/img/serial.jpeg)

### ParNew收集器

也就是`Serial`的多线程版本，GC的时候不再是一个线程，而是多个，是`Server VM`模式下的默认新生代收集器，采用**复制算法**。

使用`-XX:+UseParNewGC`可以显式开启，开启后默认使用`ParNew`+`SerialOld`的组合。但是由于`SerialOld`已经过时，所以建议配合`CMS`使用。

![img](https://gitee.com/houyao123/my-resource/raw/master/img/parnew-20210103212738439.jpeg)

### Parallel Scavenge收集器

`ParNew`收集器仅在新生代使用多线程收集，老年代默认是`SerialOld`，所以是单线程收集。而`Parallel Scavenge`在新、老两代都采用多线程收集。`Parallel Scavenge`还有一个特点就是**吞吐量优先收集器**，可以通过自适应调节，保证最大吞吐量。采用**复制算法**。

使用`-XX:+UseParallelGC`可以开启， 同时也会使用`ParallelOld`收集老年代。其它参数，比如`-XX:ParallelGCThreads=N`可以选择N个线程进行GC，`-XX:+UseAdaptiveSizePolicy`使用自适应调节策略。

### SerialOld收集器

`Serial`的老年代版本，采用**标整算法**。JDK1.5之前跟`Parallel Scavenge`配合使用，现在已经不了，作为`CMS`的后备收集器。

### ParallelOld收集器

`Parallel`的老年代版本，JDK1.6之前，新生代用`Parallel`而老年代用`SerialOld`，只能保证新生代的吞吐量。JDK1.8后，老年代改用`ParallelOld`。

使用`-XX:+UseParallelOldGC`可以开启， 同时也会使用`Parallel`收集新生代。

### CMS收集器

并发标记清除收集器，是一种以获得**最短GC停顿为**目标的收集器。适用在互联网或者B/S系统的服务器上，这类应用尤其重视服务器的**响应速度**，希望停顿时间最短。是`G1`收集器出来之前的首选收集器。使用**标清算法**。在GC的时候，会与用户线程并发执行，不会停顿用户线程。但是在**标记**的时候，仍然会**STW**。

使用`-XX:+UseConcMarkSweepGC`开启。开启过后，新生代默认使用`ParNew`，同时老年代使用`SerialOld`作为备用。

![img](https://raw.githubusercontent.com/MaJesTySA/JVM-JUC-Core/master/imgs/cms.jpeg)

#### 过程

1. **初始标记**：只是标记一下GC Roots能直接关联的对象，速度很快，需要**STW**。
2. **并发标记**：主要标记过程，标记全部对象，和用户线程一起工作，不需要STW。
3. **重新标记**：修正在并发标记阶段出现的变动，需要**STW**。
4. **并发清除**：和用户线程一起，清除垃圾，不需要STW。

#### 优缺点

**优点**：停顿时间少，响应速度快，用户体验好。

**缺点**：

1. 对CPU资源非常敏感：由于需要并发工作，多少会占用系统线程资源。
2. 无法处理浮动垃圾：由于标记垃圾的时候，用户进程仍然在运行，无法有效处理新产生的垃圾。
3. 产生内存碎片：由于使用**标清算法**，会产生内存碎片。

### G1收集器

`G1`收集器与之前垃圾收集器的一个显著区别就是——之前收集器都有三个区域，新、老两代和元空间。而G1收集器只有G1区和元空间。而G1区，不像之前的收集器，分为新、老两代，而是一个一个Region，每个Region既可能包含新生代，也可能包含老年代，只在逻辑上分新生代，老年代。

`G1`收集器既可以提高吞吐量，又可以减少GC时间。最重要的是**STW可控**，增加了预测机制，让用户指定停顿时间。

使用`-XX:+UseG1GC`开启，还有`-XX:G1HeapRegionSize=n`、`-XX:MaxGCPauseMillis=n`等参数可调。

#### 特点

1. **并行和并发**：充分利用多核、多线程CPU，尽量缩短STW。
2. **分代收集**：虽然还保留着新、老两代的概念，但物理上不再隔离，而是融合在Region中。
3. **空间整合**：`G1`整体上看是**标整**算法，在局部看又是**复制算法**，不会产生内存碎片。
4. **可预测停顿**：用户可以指定一个GC停顿时间，`G1`收集器会尽量满足。

#### 过程

与`CMS`类似。

1. 初始标记。
2. 并发标记。
3. 最终标记。
4. 筛选回收。

# 附—Linux相关指令

## top

主要查看`%CPU`、`%MEM`，还有`load average`。`load average`后面的三个数字，表示系统1分钟、5分钟、15分钟的平均负载值。如果三者平均值高于0.6，则复杂比较高了。当然，用`uptime`也可以查看。

## vmstat

查看进程、内存、I/O等多个系统运行状态。2表示每两秒采样一次，3表示一共采样3次。`procs`的`r` (run

)表示运行和等待CPU时间片的进程数，原则上1核CPU不要超过2。`b` (block)是等待资源的进程数，比如磁盘I/O、网络I/O等。

```shell
[root@ ~]# vmstat -n 2 3
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 2  0      0 173188 239748 1362628    0    0     0     3   17    8  0  0 99  0  0
 0  0      0 172800 239748 1362636    0    0     0     0  194  485  1  1 99  0  0
 1  0      0 172800 239748 1362640    0    0     0     0  192  421  1  1 99  0  0
```

## pidstat

pidstat -p xxx(pid) 查看某个进程的运行信息。

## free

查看内存信息。

## df

查看磁盘信息。

## iostat

iostat -xdk -2 -3 （查看磁盘io）

pidstat -d 2 -p pid. (查看磁盘io)

查看磁盘I/O信息。比如有时候MySQL在查表的时候，会占用大量磁盘I/O，体现在该指令的`%util`字段很大。对于死循环的程序，CPU占用固然很高，但是磁盘I/O不高。

## ifstat

查看网络I/O信息，需要安装。

# CPU占用过高原因定位

先用`top`找到CPU占用最高的进程，然后用`ps -mp pid -o THREAD,tid,time`，得到该**进程**里面占用最高的**线程**。这个线程是10进制的，将其转成16进制，然后用`jstack pid | grep tid`可以定位到具体哪一行导致了占用过高。

ps -mp pid -o THREAD,tid,time

-m 显示所有线程

-p pid 进程使用的cpu时间

-o 后是用户自定义格式

转16进制 ： printf “%x\n“ pid

Top-> ps -ef|grep pid -> ps -mp pid -o THREAD,tid,time -> jstack pid |grep tid

# JVM性能调优和监控工具

## jps

Java版的`ps -ef`查看所有JVM进程。

## jstack

查看JVM中运行线程的状态，比较重要。可以定位CPU占用过高位置，定位死锁位置。

## jinfo/jstat

`jinfo`查看JVM的运行环境参数，比如默认的JVM参数等。`jstat`统计信息监视工具。

## jmap

JVM内存映像工具。

## JVM 的主要组成部分及其作用

- 类加载器（ClassLoader）
- 运行时数据区（Runtime Data Area）
- 执行引擎（Execution Engine）
- 本地库接口（Native Interface）

组件的作用： 首先通过类加载器（ClassLoader）会加载类文件到内存，Class loader只管加载，只要符合文件结构就加载。运行时数据区（Runtime Data Area)是jvm的重点，我们所有所写的程序都被加载到这里，之后才开始运行。而字节码文件只是 JVM 的一套指令集规范，并不能直接交个底层操作系统去执行，因此需要特定的命令解析器执行引擎（Execution Engine），将字节码翻译成底层系统指令，再交由 CPU 去执行，而这个过程中需要调用其他语言的本地库接口（Native Interface）来融合不同的语言为java所用,从而实现整个程序的功能。

## JVM 运行时数据区⭐

- 程序计数器
  指向当前线程执行的字节码指令的地址（ 行号 ）。 这样做的**用处是多线程操作时， 挂起的线程在重新激活后能够知道上次执行的位置。**
- 虚拟机栈
  存储当前线程执行方法时所需要的数据，指令，返回地址。因此一个线程独享一块虚拟机栈 ，栈中内存的单位是栈帧。 每个栈帧中都拥有：局部变量表、操作数栈、动态链接、方法出口信息。
- 本地方法栈
  本地方法栈为虚拟机使用到的 Native 方法服务。
- 堆
  唯一目的就是存放对象实例，几乎所有的对象实例都在这里分配内存。 详见堆内存模型。
- 方法区
  用于存放已被加载的类信息、常量、静态变量、即时编译器编译后的代码等数据。方法区是一个 JVM 规范，永久代与元空间都是其一种实现方式。

### 堆

堆内存模型：

[![img](https://gitee.com/houyao123/my-resource/raw/master/img/JVM.png)](https://github.com/lvminghui/Java-Notes/blob/master/docs/imgs/JVM.png)

**此内存区域的唯一目的就是存放对象实例，几乎所有的对象实例以及数组都在这里分配内存。**

## 堆和栈的区别

1. 栈内存存储的是局部变量而堆内存存储的是实体；
2. 栈内存的更新速度要快于堆内存，因为局部变量的生命周期很短；
3. 栈内存存放的变量生命周期一旦结束就会被释放，而堆内存存放的实体会被垃圾回收机制不定时的回收。、

### 分代回收

HotSpot JVM把年轻代分为了三部分：1个Eden区和2个Survivor区（分别叫from和to）。一般情况下，新创建的对象都会被分配到Eden区(一些大对象特殊处理),这些对象经过第一次Minor GC后，如果仍然存活，将会被移到Survivor区。对象在Survivor区中每熬过一次Minor GC，年龄就会增加1岁，当它的年龄增加到一定程度时，就会被移动到年老代中。

因为年轻代中的对象基本都是朝生夕死的，所以在年轻代的垃圾回收算法使用的是复制算法，复制算法的基本思想就是将内存分为两块，每次只用其中一块，当这一块内存用完，就将还活着的对象复制到另外一块上面。复制算法不会产生内存碎片。

在GC开始的时候，对象只会存在于Eden区和名为“From”的Survivor区，Survivor区“To”是空的。紧接着进行GC，Eden区中所有存活的对象都会被复制到“To”，而在“From”区中，仍存活的对象会根据他们的年龄值来决定去向。年龄达到一定值(年龄阈值，可以通过-XX:MaxTenuringThreshold来设置)的对象会被移动到年老代中，没有达到阈值的对象会被复制到“To”区域。经过这次GC后，Eden区和From区已经被清空。这个时候，“From”和“To”会交换他们的角色，也就是新的“To”就是上次GC前的“From”，新的“From”就是上次GC前的“To”。不管怎样，都会保证名为To的Survivor区域是空的。Minor GC会一直重复这样的过程，直到“To”区被填满，“To”区被填满之后，会将所有对象移动到年老代中。

## 什么是双亲委派模型？⭐

双亲委派的意思是如果一个类加载器需要加载类，那么首先它会把这个类请求委派给父类加载器去完成，每一层都是如此。一直递归到顶层，当父加载器无法完成这个请求时，子类才会尝试去加载。

## 双亲委派机制的作用

1、防止重复加载同一个`.class`。通过委托去向上面问一问，加载过了，就不用再加载一遍。保证数据安全。 2、保证核心`.class`不能被篡改。通过委托方式，不会去篡改核心`.class`，不同的加载器加载同一个`.class`也不是同一个`Class`对象。这样保证了`Class`执行安全。

## 对象的创建过程？

#### Step1:类加载检查

虚拟机遇到一条 new 指令时，首先将去检查这个指令的参数是否能在常量池中定位到这个类的符号引用，并且检查这个符号引用代表的类是否已被加载过、解析和初始化过。如果没有，那必须先执行相应的**类加载过程**。

#### Step2:分配内存

在**类加载检查**通过后，接下来虚拟机将为新生对象**分配内存**。对象所需的内存大小在类加载完成后便可确定，为对象分配空间的任务等同于把一块确定大小的内存从 Java 堆中划分出来。**分配方式**有 **“指针碰撞”** 和 **“空闲列表”** 两种。

#### Step3:初始化零值

内存分配完成后，虚拟机需要将分配到的内存空间都初始化为零值（不包括对象头），这一步操作保证了对象的实例字段在 Java 代码中可以不赋初始值就直接使用，程序能访问到这些字段的数据类型所对应的零值。

#### Step4:设置对象头

初始化零值完成之后，**虚拟机要对对象进行必要的设置**，例如这个对象是哪个类的实例、如何才能找到类的元数据信息、对象的哈希码、对象的 GC 分代年龄等Z/信息。 **这些信息存放在对象头中。**

#### Step5:执行 init 方法

在上面工作都完成之后，从虚拟机的视角来看，一个新的对象已经产生了，但从 Java 程序的视角来看，对象创建才刚开始，方法还没有执行，所有的字段都还为零。所以一般来说，执行 new 指令之后会接着执行方法，把对象按照程序员的意愿进行初始化，这样一个真正可用的对象才算完全产生出来。

## 类加载的执行过程？

1. 加载：根据查找路径找到相应的 class 文件然后导入；
2. 检查：检查加载的 class 文件的正确性；
3. 准备：给类中的静态变量分配内存空间；
4. 解析：虚拟机将常量池中的符号引用替换成直接引用的过程。符号引用就理解为一个标示，而在直接引用直接指向内存中的地址；
5. 初始化：对静态变量和静态代码块执行初始化工作。

## 怎么判断对象是否可以被回收？

- 引用计数器：为每个对象创建一个引用计数，有对象引用时计数器 +1，引用被释放时计数 -1，当计数器为 0 时就可以被回收。它有一个缺点不能解决循环引用的问题；
- 可达性分析：从 GC Roots 开始向下搜索，搜索所走过的路径称为引用链。当一个对象到 GC Roots 没有任何引用链相连时，则证明此对象是可以被回收的。

## Java 中都有哪些引用类型？

- 强引用
- 软引用
- 弱引用
- 虚引用

## 常见的垃圾回收机制⭐

1. 引用计数法：引用计数法是一种简单但速度很慢的垃圾回收技术。每个对象都含有一个引用计数器,当有引用连接至对象时,引用计数加1。当引用离开作用域或被置为null时,引用计数减1。虽然管理引用计数的开销不大,但这项开销在整个程序生命周期中将持续发生。垃圾回收器会在含有全部对象的列表上遍历,当发现某个对象引用计数为0时,就释放其占用的空间。
2. 可达性分析算法：这个算法的基本思路就是通过一系列的称为“GC Roots”的对象作为起始点，从这些节点开始向下搜索，搜索所走过的路径称为引用链，当一个对象到GC Roots没有任何引用链相连（用图论的话来说，就是从GC Roots到这个对象不可达）时，则证明此对象是不可用的。

## jvm 有哪些垃圾回收算法⭐

- 停止-复制：先暂停程序的运行,然后将所有存活的对象从当前堆复制到另一个堆,没有被复制的对象全部都是垃圾。当对象被复制到新堆时,它们是一个挨着一个的,所以新堆保持紧凑排列,然后就可以按前述方法简单,直接的分配了。缺点是一浪费空间,两个堆之间要来回倒腾,二是当程序进入稳定态时,可能只会产生极少的垃圾,甚至不产生垃圾,尽管如此,复制式回收器仍会将所有内存自一处复制到另一处。
- 标记-清除：同样是从堆栈和静态存储区出发,遍历所有的引用,进而找出所有存活的对象。每当它找到一个存活的对象,就会给对象一个标记,这个过程中不会回收任何对象。只有全部标记工作完成的时候,清理动作才会开始。在清理过程中,没有标记的对象会被释放,不会发生任何复制动作。所以剩下的堆空间是不连续的,垃圾回收器如果要希望得到连续空间的话,就得重新整理剩下的对象。
- 标记-整理：它的第一个阶段与标记/清除算法是一模一样的，均是遍历GC Roots，然后将存活的对象标记。移动所有存活的对象，且按照内存地址次序依次排列，然后将末端内存地址以后的内存全部回收。因此，第二阶段才称为整理阶段。
- 分代收集算法：把Java堆分为新生代和老年代，然后根据各个年代的特点采用最合适的收集算法。新生代中，对象的存活率比较低，所以选用复制算法，老年代中对象存活率高且没有额外空间对它进行分配担保，所以使用“标记-清除”或“标记-整理”算法进行回收。

### Minor GC和Full GC触发条件⭐

- Minor GC触发条件：当Eden区满时，触发Minor GC。
- Full GC触发条件：

1. 调用System.gc时，系统建议执行Full GC，但是不必然执行
2. 老年代空间不足
3. 方法区空间不足
4. 通过Minor GC后进入老年代的平均大小大于老年代的可用内存
5. 由Eden区、From Space区向To Space区复制时，对象大小大于To Space可用内存，则把该对象转存到老年代，且老年代的可用内存小于该对象大小

### GC中Stop the world（STW）⭐

在执行垃圾收集算法时，Java应用程序的其他所有除了垃圾收集收集器线程之外的线程都被挂起。此时，系统只能允许GC线程进行运行，其他线程则会全部暂停，等待GC线程执行完毕后才能再次运行。这些工作都是由虚拟机在后台自动发起和自动完成的，是在用户不可见的情况下把用户正常工作的线程全部停下来，这对于很多的应用程序，尤其是那些对于实时性要求很高的程序来说是难以接受的。

但不是说GC必须STW,你也可以选择降低运行速度但是可以并发执行的收集算法，这取决于你的业务。

### 哪些对象可以作为GC Roots

1. 虚拟机栈（栈帧中的本地变量表）中引用的对象。
2. 方法区中类静态属性引用的对象。
3. 方法区中常量引用的对象。
4. 本地方法栈中JNI（即一般说的Native方法）引用的对象。

### JVM锁优化和膨胀过程⭐

1. 自旋锁：自旋锁其实就是在拿锁时发现已经有线程拿了锁，自己如果去拿会阻塞自己，这个时候会选择进行一次忙循环尝试。也就是不停循环看是否能等到上个线程自己释放锁。自适应自旋锁指的是例如第一次设置最多自旋10次，结果在自旋的过程中成功获得了锁，那么下一次就可以设置成最多自旋20次。
2. 锁粗化：虚拟机通过适当扩大加锁的范围以避免频繁的拿锁释放锁的过程。
3. 锁消除：通过逃逸分析发现其实根本就没有别的线程产生竞争的可能（别的线程没有临界量的引用），或者同步块内进行的是原子操作，而“自作多情”地给自己加上了锁。有可能虚拟机会直接去掉这个锁。
4. 偏向锁：在大多数的情况下，锁不仅不存在多线程的竞争，而且总是由同一个线程获得。因此为了让线程获得锁的代价更低引入了偏向锁的概念。偏向锁的意思是如果一个线程获得了一个偏向锁，如果在接下来的一段时间中没有其他线程来竞争锁，那么持有偏向锁的线程再次进入或者退出同一个同步代码块，不需要再次进行抢占锁和释放锁的操作。
5. 轻量级锁：当存在超过一个线程在竞争同一个同步代码块时，会发生偏向锁的撤销。当前线程会尝试使用CAS来获取锁，当自旋超过指定次数(可以自定义)时仍然无法获得锁，此时锁会膨胀升级为重量级锁。
6. 重量级锁：重量级锁依赖对象内部的monitor锁来实现，而monitor又依赖操作系统的MutexLock（互斥锁）。当系统检查到是重量级锁之后，会把等待想要获取锁的线程阻塞，被阻塞的线程不会消耗CPU，但是阻塞或者唤醒一个线程，都需要通过操作系统来实现。

## jvm 有哪些垃圾回收器？

- Serial：最早的单线程串行垃圾回收器。
- Serial Old：Serial 垃圾回收器的老年版本，同样也是单线程的，可以作为 CMS 垃圾回收器的备选预案。
- ParNew：是 Serial 的多线程版本。
- Parallel 和 ParNew 收集器类似是多线程的，但 Parallel 是吞吐量优先的收集器，可以牺牲等待时间换取系统的吞吐量。
- Parallel Old 是 Parallel 老生代版本，Parallel 使用的是复制的内存回收算法，Parallel Old 使用的是标记-整理的内存回收算法。
- CMS：一种以获得最短停顿时间为目标的收集器，非常适用 B/S 系统。
- G1：一种兼顾吞吐量和停顿时间的 GC 实现，是 JDK 9 以后的默认 GC 选项。
  **新生代垃圾回收器和老生代垃圾回收器都有哪些？有什么区别？**
- 新生代回收器：Serial、ParNew、Parallel Scavenge
- 老年代回收器：Serial Old、Parallel Old、CMS
- 整堆回收器：G1

新生代垃圾回收器一般采用的是复制算法，复制算法的优点是效率高，缺点是内存利用率低；老年代回收器一般采用的是标记-整理的算法进行垃圾回收。

## 详细介绍一下 CMS 垃圾回收器⭐

CMS 是英文 Concurrent Mark-Sweep 的简称，是以牺牲吞吐量为代价来获得最短回收停顿时间的垃圾回收器。对于要求服务器响应速度的应用上，这种垃圾回收器非常适合。在启动 JVM 的参数加上“-XX:+UseConcMarkSweepGC”来指定使用 CMS 垃圾回收器。

CMS 使用的是标记-清除的算法实现的，所以在 gc 的时候回产生大量的内存碎片，当剩余内存不能满足程序运行要求时，系统将会出现 Concurrent Mode Failure，临时 CMS 会采用 Serial Old 回收器进行垃圾清除，此时的性能将会被降低。

### G1和CMS的比较

1. CMS收集器是获取最短回收停顿时间为目标的收集器，因为CMS工作时，GC工作线程与用户线程可以并发执行，以此来达到降低手机停顿时间的目的（只有初始标记和重新标记会STW）。但是CMS收集器对CPU资源非常敏感。在并发阶段，虽然不会导致用户线程停顿，但是会占用CPU资源而导致引用程序变慢，总吞吐量下降。
2. CMS仅作用于老年代，是基于标记清除算法，所以清理的过程中会有大量的空间碎片。
3. CMS收集器无法处理浮动垃圾，由于CMS并发清理阶段用户线程还在运行，伴随程序的运行自热会有新的垃圾不断产生，这一部分垃圾出现在标记过程之后，CMS无法在本次收集中处理它们，只好留待下一次GC时将其清理掉。
4. G1是一款面向服务端应用的垃圾收集器，适用于多核处理器、大内存容量的服务端系统。G1能充分利用CPU、多核环境下的硬件优势，使用多个CPU（CPU或者CPU核心）来缩短STW的停顿时间，它满足短时间停顿的同时达到一个高的吞吐量。
5. 从JDK 9开始，G1成为默认的垃圾回收器。当应用有以下任何一种特性时非常适合用G1：Full GC持续时间太长或者太频繁；对象的创建速率和存活率变动很大；应用不希望停顿时间长(长于0.5s甚至1s)。
6. G1将空间划分成很多块（Region），然后他们各自进行回收。堆比较大的时候可以采用，采用复制算法，碎片化问题不严重。整体上看属于标记整理算法,局部(region之间)属于复制算法。
7. G1 需要记忆集 (具体来说是卡表)来记录新生代和老年代之间的引用关系，这种数据结构在 G1 中需要占用大量的内存，可能达到整个堆内存容量的 20% 甚至更多。而且 G1 中维护记忆集的成本较高，带来了更高的执行负载，影响效率。所以 CMS 在小内存应用上的表现要优于 G1，而大内存应用上 G1 更有优势，大小内存的界限是6GB到8GB。

### i++操作的字节码指令⭐

1. 将int类型常量加载到操作数栈顶
2. 将int类型数值从操作数栈顶取出，并存储到到局部变量表的第1个Slot中
3. 将int类型变量从局部变量表的第1个Slot中取出，并放到操作数栈顶
4. 将局部变量表的第1个Slot中的int类型变量加1
5. 表示将int类型数值从操作数栈顶取出，并存储到到局部变量表的第1个Slot中，即i中

## 说一下 jvm 调优的工具

JDK 自带了很多监控工具，都位于 JDK 的 bin 目录下，其中最常用的是 jconsole 和 jvisualvm 这两款视图监控工具。

- jconsole：用于对 JVM 中的内存、线程和类等进行监控；
- jvisualvm：JDK 自带的全能分析工具，可以分析：内存快照、线程快照、程序死锁、监控内存的变化、gc 变化等。

**常用的 jvm 调优的参数都有哪些？**

- -Xms2g：初始化推大小为 2g；
- -Xmx2g：堆最大内存为 2g；
- -XX:NewRatio=4：设置年轻的和老年代的内存比例为 1:4；
- -XX:SurvivorRatio=8：设置新生代 Eden 和 Survivor 比例为 8:2；
- –XX:+UseParNewGC：指定使用 ParNew + Serial Old 垃圾回收器组合；
- -XX:+UseParallelOldGC：指定使用 ParNew + ParNew Old 垃圾回收器组合；
- -XX:+UseConcMarkSweepGC：指定使用 CMS + Serial Old 垃圾回收器组合；
- -XX:+PrintGC：开启打印 gc 信息；
- -XX:+PrintGCDetails：打印 gc 详细信息。

## TODO：

- CMS GC回收分为哪几个阶段？分别做了什么事情？
- CMS有哪些重要参数？
- Concurrent Model Failure和ParNew promotion failed什么情况下会发生？
- CMS的优缺点？
- 有做过哪些GC调优？
- 为什么要划分成年轻代和老年代？
- 年轻代为什么被划分成eden、survivor区域？
- 年轻代为什么采用的是复制算法？
- 老年代为什么采用的是标记清除、标记整理算法
- 什么情况下使用堆外内存？要注意些什么？
- 堆外内存如何被回收？