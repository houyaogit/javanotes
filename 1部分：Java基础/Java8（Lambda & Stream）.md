# Java8（Lambda&Stream）

**熟悉 Java 的最新规范，熟练使用 Future、Lambda 表达式、Stream 操作等**

------

## Lambda表达式

1. 参数，箭头，表达式语句
2. 参数，箭头，{多个语句}
3. 无参数，仅保留括号，箭头，表达式
4. 一个参数，可省略括号，箭头，表达式

不申明返回值类型，如果有返回值返回值类型会根据上下文推断出来，但是只有某几个分支有返回值是不合法的。

------

## 函数式接口

1. 是一个接口，符合java 接口的定义
2. 只包含一个抽象方法的接口
3. 由于只有一个抽象方法Lambda表示式自动填充上这个尚未实现的方法
4. 可以包括其他的default、static、private方法
5. 采用Lambda表达式，会自动创建一个“伪”嵌套类的对象（但是没有嵌套类的class文件产生），轻量级更简洁高效

```java
Comparator<String> c =  (String first,String second)->first.length()-second.length();
// 函数式表达式 自动成为接口的实现 ， Comparator 有两个接口 compare 和 equals
System.out.println(c.compare("hyy", "hy"));
```

函数式接口

```java
//系统自带的四个标准化的函数式接口 ， 自定义函数式接口
Function<T,R> 参数T    返回值R 
Consumer<T>   参数T		 无返回值
Predicate<T>  参数T		 Boolean
Supplier<T>   参数None 数据工厂
  
String[] planets = new String[] { "Mercury", "Venus", "Earth", "Mars", "Jupiter", "Saturn", "Uranus", "Neptune" };		
//Consumer 有一个accept的抽象方法接收一个参数，没有返回
Consumer<String> printer = s -> System.out.println("Planet :" + s);						
for(String p : planets)	{
  printer.accept(p);
}
//Function 有一个apply的抽象方法，接收一个参数，返回一个结果
//Supplier 有一个get的抽象方法，无输入参数，返回一个数据,
//Predicate有一个test的抽象方法，接收一个参数，返回boolean
Predicate<String> oddLength = s -> s.length()%2 == 0 ? false:true;
for(String p : planets)	{
	if(oddLength.test(p)) {System.out.println(p);}
}
```

**通过:: 访问构造方法、对象方法、静态方法**

Math::abs 等价于 x - > Math.abs(x);

Class::new 调用某构造函数，创造一个对象 ，   Class[]:new 支持数组对象创建

Lambda表达式变量遮蔽（核心:在思考问题时,使用**不可变值和函数**,函数对一个值进行处理,映射成另一个值)）

- 可以访问外部嵌套块的变量，但是变量要求是final 或 effective final
- Lambda没有变量遮蔽问题，因为它的内容和嵌套块有着相同的作用域
- Lambda表达式中，不可以申明与（外部嵌套块）局部变量同名的参数或者局部变量

Lambda表达式中的this，既是创建这个表达式的的方法的this对象

------

## 流Stream

一个流对外提供一个接口，可以访问一串特定的数据，进行聚合操作（filter/map/reduce/sorted/find/match）  

 Stream语法 类似SQL语句，遵循“做什么而非怎么做”原则

(流是使用函数式编程方式在集合上进行复杂操作的工具)

```java
List<String> list = new ArrayList<>();
count = list.stream().filter(x->x.length()>5).count();
count = list.parallelStream().filter(x->x.length()>5).count();
```

### 流的创建

1. Collection接口的stream 方法
2. Arrays.stream
3. Stream类进行转化，of 方法直接将数组转化， empty方法产生一个空流
4. Stream类进行转化，generate方法接受一个Lambda表达式，iterate接受一个种子和Lambda表达式
5. 基本流类型： IntStream、LongStream、DoubleStream
6. 并行流  parallelStream()  转化成并行流， Stream.parallel() 产生一个并行流 （要确保并行流过的操作不会竞争）
7. Files.lines 方法，Pattern 的splitAsStream()方法

### Stream的转换

从一种流到另外一种流

过滤、去重、排序、转化、抽取、跳过、连接、其他 ， 转化过程需要使用类本身的方法compareTo、hashCode、equals

1. 过滤 filter
2. 去重 distinct
3. 排序 sorted ， 配合Comparator
4. 转化 map  利用方法引用对元素进行函数计算、Lambda
5. 转化 抽取limit 、 跳过skip 、 连接 concat
6. 其他 额外调试peek

