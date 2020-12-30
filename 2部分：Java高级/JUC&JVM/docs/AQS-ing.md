AQS 相关



* ReadWriteLock读写之间互斥么 读写锁 ， 使用场景可分为 读读、读写，写写 ，除了读读是共享的，其他都是互斥的 接下来讨论 互斥锁 和 同步所 理解 AQS 和 CAS
* AQS 和 CAS 原理 AQS： 抽象队列同步器 CAS： 一定要配合 volatile 使用 ， CAS虽然高效解决了 原子操作问题，但仍存在三个问题 循环时间长开销很大 ABA问题 只能保证一个共享变量的原子操作
* Semaphore 拿到执行权限的线程之间是否互斥

    Semaphore 、CountDownlatch、CyclicBarrer、Exchanger 为java 并发编程的4 个辅助类
    面试常问到 CountDownLatch 和 CyclicBarrer 之间的区别
    Semaphore 可有多把锁，可允许多个线程同时拥有执行权限、这些有执行权限的线程
    如果并发访问同一对象会有线程安全问题。
* 写一个你认为最好的单例模式

    懒汉、
    饿汉、
    静态内部类、
    枚举、
    双检索 ： 多线程保证线程安全，为什么两次检索，光是双检索会有什么问题，
        对象在定义的时候加上 volatile 关键字 ， 继续 原子性、可见性、java 内存模型、类的加载过程
最好的就是饿汉模式，简答+提前初始化~~当然利用类加载时的加锁实现的单例也是可以的。
* B树 和 B+树 是解决什么样问题的，怎么演化过啊里， 之间区别

B树和B+树解决了二叉查找树的深度过大而造成磁盘I/O读写过于频繁的问题。B+数的非叶子节点存索引，因此能以更低的 IO 读入索引信息。
* 写一个生产者、消费者模式

用上面的 Semaphore 就可以，lock + condition也行。
* 写一个死锁


* cpu 100% 怎么定位

Top 获取高占用 CPU 进程 id， 通过ps -Hp pid 获取pid，转为16进制后把 Java 的线程dump一份搜一下就能搜到堆栈信息
* String a=“ab” 和 String b=“a”+“b” ，a==b 是否相等， 为什么

    附加问题： new 一个对象赋给变量， 问创建类几个对象
True 字符串常量
* int a = 1 , 是原子性操作吗，

    是原子性操作
* 可以用for循环直接删除 ArrayList的特定元素吗？ 可能会出现什么问题，怎么解决。

不可以，用迭代器解决
* 新的任务交到线程池， 线程池是怎么处理的

取决于线程池的配置~~~
    第一步：线程池判断核心线程池里面的线程是否都在执行任务，如果不是创建一个新的工作线程执行任务， 如果是执行第二步
    第二步：线程池判断工作队列是否已经满， 如果不满则 将新的任务存储到工作队列进行等待，如果满了 执行第三步
    第三步：线程池判断线程池的线程是否都在工作状态，如果不是创建一个新的工作线程来执行， 如果满了 交给饱和策略来处理
* synchronized的 底层实现原理

关键词：monitorenter + monitorexit, ACC_SYNCHRONIZED, markword + lock record，自旋/轻量级锁/重量级锁，CAS。
* valatile 作用，指令重排相关

    有两点作用：
    1. 多线程主要围绕可见行、原子性两个特性展开， volatile 修饰的变量保证了其在线程之间的可见行， 即每次读到volatile 的对象时 一定是最新的数据
    2. java 的jvm为了更好的性能，在Java底层会对指令进行重排， 多线程下会出现意想不到的问题，volatile会禁止重排，当然也一定程序降低了代码执行效率
* AOP 和 IOC原理

    AOP 面向切面，是面向对象的补充， 由代理模式实现（动态代理、静态代理  spring使用的是动态代理）
    IOC 控制反转，是一种bean 的管理方式，将对象的控制权交给spring框架， 使用对象时 无需创建 直接使用
* Spring怎么解决循环依赖的问题 

    什么是循环依赖、怎么检测出循环依赖、spring 循环依赖有几种方式、 使用基于setter的循环依赖为什么不会出现问题、 Bean的生命周期
 	先设置引用，然后延迟初始化
* dispatchServlet 怎么分发任务的

根据 Mapping Registry做匹配，然后用反射做任务分发。
* mysql 给离散度低的字段建立索引会出现什么问题， 具体说下原因

重复性较强的字段不适合添加索引， mysql 给离散度低的字段建立索引，比如性别，在以性别为条件时查询会更慢
给离散度低的字段建立索引无法达到快速定位缩小行范围目的，考虑下 B+树的的结构。
hashMap 底层实现原理， 既然有hash排位，为什么还需要使用equals 方法，作用是什么
多线程
*反射，队列，锁*

```
1.反射: 
Field(Method) Field[] field = className.class.getDeclaredFields(); 
for(Field fe:field){ 
  fe.getName()......…… 
}
Method value = message.getClass().getMethod("getId"); 
2.队列:
BlockQueue BlockingQueue<> smsBlock = new ArrayBlockingQueue<>(100); 
3.计时锁:  
CountDownLatch CountDownLatch  testLock = new CountDownLatch(1); 
testLock.await(); 
testLock.countDown();
```