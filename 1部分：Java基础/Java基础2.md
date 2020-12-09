## 基础篇

* 类型转换、类型比较 前先做检查 (instanceof) 

* String底层：char[ ] 数组

* Restful ： 设计风格，不是标准是约束条件， 基于这个风格设计的软件更简洁、更有层次，更易于实现缓存等机制。

* 一个汉字：utf-8 一个汉字占三字节，GBK 一个汉字两个字节。 一个字符占两个字节， int 4， long、double 8

* 作用域  当前类 同包 子类 其他： public - protected - default - private 

* **接口与抽象类的区别：**接口：Interface、多继承 、完全抽象没有方法和构造器、只能public修饰符

  抽象类：Abstract 、单继承多实现、可以有默认方法和构造器、所有修饰符

* **java序列化：** java对象转为字节序列的过程，实现 Serializable 接口才可被序列化

* 可以用for循环直接删除ArrayList的特定元素吗？可能会出现什么问题？怎样解决？    
  	异常或漏删元素，ArrayList底层结构是数组类型，数组的特点是删除其中某个元素时，后面的所有元素索引都会前移，此时for循环的指针却会下移，因此会略过下一个元素。解决方法可以是删除将指针回调一次，或者使用迭代器遍历。

* volatile作用，指令重排相关？  
      当一个变量被 volatile修饰时，任何线程对它的写操作都会立即刷新到主内存中（堆），并且会强制让缓存线程中的数据清空，必须从主内存重新读取最新数据。volatile可以防止指令优化，保证业务的正确性。比如双重懒加载的单例模式，使用这个保证了执行顺序，否则就有可能拿到未初始化的单例对象。   

* 反射讲一讲，主要是概念,都在哪需要反射机制，反射的性能，如何优化?  
      是在运行状态中，对于任意的一个类，都能够知道这个类的所有属性和方法，对任意一个对象都能够通过反射机制调用一个类的任意方法，这种动态获取类信息及动态调用类对象方法的功能称为java的反射机制。  
  反射的作用：
  1、动态地创建类的实例，将类绑定到现有的对象中，或从现有的对象中获取类型
  2、应用程序需要在运行时从某个特定的程序集中载入一个特定的类  

* **值类型和引用类型的区别:**

  值类型(8个基本类型):4整,int,byte,long,short.2浮点,float,double  1真值类型 boolean 1编码: char

  引用类型:String,object,class,接口,数组) 其中String是final类不可被继承。引用类型，表示你操作的数据是同一个,当传一个参数给另一个方法时,你在另一个方法中改变这个变量的值,那么调用这个方法是所传入的变量的值也将改变

* **String s = new String("abc");创建了几个 String Object?** ，两个  new String() 、 abc

* **==和equals的区别：** == ：内存地址,比较指针操作, equals：比较对象的内容是否相等适用于所有对象。

* **hashCode方法的作用：** 返回对象存储的物理地址（hash算法），区分不同的对象，减少equals调用

* **String/StringBuffer/StringBuilder的区别，扩展再问他们的实现：** StringBuffer 线程安全、值可变 

* 类的加载机制：分为三个步骤：1装载（二进制），2 验证、准备（分配内存）、解析 ，3 初始化（赋初始值）

* 6种触发初始化：1.new 2.访问静态变量 3.调用静态方法 4.反射 5.初始化一个类的子类 6.jvm启动标明的启动类

* 类的实例化四中方法：new、反射、clone、反序列化