### Optional类型

数据对象容器，Java用来解决NullPointException的一把钥匙， 在流运算中避免对象是否null 的判断

创建：of、 empty、 ofNullable 对象为空就是 empty

使用：get、orElse, orElseGet

### 流的计算结果

流的计算

1. 简单简约（聚合函数）：count、max、min
2. 自定义简约：reduce
3. 查看遍历：iterator、 forEach
4. 存放到数据结构中 ：toArray 、 collect(Collectors.toList()).  Collectors.toSet() , toMap , joining 将结果连接起来

```plain
简单计算
1.count()计数
2.max(Comparator)
3.min(Comparator)
4.findFirst()
5.findAny()
6.anyMatch(Predicate)
7.allMatch(Predicate)
8.noneMatch(Predicate)
```

### 流的高阶计算

1. 分组 groupingBy 、 分区partitionBy
2. 分组后的简约 counting、 summing、maxBy、minBy
   以上方法在 java.util.stream.Collectors
   惰性求值，及早求值

### Stream的应用

谨慎使用并行流

1. 底层使用 fork -Join Pool ， 处理计算密集型任务
2. 数据量过小不用
3. 数据结构不易分解不用，如LinkedList
4. 数据频繁拆箱装箱不用
5. 设计findFirst 、 limit 的时候不用

------

## 高级集合类和收集器

```plain
1. 方法引用是一种引用方法的轻量级语法，形如：ClassName::methodName 。
2. 收集器可用来计算流的最终值，是reduce 方法的模拟。
3. Java 8 提供了收集多种容器类型的方式，同时允许用户自定义收集器。
```

------

## 数据并行化

(当需要对大量数据重复操作时,数据并行化很管用, fork/join)

1. 数据并行化是把工作拆分，同时在多核 CPU 上执行的方式。
2. 如果使用流编写代码，可通过调用parallel 或者 parallelStream 方法实现数据并行化操作。
3. 影响性能的五要素是：数据大小、源数据结构、值是否装箱、可用的 CPU 核数量，以及处理每个元素所花的时间。
   **并行化:**

```plain
例 6-1　串行化计算专辑曲目长度
public int serialArraySum() {
return albums.stream()
.flatMap(Album::getTracks)
.mapToInt(Track::getLength)
.sum();
}
调用 parallelStream 方法即能并行处理，如例 6-2 所示，剩余代码都是一样的，并行化就
是这么简单！
例 6-2　并行化计算专辑曲目长度
public int parallelArraySum() {
return albums.parallelStream()
.flatMap(Album::getTracks)
.mapToInt(Track::getLength)
.sum();
}
```

**peek**

在流中设置断点,日志中peek记录中间值

```plain
例 7-18　使用 peek 方法记录中间值
Set<String> nationalities= album.getMusicians()
    .filter(artist -> artist.getName().startsWith("The"))
    .map(artist -> artist.getNationality())
    .peek(nation -> System.out.println("Found nationality: " + nation))
    .collect(Collectors.<String>toSet());
```

------

## Lambda

**Lambda使用场景**

1. 使用()->{} 替代匿名内部类
2. 以流水线的方式处理数据
3. 更简单的数据并行处理
4. 用内部迭代取代外部迭代
5. 重构现有臃肿代码,更高的开发效率

**Lambda表达式的Best Practice**

1. 保持Lambda表达式简短且一目了然
2. 使用[*@*FunctionalInterface ]() 注释 
3. 优先使用 java.util.function 包下面的函数式接口
4. 不要在Lambda表达中执行有副作用的操作
5. 不要把Lambda表达式和匿名内部类同等对待
6. 多使用方法引用
   (在Lambda表达式中 a -> a.toLowerCase()和String::toLowerCase都能起到相同的作用，但两者相比，后者通常可读性更高并且代码会简短。)
7. 尽量避免Lambda的方法体中使用代码块{}
8. 不要盲目的开启并行流

#### 4大函数式接口

只有一个方法的接口，简化编程模型，新时代的成员：lambda表达式，链式编程，函数式接口，stream流式计算

- Function函数式接口、
- perticate断定型接口（只有一个入参，只能返回boolean）
- Consumer消费型接口只有输入没有返回值
- Supplier供型接口，没有参数只有返回值

#### Stream流式计算

- **计算交给流** filter、map、sorted、limit、forEach