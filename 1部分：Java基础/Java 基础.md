# Java基础

### Java 语言有哪些特点/什么是Java？

1. 简单易学；
2. 面向对象（封装，继承，多态）；
3. 平台无关性（ Java 虚拟机实现平台无关性）；
4. GC实现垃圾回收；
5. 异常处理机制；
6. 支持多线程；
7. 支持网络编程并且很方便；
8. 编译与解释并存；

### Java面向对象

初级：类 ，对象，集成封装和多态

中级：面向对象的开发优势， 高内聚 低耦合

高级：设计模式的角度，面向对象开发的优势，劣势

### 面向对象和面向过程的区别

- **面向过程** ：**面向过程性能比面向对象高。** 因为类调用时需要实例化，开销比较大，比较消耗资源，所以当性能是最重要的考量因素的时候，比如单片机、嵌入式开发、Linux/Unix等一般采用面向过程开发。但是，**面向过程没有面向对象易维护、易复用、易扩展。**

- **面向对象** ：**面向对象易维护、易复用、易扩展。** 因为面向对象有封装、继承、多态性的特性，所以可以设计出低耦合的系统，使系统更加灵活、更加易于维护。但是，**面向对象性能比面向过程低**。

  

### Java和C++的区别?

- 都是面向对象的语言，都支持封装、继承和多态
- Java 不提供指针来直接访问内存，程序内存更加安全
- Java 的类是单继承的，C++ 支持多重继承；虽然 Java 的类不可以多继承，但是接口可以多继承。
- Java 有自动内存管理机制，不需要程序员手动释放无用内存
- **在 C 语言中，字符串或字符数组最后都会有一个额外的字符‘\0’来表示结束。但是，Java 语言中没有结束符这一概念。**

### Java有几种基本数据类型

八种：**byte**,short,**long**,**int**,char,**float,double,boolean**

boolean, byte(8), short(16),char(16) ,    int(2147483647) ,float(32),long ,double 

### 基本类型和引用类型？他们的区别

除了8中基本数据类型都是引用类型， 为了面向对象操作的一致性，每种数据类型都有对应的包装类。

**不同点**：

* 赋值方法不同，基本类型直接赋值，引用类型通过 new 创建对象，然后再把对象赋予相应的变量。
* 比较方面的不同，== 号的比较：引用类型比较的是引用地址，基本类型比较的是值
* 在数据做为参数传递的时候，基本数据类型是值传递，而引用数据类型是引用传递（地址传递）。 

* **分别放在 JVM 的哪里？**

基本数据类型在被创建时，在栈上给其划分一块内存，将数值直接存储在栈上（也不完全一定）。

而引用数据类型在被创建时，首先要在栈上给其引用（句柄）分配一块内存，而对象的具体信息都存储在堆内存上，然后由栈上面的引用指向堆中对象的地址。

**引用类型的创建过程：**

现在为其创建一个对象MyDate d1 = new MyDate(8,8,2008);

在内存中的具体创建过程是：

1）首先在栈内存中位其d1分配一块空间；

2）然后在堆内存中为MyDate对象分配一块空间，并为其三个属性设初值0，0，0；

3）根据类MyDate中对属性的定义，为该对象的三个属性进行赋值操作；

4）调用构造方法，为三个属性赋值为8，8，2008；（注意这个时候d1与MyDate对象之间还没有建立联系）

5）将MyDate对象在堆内存中的地址，赋值给栈中的d1;通过句柄d1可以找到堆中对象的具体信息。 

### 重载和重写的区别

#### 重载

发生在同一个类中，方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同。

#### 重写

重写是子类对父类的允许访问的方法的实现过程进行重新编写,发生在子类中，方法名、参数列表必须相同，返回值范围小于等于父类，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类。另外，如果父类方法访问修饰符为 private 则子类就不能重写该方法。**也就是说方法提供的行为改变，而方法的外貌并没有改变。**

### Java 面向对象编程三大特性: 封装 继承 多态

#### 封装

把描述一个对象的属性和行为的代码封装在一个类中，属性用变量定义，行为用方法进行定义，方法可以直接访问同一个对象中的属性。 

#### 继承

子类继承父类的特征和行为。子类可以有父类非私有的方法，属性。子类也可以对父类进行扩展，也可以重写父类的方法。缺点就是提高代码之间的耦合性。 

#### 多态

多态是指程序中定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用在编程时并不确定，而是在程序运行期间才确定(比如：向上转型，只有运行才能确定其对象属性)。方法覆盖和重载体现了多态性。 

### final 在 java 中有什么作用？

- final 修饰的类叫最终类，该类不能被继承。
- final 修饰的方法不能被重写。
- final 修饰的变量不可更改，**其不可更改指的是其引用不可修改，对于引用类型值还是可能改变的，举个列子：String 内部对于 value 的定义；而对于基本类型来说就叫做常量了。** 

**final、finally、finalize 有什么区别？**

- final可以修饰类、变量、方法，修饰类表示该类不能被继承、修饰方法表示该方法不能被重写、修饰变量表示该变量是一个常量不能被重新赋值。
- finally一般作用在try-catch代码块中，在处理异常的时候，通常我们将一定要执行的代码方法finally代码块中，表示不管是否出现异常，该代码块都会执行，一般用来存放一些关闭资源的代码。
- finalize是一个方法，属于Object类的一个方法，而Object类是所有类的父类，该方法一般由垃圾回收器来调用，当我们调用System的gc()方法的时候，由垃圾回收器调用finalize(),回收垃圾。 

### String StringBuffer 和 StringBuilder 的区别是什么?

**线程安全**

 `StringBuilder`是线程不安全的，效率较高；而`StringBuffer`是线程安全的，效率较低。

**性能**

每次对 String 类型进行改变的时候，都会生成一个新的 String 对象，然后将指针指向新的 String 对象。StringBuffer 每次都会对 StringBuffer 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 StringBuilder 相比使用 StringBuffer 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

**数据可变和不可变**

1. `String`的值不可变的。
2. `StringBuffer`和`StringBuilder`的值是可变的，底层使用的是可变字符数组：`char[] value;`

**使用场景**

* 如果需要操作少量的数据用 String
* 单线程操作字符串缓冲区的情况下操作大量数据使用 StringBuilder  
* 多线程操作字符串缓冲区 下操作大量数据使用 StringBuffer 



StringBuffer  的 reverse（） 方法， substring()

**String/StringBuffer/StringBuilder的区别，扩展再问他们的实现：** StringBuffer 线程安全、值可变 

**String s = new String("abc");创建了几个 String Object?** ，两个  new String() 、 abc



### String 是如何实现不可变的？

String底层：char[ ] 数组