* **加载器：ClassLoader**

  > 1. Bootstrap ClassLoader（引导类加载器）: 负责加载$JAVA_HOME中jre/lib/rt.jar里所有的class，由C++实现，不是ClassLoader子类
  > 2. Extension ClassLoader（扩展类加载器）: 负责加载java平台中扩展功能的一些jar包，包括$JAVA_HOME中		  jre/lib/*.jar或-Djava.ext.dirs指定目录下的jar包
  > 3. App ClassLoader（系统类加载器）: 负责记载classpath中指定的jar包及目录中class
  > 4. Custom ClassLoader（自定义加载器）
  >
  > 加载过程中会先检查类是否被已加载，**检查顺序是自底向上**，从Custom ClassLoader到BootStrap ClassLoader逐层检查，只要某个classloader已加载就视为已加载此类，保证此类只所有ClassLoader加载一次。**而加载的顺序是自顶向下**，也就是由上层来逐层尝试加载此类。

Java面向对象：

初级：类 ，对象，集成封装和多态

中级：面向对象的开发优势， 高内聚 低耦合

高级：设计模式的角度，面向对象开发的优势，劣势

IntegerCache： -128～127 自动装箱

StringBuffer  的 reverse（） 方法， substring()

n=n&(n-1) ,  n&(n-1) = 0 是2的幂数

索引区别（hash：等值查、B+树：区间、最左前置原则、排序。。）

private 修饰的方法可以通过反射访问， 那private的意义是什么呢<br>

​    a. java的 private 不是为类绝对安全设计的，更多是对用户常规使用Java的一种约束

​    b. 从外部常规调用时，能清晰看到类的结构

java 类初始化顺序

```
（派生类 继承 基类）
基类   静态代码块  静态成员   （并列优先，按代码中出现的顺序）
派生类  静态代码块  静态成员
基类   普通代码块  普通成员字段
基类   构造器
派生类  普通代码块 普通成员字段
派生类  构造器
```

* 对方法区 和 永久区的理解，以及他们之间的关系

  方法区是JVM 里面的规范要求， 永久区是Hotspot 虚拟机对方法区的具体实现方式 ，类似的有JDK1.8 引入的元空间

* 局部变量使用前需要显式的赋值，否则编译不通过， 为什么这么设计 

  这样设计是一种约束，尽量大的程度减少使用者 犯错的机率

  假如局部变量可以使用默认值，如果忘记赋值，会出现不可预期的情况

* ReadWriteLock读写之间互斥么

  读写锁 ， 使用场景可分为 读读、读写，写写 ，除了读读是共享的，其他都是互斥的

  接下来讨论 互斥锁 和 同步所 理解  AQS 和 CAS

* AQS 和 CAS 原理

  AQS： 抽象队列同步器

  CAS： 一定要配合 volatile 使用 ， CAS虽然高效解决了 原子操作问题，但仍存在三个问题

  循环时间长开销很大

  ABA问题

  只能保证一个共享变量的原子操作

* Semaphore 拿到执行权限的线程之间是否互斥

​    Semaphore 、CountDownlatch、CyclicBarrer、Exchanger 为java 并发编程的4 个辅助类

​    面试常问到 CountDownLatch 和 CyclicBarrer 之间的区别

​    Semaphore 可有多把锁，可允许多个线程同时拥有执行权限、这些有执行权限的线程

​    如果并发访问同一对象会有线程安全问题。

* 写一个你认为最好的单例模式

​    懒汉、

​    饿汉、

​    静态内部类、

​    枚举、

​    双检索 ： 多线程保证线程安全，为什么两次检索，光是双检索会有什么问题，

​        对象在定义的时候加上 volatile 关键字 ， 继续 原子性、可见性、java 内存模型、类的加载过程

最好的就是饿汉模式，简答+提前初始化~~当然利用类加载时的加锁实现的单例也是可以的。

* B树 和 B+树 是解决什么样问题的，怎么演化过啊里， 之间区别

B树和B+树解决了二叉查找树的深度过大而造成磁盘I/O读写过于频繁的问题。B+数的非叶子节点存索引，因此能以更低的 IO 读入索引信息。

* 写一个生产者、消费者模式

用上面的 Semaphore 就可以，lock + condition也行。

* 写一个死锁

 

* cpu 100% 怎么定位

top 获取高占用 CPU 进程 id， 通过ps -Hp pid 获取pid，转为16进制后把 Java 的线程dump一份搜一下就能搜到堆栈信息

* String a=“ab” 和 String b=“a”+“b” ，a==b 是否相等， 为什么

​    附加问题： new 一个对象赋给变量， 问创建类几个对象

true 字符串常量

* int a = 1 , 是原子性操作吗，

​    是原子性操作

* 可以用for循环直接删除 ArrayList的特定元素吗？ 可能会出现什么问题，怎么解决。

不可以，用迭代器解决

* 新的任务交到线程池， 线程池是怎么处理的

取决于线程池的配置~~~

​    第一步：线程池判断核心线程池里面的线程是否都在执行任务，如果不是创建一个新的工作线程执行任务， 如果是执行第二步

​    第二步：线程池判断工作队列是否已经满， 如果不满则 将新的任务存储到工作队列进行等待，如果满了 执行第三步

​    第三步：线程池判断线程池的线程是否都在工作状态，如果不是创建一个新的工作线程来执行， 如果满了 交给饱和策略来处理

* synchronized的 底层实现原理

关键词：monitorenter + monitorexit, ACC_SYNCHRONIZED, markword + lock record，自旋/轻量级锁/重量级锁，CAS。

* valatile 作用，指令重排相关

​    有两点作用：

​    1. 多线程主要围绕可见行、原子性两个特性展开， volatile 修饰的变量保证了其在线程之间的可见行， 即每次读到volatile 的对象时 一定是最新的数据

​    2. java 的jvm为了更好的性能，在Java底层会对指令进行重排， 多线程下会出现意想不到的问题，volatile会禁止重排，当然也一定程序降低了代码执行效率

* AOP 和 IOC原理

​    AOP 面向切面，是面向对象的补充， 由代理模式实现（动态代理、静态代理  spring使用的是动态代理）

​    IOC 控制反转，是一种bean 的管理方式，将对象的控制权交给spring框架， 使用对象时 无需创建 直接使用

* Spring怎么解决循环依赖的问题 

​    什么是循环依赖、怎么检测出循环依赖、spring 循环依赖有几种方式、 使用基于setter的循环依赖为什么不会出现问题、 Bean的生命周期

 	先设置引用，然后延迟初始化

* dispatchServlet 怎么分发任务的

根据 Mapping Registry做匹配，然后用反射做任务分发。

* mysql 给离散度低的字段建立索引会出现什么问题， 具体说下原因

重复性较强的字段不适合添加索引， mysql 给离散度低的字段建立索引，比如性别，在以性别为条件时查询会更慢

给离散度低的字段建立索引无法达到快速定位缩小行范围目的，考虑下 B+树的的结构。

hashMap 底层实现原理， 既然有hash排位，为什么还需要使用equals 方法，作用是什么

多线程

**反射，队列，锁**

```java
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