首先要明确：**String 不可变的是字符串的值不变。**

让我们看 String 源码：

```java
private final char value[];
```

从源码来看, String 类内部是用 char 数组来保存字符串的值, 并且 char[] 是 final 的, 这里的 final 意味着什么呢? 

- value 必须在构造时为其赋值
- 赋值后 value 的引用不能再变

 当我们实例化一个 String 对象并得到其引用后, 构造已经结束了, 即 value 的引用已经不能再变了 。 那么 value 的值呢, 理论上是可以改变的, 只要我们拿到 value 的引用, 可以直接通过下标改变他的值 。

然而，因为 String 并没有提供接口来改变 value 的值，所以value 的值我们从 String 外部获取不到，也改变不了。这才是String 才是不可变的真正原因，并不仅仅是使用 final 修饰了 value 数据。

补充：然而，并不是真正的完全不能获取，利用反射可以直接获取类内部属性。

### String 为什么设置为不可变？

* 为了实现字符串常量池(只有当字符是不可变的，字符串池才有可能实现) 
  * 补充：字符串常量池使用哈希表来存储字符串，类似于HashMap，初始桶的个数是60013
*  为了线程安全(字符串自己便是线程安全的) 
* 为了保证同一个对象调用 hashCode() 都产生相同的值，String 设置为不可变可以对这个条件有很好的支持，这也是 Map 类的 key 使用 String 的原因。





### Exception、Error、运行时异常与一般异常有何异同 /java 异常体系

**所有的异常都是从Throwable继承而来的** 

**Error**是错误，对于所有的编译时期的错误以及系统错误都是通过Error抛出的。 

 **Exception** 它规定的异常是程序本身可以处理的异常。 

**checked exception**可检查的异常，这是编码时非常常用的，所有checked exception都是需要在代码中处理的。它们的发生是可以预测的，正常的一种情况，可以合理的处理。比如IOException，或者一些自定义的异常。除了RuntimeException及其子类以外，都是checked exception。

**Unchecked Exception**

RuntimeException及其子类都是unchecked exception。比如NPE空指针异常，除数为0的算数异常ArithmeticException等等，这种异常是运行时发生，无法预先捕捉处理的。比如 NullPointerException ， SQLException， NumberFormatException ， FileNotFoundException， NoSuchMethodException。



## 接口和抽象类的区别

1. 接口的方法默认是 public，所有方法在接口中不能有实现(Java 8 开始接口方法可以有默认实现），而抽象类可以有非抽象的方法。
2. 接口中除了static、final变量，不能有其他变量，而抽象类中则不一定。
3. 一个类可以实现多个接口，但只能实现一个抽象类。接口自己本身可以通过extends关键字扩展多个接口。
4. 接口方法默认修饰符是public，抽象方法可以有public、protected和default这些修饰符
5. 从设计层面来说，抽象是对类的抽象，是一种模板设计，而接口是对行为的抽象，是一种行为的规范。

## Object类有哪些常用的方法？

equals 方法，hashCode 方法，toString 方法，wait 和 notify 系列的几个， getclass

### == 和 equals 的区别

 == 对于基本类型来说是值比较，对于引用类型来说是比较的是引用；而 equals 默认情况下是 `==`比较，只是很多类重写了 equals 方法，比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。 

## hashCode 与 equals (重要)

### hashCode（）介绍

hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个 int 整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。

散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！（可以快速找到所需要的对象）

### 为什么要有 hashCode

**我们先以“HashSet 如何检查重复”为例子来说明为什么要有 hashCode：** 当你把对象加入 HashSet 时，HashSet 会先计算对象的 hashcode 值来判断对象加入的位置，同时也会与其他已经加入的对象的 hashcode 值作比较，如果没有相符的hashcode，HashSet会假设对象没有重复出现。但是如果发现有相同 hashcode 值的对象，这时会调用 `equals()`方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样我们就大大减少了 equals 的次数，相应就大大提高了执行速度。

通过我们可以看出：`hashCode()` 的作用就是**获取哈希码**，也称为散列码；它实际上是返回一个int整数。这个**哈希码的作用**是快速确定该对象在哈希表中的索引位置。

**`hashCode() `在哈希表中才有用，在其它情况下没用**。

### hashCode（）与equals（）的相关规定

1. 如果两个对象相等，则hashcode一定也是相同的
2. 两个对象相等,对两个对象分别调用equals方法都返回true
3. 两个对象有相同的hashcode值，它们也不一定是相等的
4. **因此，equals 方法被覆盖过，则 hashCode 方法也必须被覆盖**
5. hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

**哪些场景下，子类需要重写 equals 方法和 hashCode 方法？**  

需要判断两个对象状态的相等性的时候。

### 为什么要重写 hashcode( ) 还要重写 equals( ) ？反之亦可问。

重写equals方法是为了按我们自己的想法来比较两个对象是否相等。如果不重写hashCode方法，可能出现**具有相同含义的不同对象**（他们的hashCode不同）的情况。而如果只重写 hashCode 不重写 equals 方法，因为 equals其实就是 == ，只是判断两个对象是否是同一个对象，所以不能得到我们想要的结果。所以需要同时重写equals和hashCode方法，目的是为了准确定位到我们期望的key。

**在 hashmap 中考虑：**

**通过阅读源码得知，在 hashMap 的 put 方法中，寻址找到的桶位如果上面已经有元素了，就判断 hash 值是否相同的同时也要通过 equals 判断（equals 是判断 map 的key 值），都为 true 才覆盖原来的值。如果只重写其中任意一个就会造成值的重复。 **

通俗点的解释

hashcode就类似 门牌号，小区非常大但定位你住哪里告诉门牌号就可以，非常快速定位到（非常像组数下标）

equals就是找到门牌号后需要比较里面具体的房间，少一个都不可以。

## Java序列化中如果有些字段不想进行序列化，怎么办？

对于不想进行序列化的变量，使用transient关键字修饰。

transient关键字的作用是：阻止实例中那些用此关键字修饰的的变量序列化；当对象被反序列化时，被transient修饰的变量值不会被持久化和恢复。transient只能修饰变量，不能修饰类和方法。

ArrayList 中存储数据的数组 elementData 是用 transient 修饰的，因为这个数组是动态扩展的，并不是所有的空间都被使用，因此就不需要所有的内容都被序列化。通过重写序列化和反序列化方法，使得可以只序列化数组中有内容的那部分数据。 

### 幂等性

幂等（idempotent、idempotence）是一个数学与计算机学概念，常见于抽象代数中，即f(f(x)) = f(x).简单的来说就是一个操作多次执行产生的结果与一次执行产生的结果一致。有些系统操作天生就具有幂等性例如数据库的select语句，但更多时候是需要程序员来做保证的，尤其是在分布式系统环境中，接口能不能做到保证幂等性对系统的影响可能是非常大的，例如很常见的支付下单等场景，由于分布式环境中网络的复杂性，用户误操作，网络抖动，消息重复，服务超时导致业务自动重试等等各种情况都可能会使线上数据产生了不一致，造成生产事故。 



## 怎么防止前端重复提交？

1. 提交按钮后屏蔽提交按钮(前端js控制)
2. 前端生产唯一id， 后端在数据库设计时业务字段加唯一约束，防止数据库插入重复数据。 
3. 利用Session防止表单重复提交

**ToDoList：**

描述下 HashMap get 方法的主要执行逻辑和流程；

Java 异常，什么是 checked Exception 和 unchecked Exception，举几个具体的例子；是否研究过 Spring Boot 中的异常；

ConcurrentHashMap 的特性和实现原理；

什么是分库分表，以及分库分表的具体方法和使用场景；

数据库事务的 ACID ；

什么是分布式锁以及其实现原理和使用场景；

## == 和 equals 的区别

## == 解读
对于基本类型和引用类型 == 的作用效果是不同的，如下所示：
基本类型：比较的是值是否相同；
引用类型：比较的是引用是否相同；

**==和equals的区别：** == ：内存地址,比较指针操作, equals：比较对象的内容是否相等适用于所有对象。

## equals
Object类的equals方法：
```java
 public boolean equals(Object obj) {
        return (this == obj);
    }
```
可以看出其实就是==
而String类中重写了父类Object的equals方法：
```java
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```
可以看出此方法就是先使用==比较，如果不同再把对象转换为字符串逐一字符比较。
**总结** ：== 对于基本类型来说是值比较，对于引用类型来说是比较的是引用；而 equals 默认情况下是 `==`比较，只是很多类重写了 equals 方法，比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。
## 缓存池和字符串常量池

## 缓存池
基本类型的valueOf() 方法会调用缓存池比较值的大小：
```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```
判断值是否在缓存池中，如果在的话就直接返回缓存池的内容，不在就新建一个。
Integer 缓存池的大小默认为 -128~127。
编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。
```java
Integer m = 123;
Integer n = 123;
System.out.println(m == n); // true
```
基本类型对应的缓冲池如下：
boolean values true and false
all byte values
short values between -128 and 127
int values between -128 and 127
char in the range \u0000 to \u007F
在 jdk 1.8 所有的数值类缓冲池中，Integer 的缓冲池 IntegerCache 很特殊，这个缓冲池的下界是 - 128，上界默认是 127，但是这个上界是可调的，在启动 jvm 的时候，通过 -XX:AutoBoxCacheMax=<size> 来指定这个缓冲池的大小，该选项在 JVM 初始化的时候会设定一个名为 java.lang.IntegerCache.high 系统属性，然后 IntegerCache 初始化的时候就会读取该系统属性来决定上界。
## 字符串常量池
**字符串常量池 String Pool**
保存着所有字符串字面量（literal strings），这些字面量在编译时期就确定。不仅如此，还可以使用 String 的 intern() 方法在运行过程将字符串添加到 String Pool 中。
当一个字符串调用 intern() 方法时，如果 String Pool 中已经存在一个字符串和该字符串值相等（使用 equals() 方法进行确定），那么就会返回 String Pool 中字符串的引用；否则，就会在 String Pool 中添加一个新的字符串，并返回这个新字符串的引用。
如果是采用 "bbb" 这种字面量的形式创建字符串，会自动地将字符串放入 String Pool 中。

### String s = new String(“abc”) 会创建几个对象
使用这种方式一共会创建两个字符串对象（前提是 String Pool 中还没有 “abc” 字符串对象）。
“abc” 属于字符串字面量，因此编译时期会在 String Pool 中创建一个字符串对象，指向这个 “abc” 字符串字面量；
而使用 new 的方式会在堆中创建一个字符串对象。



### private 修饰的方法可以通过反射访问， 那private的意义是什么呢<br>

​    a. java的 private 不是为类绝对安全设计的，更多是对用户常规使用Java的一种约束

​    b. 从外部常规调用时，能清晰看到类的结构

* 


## java 类初始

### Java类的初始化

**6种触发初始化：**

1.new 2.访问静态变量 3.调用静态方法 4.反射 5.初始化一个类的子类 6.jvm启动标明的启动类

**类的实例化四中方法：**

new、反射、clone、反序列化

### Java类初始化化顺序

```
（派生类 继承 基类）
基类   静态代码块  静态成员   （并列优先，按代码中出现的顺序）
派生类  静态代码块  静态成员
基类   普通代码块  普通成员字段
基类   构造器
派生类  普通代码块 普通成员字段
派生类  构造器
```

* 局部变量使用前需要显式的赋值，否则编译不通过， 为什么这么设计 

这样设计是一种约束，尽量大的程度减少使用者 犯错的机率

假如局部变量可以使用默认值，如果忘记赋值，会出现不可预期的情况



### 类的加载

- 类的加载机制：分为三个步骤：1装载（二进制），2 验证、准备（分配内存）、解析 ，3 初始化（赋初始值）

**加载器：ClassLoader**

> 1. Bootstrap ClassLoader（引导类加载器）: 负责加载$JAVA_HOME中jre/lib/rt.jar里所有的class，由C++实现，不是ClassLoader子类
> 2. Extension ClassLoader（扩展类加载器）: 负责加载java平台中扩展功能的一些jar包，包括$JAVA_HOME中		  jre/lib/*.jar或-Djava.ext.dirs指定目录下的jar包
> 3. App ClassLoader（系统类加载器）: 负责记载classpath中指定的jar包及目录中class
> 4. Custom ClassLoader（自定义加载器）
>
> 加载过程中会先检查类是否被已加载，**检查顺序是自底向上**，从Custom ClassLoader到BootStrap ClassLoader逐层检查，只要某个classloader已加载就视为已加载此类，保证此类只所有ClassLoader加载一次。**而加载的顺序是自顶向下**，也就是由上层来逐层尝试加载此类。



## 其他

* IntegerCache： -128～127 自动装箱

* 类型转换、类型比较 前先做检查 (instanceof) 

* Restful ： 设计风格，不是标准是约束条件， 基于这个风格设计的软件更简洁、更有层次，更易于实现缓存等机制。

* 一个汉字：utf-8 一个汉字占三字节，GBK 一个汉字两个字节。 一个字符占两个字节， int 4， long、double 8

* 作用域  当前类 同包 子类 其他： public - protected - default - private 

* **接口与抽象类的区别：**接口：Interface、多继承 、完全抽象没有方法和构造器、只能public修饰符

  抽象类：Abstract 、单继承多实现、可以有默认方法和构造器、所有修饰符

* **java序列化：** java对象转为字节序列的过程，实现 Serializable 接口才可被序列化

* 反射讲一讲，主要是概念,都在哪需要反射机制，反射的性能，如何优化?  
      是在运行状态中，对于任意的一个类，都能够知道这个类的所有属性和方法，对任意一个对象都能够通过反射机制调用一个类的任意方法，这种动态获取类信息及动态调用类对象方法的功能称为java的反射机制。  
  反射的作用：
  1、动态地创建类的实例，将类绑定到现有的对象中，或从现有的对象中获取类型
  2、应用程序需要在运行时从某个特定的程序集中载入一个特定的类  

* **值类型和引用类型的区别:**

  值类型(8个基本类型):4整,int,byte,long,short.2浮点,float,double  1真值类型 boolean 1编码: char

  引用类型:String,object,class,接口,数组) 其中String是final类不可被继承。引用类型，表示你操作的数据是同一个,当传一个参数给另一个方法时,你在另一个方法中改变这个变量的值,那么调用这个方法是所传入的变量的值也将改变

* 

* **hashCode方法的作用：** 返回对象存储的物理地址（hash算法），区分不同的对象，减少equals调用






### 参考： [https://javakeeper.starfish.ink/java/](https://javakeeper.starfish.ink/java/JVM/JVM-Java.html#jvm-的架构模型)





### Java 语言有哪些特点/什么是Java？

1. 简单易学；
2. 面向对象（封装，继承，多态）；
3. 平台无关性（Java 虚拟机实现平台无关性）；
4. GC实现垃圾回收；
5. 异常处理机制；
6. 支持多线程；
7. 支持网络编程并且很方便；
8. 编译与解释并存；

### Java面向对象

初级：类 ，对象，集成封装和多态

中级：面向对象的开发优势， 高内聚 低耦合

高级：设计模式的角度，面向对象开发的优势，劣势

### 面向对象和面向过程的区别

- **面向过程** ：**面向过程性能比面向对象高。** 因为类调用时需要实例化，开销比较大，比较消耗资源，所以当性能是最重要的考量因素的时候，比如单片机、嵌入式开发、Linux/Unix等一般采用面向过程开发。但是，**面向过程没有面向对象易维护、易复用、易扩展。**
- **面向对象** ：**面向对象易维护、易复用、易扩展。** 因为面向对象有封装、继承、多态性的特性，所以可以设计出低耦合的系统，使系统更加灵活、更加易于维护。但是，**面向对象性能比面向过程低**。

### Java和C++的区别?

- 都是面向对象的语言，都支持封装、继承和多态
- Java 不提供指针来直接访问内存，程序内存更加安全
- Java 的类是单继承的，C++ 支持多重继承；虽然 Java 的类不可以多继承，但是接口可以多继承。
- Java 有自动内存管理机制，不需要程序员手动释放无用内存
- **在 C 语言中，字符串或字符数组最后都会有一个额外的字符‘\0’来表示结束。但是，Java 语言中没有结束符这一概念。**

### Java有几种基本数据类型

八种：**byte**,short,**long**,**int**,char,**float,double,boolean**

boolean, byte(8), short(16),char(16) ,    int(2147483647) ,float(32),long ,double

### 基本类型和引用类型？他们的区别

除了8中基本数据类型都是引用类型， 为了面向对象操作的一致性，每种数据类型都有对应的包装类。

**不同点**：

- 赋值方法不同，基本类型直接赋值，引用类型通过 new 创建对象，然后再把对象赋予相应的变量。
- 比较方面的不同，== 号的比较：引用类型比较的是引用地址，基本类型比较的是值
- 在数据做为参数传递的时候，基本数据类型是值传递，而引用数据类型是引用传递（地址传递）。
- **分别放在 JVM 的哪里 ？**

基本数据类型在被创建时，在栈上给其划分一块内存，将数值直接存储在栈上（也不完全一定）。

而引用数据类型在被创建时，首先要在栈上给其引用（句柄）分配一块内存，而对象的具体信息都存储在堆内存上，然后由栈上面的引用指向堆中对象的地址。

**引用类型的创建过程：**

现在为其创建一个对象MyDate d1 = new MyDate(8,8,2008);

在内存中的具体创建过程是：

1）首先在栈内存中位其d1分配一块空间；

2）然后在堆内存中为MyDate对象分配一块空间，并为其三个属性设初值0，0，0；

3）根据类MyDate中对属性的定义，为该对象的三个属性进行赋值操作；

4）调用构造方法，为三个属性赋值为8，8，2008；（注意这个时候d1与MyDate对象之间还没有建立联系）

5）将MyDate对象在堆内存中的地址，赋值给栈中的d1;通过句柄d1可以找到堆中对象的具体信息。

### 重载和重写的区别

#### 重载

发生在同一个类中，方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和访问修饰符可以不同。

#### 重写

**重写是子类对父类的允许访问的方法的实现过程进行重新编写,发生在子类中，方法名、参数列表必须相同**，返回值范围小于等于父类，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类。另外，如果父类方法访问修饰符为 private 则子类就不能重写该方法。**也就是说方法提供的行为改变，而方法的外貌并没有改变。**

### Java 面向对象编程三大特性: 封装 继承 多态

#### 封装

把描述一个对象的属性和行为的代码封装在一个类中，属性用变量定义，行为用方法进行定义，方法可以直接访问同一个对象中的属性。

#### 继承

子类继承父类的特征和行为。子类可以有父类非私有的方法，属性。子类也可以对父类进行扩展，也可以重写父类的方法。缺点就是提高代码之间的耦合性。

#### 多态

多态是指程序中定义的引用变量所指向的具体类型和通过该引用变量发出的方法调用在编程时并不确定，而是在程序运行期间才确定(比如：向上转型，只有运行才能确定其对象属性)。方法覆盖和重载体现了多态性。

### final 在 java 中有什么作用？

- final 修饰的类叫最终类，该类不能被继承。
- final 修饰的方法不能被重写。
- final 修饰的变量不可更改，**其不可更改指的是其引用不可修改，对于引用类型值还是可能改变的，举个列子：String 内部对于 value 的定义；而对于基本类型来说就叫做常量了。**

**final、finally、finalize 有什么区别？**

- final可以修饰类、变量、方法，修饰类表示该类不能被继承、修饰方法表示该方法不能被重写、修饰变量表示该变量是一个常量不能被重新赋值。
- finally一般作用在try-catch代码块中，在处理异常的时候，通常我们将一定要执行的代码方法finally代码块中，表示不管是否出现异常，该代码块都会执行，一般用来存放一些关闭资源的代码。
- finalize是一个方法，属于Object类的一个方法，而Object类是所有类的父类，该方法一般由垃圾回收器来调用，当我们调用System的gc()方法的时候，由垃圾回收器调用finalize(),回收垃圾。

### String StringBuffer 和 StringBuilder 的区别是什么?

**线程安全**

`StringBuilder`是线程不安全的，效率较高；而`StringBuffer`是线程安全的，效率较低。

**性能**

每次对 String 类型进行改变的时候，都会生成一个新的 String 对象，然后将指针指向新的 String 对象。StringBuffer 每次都会对 StringBuffer 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 StringBuilder 相比使用 StringBuffer 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

**数据可变和不可变**

1. `String`的值不可变的。
2. `StringBuffer`和`StringBuilder`的值是可变的，底层使用的是可变字符数组：`char[] value;`

**使用场景**

- 如果需要操作少量的数据用 String
- 单线程操作字符串缓冲区的情况下操作大量数据使用 StringBuilder
- 多线程操作字符串缓冲区 下操作大量数据使用 StringBuffer

StringBuffer  的 reverse（） 方法， substring()

**String/StringBuffer/StringBuilder的区别，扩展再问他们的实现：** StringBuffer 线程安全、值可变

**String s = new String("abc");创建了几个 String Object?** ，两个  new String() 、 abc

### String 是如何实现不可变的？

String底层：char[ ] 数组

首先要明确：**String 不可变的是字符串的值不变。**

让我们看 String 源码：

```java
private final char value[];
```

从源码来看, String 类内部是用 char 数组来保存字符串的值, 并且 char[] 是 final 的, 这里的 final 意味着什么呢?

- value 必须在构造时为其赋值
- 赋值后 value 的引用不能再变

当我们实例化一个 String 对象并得到其引用后, 构造已经结束了, 即 value 的引用已经不能再变了 。 那么 value 的值呢, 理论上是可以改变的, 只要我们拿到 value 的引用, 可以直接通过下标改变他的值 。

然而，因为 String 并没有提供接口来改变 value 的值，所以value 的值我们从 String 外部获取不到，也改变不了。这才是String 才是不可变的真正原因，并不仅仅是使用 final 修饰了 value 数据。

补充：然而，并不是真正的完全不能获取，利用反射可以直接获取类内部属性。

### String 为什么设置为不可变？

- 为了实现字符串常量池(只有当字符是不可变的，字符串池才有可能实现)

- 补充：字符串常量池使用哈希表来存储字符串，类似于HashMap，初始桶的个数是60013

- 为了线程安全(字符串自己便是线程安全的)
- 为了保证同一个对象调用 hashCode() 都产生相同的值，String 设置为不可变可以对这个条件有很好的支持，这也是 Map 类的 key 使用 String 的原因。

### Exception、Error、运行时异常与一般异常有何异同 /java 异常体系

**所有的异常都是从Throwable继承而来的**

**Error**是错误，对于所有的编译时期的错误以及系统错误都是通过Error抛出的。

**Exception** 它规定的异常是程序本身可以处理的异常。

**checked exception**可检查的异常，这是编码时非常常用的，所有checked exception都是需要在代码中处理的。它们的发生是可以预测的，正常的一种情况，可以合理的处理。比如IOException，或者一些自定义的异常。除了RuntimeException及其子类以外，都是checked exception。

**Unchecked Exception**

RuntimeException及其子类都是unchecked exception。比如NPE空指针异常，除数为0的算数异常ArithmeticException等等，这种异常是运行时发生，无法预先捕捉处理的。比如 NullPointerException ， SQLException， NumberFormatException ， FileNotFoundException， NoSuchMethodException。

### 接口和抽象类的区别

1. 接口的方法默认是 public，所有方法在接口中不能有实现(Java 8 开始接口方法可以有默认实现），而抽象类可以有非抽象的方法。
2. 接口中除了static、final变量，不能有其他变量，而抽象类中则不一定。
3. 一个类可以实现多个接口，但只能实现一个抽象类。接口自己本身可以通过extends关键字扩展多个接口。
4. 接口方法默认修饰符是public，抽象方法可以有public、protected和default这些修饰符
5. 从设计层面来说，抽象是对类的抽象，是一种模板设计，而接口是对行为的抽象，是一种行为的规范。

### Object类有哪些常用的方法？

equals 方法，hashCode 方法，toString 方法，wait 和 notify 系列的几个， getclass

### hashCode 与 equals (重要)

### hashCode（）介绍

hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个 int 整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。

散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！（可以快速找到所需要的对象）

### 为什么要有 hashCode

**我们先以“HashSet 如何检查重复”为例子来说明为什么要有 hashCode：** 当你把对象加入 HashSet 时，HashSet 会先计算对象的 hashcode 值来判断对象加入的位置，同时也会与其他已经加入的对象的 hashcode 值作比较，如果没有相符的hashcode，HashSet会假设对象没有重复出现。但是如果发现有相同 hashcode 值的对象，这时会调用 `equals()`方法来检查 hashcode 相等的对象是否真的相同。如果两者相同，HashSet 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样我们就大大减少了 equals 的次数，相应就大大提高了执行速度。

通过我们可以看出：`hashCode()` 的作用就是**获取哈希码**，也称为散列码；它实际上是返回一个int整数。这个**哈希码的作用**是快速确定该对象在哈希表中的索引位置。

`**hashCode()**`**在哈希表中才有用，在其它情况下没用**。

### hashCode（）与equals（）的相关规定

1. 如果两个对象相等，则hashcode一定也是相同的
2. 两个对象相等,对两个对象分别调用equals方法都返回true
3. 两个对象有相同的hashcode值，它们也不一定是相等的
4. **因此，equals 方法被覆盖过，则 hashCode 方法也必须被覆盖**
5. hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

**哪些场景下，子类需要重写 equals 方法和 hashCode 方法？**

需要判断两个对象状态的相等性的时候。

### 为什么要重写 hashcode( ) 还要重写 equals( ) ？反之亦可问。

重写equals方法是为了按我们自己的想法来比较两个对象是否相等。如果不重写hashCode方法，可能出现**具有相同含义的不同对象**（他们的hashCode不同）的情况。而如果只重写 hashCode 不重写 equals 方法，因为 equals其实就是 == ，只是判断两个对象是否是同一个对象，所以不能得到我们想要的结果。所以需要同时重写equals和hashCode方法，目的是为了准确定位到我们期望的key。

**在 hashmap 中考虑：**

**通过阅读源码得知，在 hashMap 的 put 方法中，寻址找到的桶位如果上面已经有元素了，就判断 hash 值是否相同的同时也要通过 equals 判断（equals 是判断 map 的key 值），都为 true 才覆盖原来的值。如果只重写其中任意一个就会造成值的重复。** 

通俗点的解释

hashcode就类似 门牌号，小区非常大但定位你住哪里告诉门牌号就可以，非常快速定位到（非常像组数下标）

equals就是找到门牌号后需要比较里面具体的房间，少一个都不可以。

### Java序列化中如果有些字段不想进行序列化，怎么办？

对于不想进行序列化的变量，使用transient关键字修饰。

transient关键字的作用是：阻止实例中那些用此关键字修饰的的变量序列化；当对象被反序列化时，被transient修饰的变量值不会被持久化和恢复。transient只能修饰变量，不能修饰类和方法。

ArrayList 中存储数据的数组 elementData 是用 transient 修饰的，因为这个数组是动态扩展的，并不是所有的空间都被使用，因此就不需要所有的内容都被序列化。通过重写序列化和反序列化方法，使得可以只序列化数组中有内容的那部分数据。

### 幂等性

幂等（idempotent、idempotence）是一个数学与计算机学概念，常见于抽象代数中，即f(f(x)) = f(x).简单的来说就是一个操作多次执行产生的结果与一次执行产生的结果一致。有些系统操作天生就具有幂等性例如数据库的select语句，但更多时候是需要程序员来做保证的，尤其是在分布式系统环境中，接口能不能做到保证幂等性对系统的影响可能是非常大的，例如很常见的支付下单等场景，由于分布式环境中网络的复杂性，用户误操作，网络抖动，消息重复，服务超时导致业务自动重试等等各种情况都可能会使线上数据产生了不一致，造成生产事故。

### 怎么防止前端重复提交？

1. 提交按钮后屏蔽提交按钮(前端js控制)
2. 前端生产唯一id， 后端在数据库设计时业务字段加唯一约束，防止数据库插入重复数据。
3. 利用Session防止表单重复提交

### == 和 equals 的区别

**== 解读**

对于基本类型和引用类型 == 的作用效果是不同的，如下所示：

基本类型：比较的是值是否相同；

引用类型：比较的是引用是否相同；

**==和equals的区别：** == ：内存地址,比较指针操作, equals：比较对象的内容是否相等适用于所有对象。

**equals解读**

Object类的equals方法：

```java
public boolean equals(Object obj) {
        return (this == obj);
    }
```

可以看出其实就是==

而String类中重写了父类Object的equals方法：

```java
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```

可以看出此方法就是先使用比较，如果不同再把对象转换为字符串逐一字符比较。
**总结** ： 对于基本类型来说是值比较，对于引用类型来说是比较的是引用；而 equals 默认情况下是 `==`比较，只是很多类重写了 equals 方法，比如 String、Integer 等把它变成了值比较，所以一般情况下 equals 比较的是值是否相等。

### 缓存池和字符串常量池

#### 缓存池

基本类型的valueOf() 方法会调用缓存池比较值的大小：

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

判断值是否在缓存池中，如果在的话就直接返回缓存池的内容，不在就新建一个。

Integer 缓存池的大小默认为 -128~127。

编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。

```java
Integer m = 123;
Integer n = 123;
System.out.println(m == n); // true
```

基本类型对应的缓冲池如下：

boolean values true and false

all byte values

short values between -128 and 127

int values between -128 and 127

char in the range \u0000 to \u007F

在 jdk 1.8 所有的数值类缓冲池中，Integer 的缓冲池 IntegerCache 很特殊，这个缓冲池的下界是 - 128，上界默认是 127，但是这个上界是可调的，在启动 jvm 的时候，通过 -XX:AutoBoxCacheMax= 来指定这个缓冲池的大小，该选项在 JVM 初始化的时候会设定一个名为 java.lang.IntegerCache.high 系统属性，然后 IntegerCache 初始化的时候就会读取该系统属性来决定上界。

### 字符串常量池

**字符串常量池 String Pool**

保存着所有字符串字面量（literal strings），这些字面量在编译时期就确定。不仅如此，还可以使用 String 的 intern() 方法在运行过程将字符串添加到 String Pool 中。

当一个字符串调用 intern() 方法时，如果 String Pool 中已经存在一个字符串和该字符串值相等（使用 equals() 方法进行确定），那么就会返回 String Pool 中字符串的引用；否则，就会在 String Pool 中添加一个新的字符串，并返回这个新字符串的引用。

如果是采用 "bbb" 这种字面量的形式创建字符串，会自动地将字符串放入 String Pool 中。

### String s = new String(“abc”) 会创建几个对象

使用这种方式一共会创建两个字符串对象（前提是 String Pool 中还没有 “abc” 字符串对象）。

“abc” 属于字符串字面量，因此编译时期会在 String Pool 中创建一个字符串对象，指向这个 “abc” 字符串字面量；而使用 new 的方式会在堆中创建一个字符串对象。

### private 修饰的方法可以通过反射访问， 那private的意义是什么呢

​    a. java的 private 不是为类绝对安全设计的，更多是对用户常规使用Java的一种约束

​    b. 从外部常规调用时，能清晰看到类的结构

### Java类的初始化

**6种触发初始化：**

1.new 2.访问静态变量 3.调用静态方法 4.反射 5.初始化一个类的子类 6.jvm启动标明的启动类

**类的实例化四中方法：**

new、反射、clone、反序列化

### Java类初始化化顺序

```plain
（派生类 继承 基类）
基类   静态代码块  静态成员   （并列优先，按代码中出现的顺序）
派生类  静态代码块  静态成员
基类   普通代码块  普通成员字段
基类   构造器
派生类  普通代码块 普通成员字段
派生类  构造器
```

- 局部变量使用前需要显式的赋值，否则编译不通过， 为什么这么设计

这样设计是一种约束，尽量大的程度减少使用者 犯错的机率

假如局部变量可以使用默认值，如果忘记赋值，会出现不可预期的情况

### 类的加载

- 类的加载机制：分为三个步骤：1装载（二进制），2 验证、准备（分配内存）、解析 ，3 初始化（赋初始值）

**加载器：ClassLoader**

1. Bootstrap ClassLoader（引导类加载器）: 负责加载$JAVA_HOME中jre/lib/rt.jar里所有的class，由C++实现，不是ClassLoader子类
2. Extension ClassLoader（扩展类加载器）: 负责加载java平台中扩展功能的一些jar包，包括$JAVA_HOME中		  jre/lib/*.jar或-Djava.ext.dirs指定目录下的jar包
3. App ClassLoader（系统类加载器）: 负责记载classpath中指定的jar包及目录中class
4. Custom ClassLoader（自定义加载器）

加载过程中会先检查类是否被已加载，**检查顺序是自底向上**，从Custom ClassLoader到BootStrap ClassLoader逐层检查，只要某个classloader已加载就视为已加载此类，保证此类只所有ClassLoader加载一次。**而加载的顺序是自顶向下**，也就是由上层来逐层尝试加载此类。

### 其他

- IntegerCache： -128～127 自动装箱
- 类型转换、类型比较 前先做检查 (instanceof)
- Restful ： 设计风格，不是标准是约束条件， 基于这个风格设计的软件更简洁、更有层次，更易于实现缓存等机制。
- 一个汉字：utf-8 一个汉字占三字节，GBK 一个汉字两个字节。 一个字符占两个字节， int 4， long、double 8
- 作用域  当前类 同包 子类 其他： public - protected - default - private
- **接口与抽象类的区别：**接口：Interface、多继承 、完全抽象没有方法和构造器、只能public修饰符
  抽象类：Abstract 、单继承多实现、可以有默认方法和构造器、所有修饰符
- **java序列化：** java对象转为字节序列的过程，实现 Serializable 接口才可被序列化
- 反射讲一讲，主要是概念,都在哪需要反射机制，反射的性能，如何优化?
  是在运行状态中，对于任意的一个类，都能够知道这个类的所有属性和方法，对任意一个对象都能够通过反射机制调用一个类的任意方法，这种动态获取类信息及动态调用类对象方法的功能称为java的反射机制。
  反射的作用：
  1、动态地创建类的实例，将类绑定到现有的对象中，或从现有的对象中获取类型
  2、应用程序需要在运行时从某个特定的程序集中载入一个特定的类
- **值类型和引用类型的区别:**
  值类型(8个基本类型):4整,int,byte,long,short.2浮点,float,double  1真值类型 boolean 1编码: char
  引用类型:String,object,class,接口,数组) 其中String是final类不可被继承。引用类型，表示你操作的数据是同一个,当传一个参数给另一个方法时,你在另一个方法中改变这个变量的值,那么调用这个方法是所传入的变量的值也将改变
- **hashCode方法的作用：** 返回对象存储的物理地址（hash算法），区分不同的对象，减少equals调用





![img](https://cdn.jsdelivr.net/gh/houyaogit/Pictures@master/PicGo/ecb65198-6dba-42b7-a28e-4102a770ea2c.png)

⌘ J

- 开始
- 小记
- 收藏
- 逛逛
- 知识库

- 100-Work
- 101-工作归纳
- 200-Learning 书
- 300-Life
- 400-Meaning
- 资源知识库
- Z-Archive

- 知识小组暂无常用知识小组
- **更大附件、无限文档数、公开分享等****立刻购买会员**
- 更多

**101-工作归纳**知识小组-Java

TODO

工作总结

常见问题TODO

操作系统、计算机网络

Java

一、Java基础

**OOP**

Java集合总结

Effective Java

Java IO & NIO

JDBC核心技术

JVM总结

JVM

JVM内存模型、Java内存模型与GC

JUC核心知识点

JUC(80%)

Lambda & Stream

Linux

Spring

数据库

Redis总结

ActiveMQ

Nginx

其他

MacHands

Dameng

docker

Git常用命令

mac_svn

Mac-github

Mac-Idea智能

mac_redis

mac_nacos&sentinel

mac_mysql

mac_maven

mac_iphone学习软件

抓包whistle

b站题目答案-TODO

归档

保存于：2023-04-27 14:15:50

0 / 0

**分享**发布





### 什么是B/S架构？什么是C/S架构

1. B/S(Browser/Server)，浏览器/服务器程序
2. C/S(Client/Server)，客户端/服务端，桌面应用程序

### 什么是JDK？什么是JRE?

1. JDK：java development kit：java开发工具包，是开发人员所需要安装的环境
2. JRE：java runtime environment：java运行环境，java程序运行所需要安装的环境

### Java的数据结构有那些？

1. 线性表（ArrayList）
2. 链表（LinkedList）
3. 栈（Stack）
4. 队列（Queue）
5. 图（Map）
6. 树（Tree） 

### 类与对象的关系?

​	类是对象的抽象，对象是类的具体，类是对象的模板，对象是类的实例



### Java中有几种数据类型

​	整形：byte,short,int,long

​	浮点型：float,double

​	字符型：char

​	布尔型：boolean



### instanceof关键字的作用

​	instanceof 严格来说是Java中的一个双目运算符，用来测试一个对象是否为一个类的实例，用法为：	

boolean result = obj instanceof Class   

​	其中 obj 为一个对象，Class 表示一个类或者一个接口，当 obj 为 Class 的对象，或者是其直接或间接子类，或者是其接口的实现类，结果result 都返回 true，否则返回false。

​	注意：编译器会检查 obj 是否能转换成右边的class类型，如果不能转换则直接报错，如果不能确定类型，则通过编译，具体看运行时定。

```bash
inti=0;
System.out.println(i instanceof Integer);//编译不通过i必须是引用类型，不能是基本类型
System.out.println(i instanceof Object);//编译不通过
Integer integer=newInteger(1);
System.out.println(integer instanceof Integer);//true
//false,在JavaSE规范中对instanceof运算符的规定就是：如果obj为null，那么将返回false。
System.out.println(nullinstanceofObject);
```

### 什么是隐式转换，什么是显式转换

​	显示转换就是类型强转，把一个大类型的数据强制赋值给小类型的数据；

​	隐式转换就是大范围的变量能够接受小范围的数据；

​	隐式转换和显式转换其实就是自动类型转换和强制类型转换。



### Char类型能不能转成int类型？能不能转化成string类型，能不能转成double类型

​	Char在java中也是比较特殊的类型，它的int值从1开始，一共有2的16次方个数据；

​	Char<int<long<float<double；Char类型可以隐式转成int,double类型，但是不能隐式转换成string；

​	如果char类型转成byte，short类型的时候，需要强转。



### 一个java类中包含那些内容？

​	属性、方法、内部类、构造方法、代码块。



### 那针对浮点型数据运算出现的误差的问题，你怎么解决？

​	使用**BigDecimal**类进行浮点型数据的运算



### 访问修饰符 public,private,protected,以及不写（默认） 时的区别？

​		类的成员不写访问修饰时默认为 default。默认对于同一个包中的其他类相当于公 开（public），对于不是同一个包中的其他类相当于私有（private）。受保护 （protected）对子类相当于公开，对不是同一包中的没有父子关系的类相当于私 有。Java 中，外部类的修饰符只能是 public 或默认，类的成员（包括内部类）的 修饰符可以是以上四种。

| ** ****修饰符** | **当前类** | **同 包** | **子 类** | **其他包** |
| --------------- | ---------- | --------- | --------- | ---------- |
| public          | 能         | 能        | 能        | 能         |
| protected       | 能         | 能        | 能        | 不能       |
| default         | 能         | 能        | 不能      | 不能       |
| private         | 能         | 不能      | 不能      | 不能       |

​		

### equals与==的区别

​	**==：**

​		== 比较的是变量(栈)内存中存放的对象的(堆)**内存地址**，用来判断两个对象的地址是否相同，即是否是指相同一个对象。比较的是真正意义上的指针操作。

1、比较的是操作符两端的操作数是否是同一个对象。

2、两边的操作数必须是同一类型的（可以是父子类之间）才能编译通过。

3、比较的是地址，如果是具体的阿拉伯数字的比较，值相等则为true，如：

int a=10 与 long b=10L 与 double c=10.0都是相同的（为true），因为他们都指向地址为10的堆。

​	**equals：**

​		equals用来比较的是两个**对象的内容**是否相等，由于所有的类都是继承自java.lang.Object类的，所以

适用于所有对象，如果没有对该方法进行覆盖的话，调用的仍然是Object类中的方法，而Object中的

equals方法返回的却是==的判断。

​	**总结**：

​		所有比较是否相等时，都是用equals 并且在对常量相比较时，把常量写在前面，因为使用object的

equals object可能为null 则空指针在阿里的代码规范中只使用equals ，阿里插件默认会识别，并可以快速修改，推荐安装阿里插件来排查老代码使用“==”，替换成equals



### 程序的结构有那些？

​		顺序结构 , 选择结构 , 循环结构



### 数组实例化有几种方式？

​		**静态实例化：**创建数组的时候已经指定数组中的元素， int [] a= new int[]{ 1 , 3 , 3}

​		**动态实例化：**实例化数组的时候，只指定了数组程度，数组中所有元素都是数组类型的默认

数组一旦实例化，它的长度就是固定的



### 内部类与静态内部类的区别？

​		**静态内部类相对与外部类是独立存在**的，在静态内部类中无法直接访问外部类中变量、方法。如果要访问的话，必须要new一个外部类的对象，使用new出来的对象来访问。但是可以直接访问静态的变量、调用静态的方法；

​		**普通内部类作为外部类一个成员而存在**，在普通内部类中可以直接访问外部类属性，调用外部类的方法。如果外部类要访问内部类的属性或者调用内部类的方法，必须要创建一个内部类的对象，使用该对象访问属性或者调用方法。如果其他的类要访问普通内部类的属性或者调用普通内部类的方法，必须要在外部类中创建一个普通内部类的对象作为一个属性，外同类可以通过该属性调用普通内部类的方法或者访问普通内部类的属性。

​		如果其他的类要访问静态内部类的属性或者调用静态内部类的方法，直接创建一个静态内部类对象即可。



### Static关键字有什么作用？

​		Static可以修饰内部类、方法、变量、代码块

​		Static修饰的类是静态内部类

​		Static修饰的方法是静态方法，表示该方法属于当前类的，而不属于某个对象的，静态方法也不能被重写，可以直接使用类名来调用。在static方法中不能使用this或者super关键字。

​		Static修饰变量是静态变量或者叫类变量，静态变量被所有实例所共享，不会依赖于对象。静态变量在内存中只有一份拷贝，在JVM加载类的时候，只为静态分配一次内存。

​		Static修饰的代码块叫静态代码块，通常用来做程序优化的。静态代码块中的代码在整个类加载的时候只会执行一次。静态代码块可以有多个，如果有多个，按照先后顺序依次执行。



### final在java中的作用，有哪些用法?

​		final也是很多面试喜欢问的地方,但我觉得这个问题很无聊,通常能回答下以下5点就不错了: 

1. 被final修饰的类不可以被继承
2. 被final修饰的方法不可以被重写
3. 被final修饰的变量不可以被改变.如果修饰引用,那么表示引用不可变,引用指向的内容可变. 
4. 被final修饰的方法,JVM会尝试将其内联,以提高运行效率
5. 被final修饰的常量,在编译阶段会存入常量池中.

除此之外,编译器对final域要遵守的两个重排序规则更好:

在构造函数内对一个final域的写入,与随后把这个被构造对象的引用赋值给一个引用变量,这两个操作之间不能重排序

初次读一个包含final域的对象的引用,与随后初次读这个final域,这两个操作之间不能重排序



### 讲下java中的math类有那些常用方法？

​	Pow()：幂运算

​	Sqrt()：平方根

​	Round()：四舍五入

​	Abs()：求绝对值

​	Random()：生成一个0-1的随机数，包括0不包括1



### String类的常用方法有那些？

​	charAt：返回指定索引处的字符

​	indexOf()：返回指定字符的索引

​	replace()：字符串替换

​	trim()：去除字符串两端空白

​	split()：分割字符串，返回一个分割后的字符串数组

​	getBytes()：返回字符串的byte类型数组

​	length()：返回字符串长度

​	toLowerCase()：将字符串转成小写字母

​	toUpperCase()：将字符串转成大写字符

​	substring()：截取字符串

​	format()：格式化字符串

​	equals()：字符串比较



### Hashcode的作用

​		java的集合有两类，一类是List，还有一类是Set。前者有序可重复，后者无序不重复。当我们在set中插入的时候怎么判断是否已经存在该元素呢，可以通过equals方法。但是如果元素太多，用这样的方法就会比较满。于是有人发明了哈希算法来提高集合中查找元素的效率。 这种方式将集合分成若干个存储区域，每个对象可以计算出一个哈希码，可以将哈希码分组，每组分别对应某个存储区域，根据一个对象的哈希码就可以确定该对象应该存储的那个区域。

hashCode方法可以这样理解：它返回的就是根据对象的内存地址换算出的一个值。这样一来，当集合要添加新的元素时，先调用这个元素的hashCode方法，就一下子能定位到它应该放置的物理位置上。如果这个位置上没有元素，它就可以直接存储在这个位置上，不用再进行任何比较了；如果这个位置上已经有元素了，就调用它的equals方法与新元素进行比较，相同的话就不存了，不相同就散列其它的地址。这样一来实际调用equals方法的次数就大大降低了，几乎只需要一两次。



### Java创建对象有几种方式？

java中提供了以下四种创建对象的方式: 

1. new创建新对象
2. 通过反射机制
3. 采用clone机制
4. 通过序列化机制











# 