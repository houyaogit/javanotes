# MyBatis



#### mybatis-plus 的插件

#### mybatis-plus-join-boot-starter

mybatis-plus-join-boot-starter 是一个基于 Spring Boot 的 Mybatis-Plus 插件，可以方便地实现多表关联查询。它提供了一些自定义注解和工具类，可以简化多表关联查询的编写过程。

#### mybatis-plus-join

而 mybatis-plus-join 则是一个 Mybatis-Plus 的扩展插件，也可以实现多表关联查询。它提供了一些自定义注解和接口，可以更加灵活地实现多表关联查询，支持多种关联方式和复杂查询条件。





MyBatis 最上面是接口层，接口层就是开发人员在 Mapper 或者是 Dao 接口中的接口定义，是查询、新增、更新还是删除操作；中间层是数据处理层，主要是配置 Mapper -> XML 层级之间的参数映射，SQL 解析，SQL 执行，结果映射的过程。上述两种流程都由基础支持层来提供功能支撑，基础支持层包括连接管理，事务管理，配置加载，缓存处理等。



## 接口层

在不与 Spring 集成的情况下，使用 MyBatis 执行数据库的操作主要如下：

```java
InputStream is = Resources.getResourceAsStream("myBatis-config.xml");
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
SqlSessionFactory factory = builder.build(is);
sqlSession = factory.openSession();
```

其中的`SqlSessionFactory`,`SqlSession`是 MyBatis 接口的核心类，尤其是 SqlSession，这个接口是 MyBatis 中最重要的接口，这个接口能够让你执行命令，获取映射，管理事务。

#### **数据处理层**

- **配置解析**

在 Mybatis 初始化过程中，会加载 `mybatis-config.xml` 配置文件、映射配置文件以及 Mapper 接口中的注解信息，解析后的配置信息会形成相应的对象并保存到 `Configration` 对象中。之后，根据该对象创建 SqlSessionFactory 对象。待 Mybatis 初始化完成后，可以通过 SqlSessionFactory 创建 SqlSession 对象并开始数据库操作。

- **SQL 解析与 scripting 模块**

Mybatis 实现的动态 SQL 语句，几乎可以编写出所有满足需要的 SQL。

Mybatis 中 scripting 模块会根据用户传入的参数，解析映射文件中定义的动态 SQL 节点，形成数据库能执行的 SQL 语句。

- **SQL 执行**

SQL 语句的执行涉及多个组件，包括 MyBatis 的四大核心，它们是: `Executor`、`StatementHandler`、`ParameterHandler`、`ResultSetHandler`。SQL 的执行过程可以用下面这幅图来表示



#### **基础支持层**

- 反射模块

Mybatis 中的反射模块，对 Java 反射进行了很好的封装，提供了简易的 API，方便上层调用，并且对反射操作进行了一系列的优化，比如，缓存了类的 `元数据（MetaClass）`和对象的`元数据（MetaObject）`，提高了反射操作的性能。

- 类型转换模块

Mybatis 的别名机制，能够简化配置文件，该机制是类型转换模块的主要功能之一。类型转换模块的另一个功能**是实现 JDBC 类型与 Java 类型的转换**。在 SQL 语句绑定参数时，会将数据由 Java 类型转换成 JDBC 类型；在映射结果集时，会将数据由 JDBC 类型转换成 Java 类型。

- 日志模块

在 Java 中，有很多优秀的日志框架，如 Log4j、Log4j2、slf4j 等。Mybatis 除了提供了详细的日志输出信息，还能够集成多种日志框架，其日志模块的主要功能就是集成第三方日志框架。

- 资源加载模块

该模块主要封装了类加载器，确定了类加载器的使用顺序，并提供了加载类文件和其它资源文件的功能。

- 解析器模块

该模块有两个主要功能：一个是封装了 `XPath`，为 Mybatis 初始化时解析 `mybatis-config.xml`配置文件以及映射配置文件提供支持；另一个为处理动态 SQL 语句中的占位符提供支持。

- 数据源模块

Mybatis 自身提供了相应的数据源实现，也提供了与第三方数据源集成的接口。数据源是开发中的常用组件之一，很多开源的数据源都提供了丰富的功能，如连接池、检测连接状态等，选择性能优秀的数据源组件，对于提供 ORM 框架以及整个应用的性能都是非常重要的。

- 事务管理模块

一般地，Mybatis 与 Spring 框架集成，由 Spring 框架管理事务。但 Mybatis 自身对数据库事务进行了抽象，提供了相应的事务接口和简单实现。

- 缓存模块

Mybatis 中有`一级缓存`和`二级缓存`，这两级缓存都依赖于缓存模块中的实现。但是需要注意，这两级缓存与 Mybatis 以及整个应用是运行在同一个 JVM 中的，共享同一块内存，如果这两级缓存中的数据量较大，则可能影响系统中其它功能，所以需要缓存大量数据时，优先考虑使用 Redis、Memcache 等缓存产品。

- Binding 模块

在调用 `SqlSession` 相应方法执行数据库操作时，需要制定映射文件中定义的 SQL 节点，如果 SQL 中出现了拼写错误，那就只能在运行时才能发现。为了能尽早发现这种错误，Mybatis 通过 Binding 模块将用户自定义的 Mapper 接口与映射文件关联起来，系统可以通过调用自定义 Mapper 接口中的方法执行相应的 SQL 语句完成数据库操作，从而避免上述问题。注意，在开发中，我们只是创建了 Mapper 接口，而并没有编写实现类，这是因为 Mybatis 自动为 Mapper 接口创建了动态代理对象。

# MyBatis 核心组件

## SqlSessionFactory

SqlSessionFactory 是 MyBatis 框架中的一个接口，它主要负责的是

- MyBatis 框架初始化操作
- 为开发人员提供`SqlSession` 对象

### SqlSessionFactory 的执行流程

下面来对 SqlSessionFactory 的执行流程来做一个分析

首先第一步是 SqlSessionFactory 的创建

```plain
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

从这行代码入手，首先创建了一个 `SqlSessionFactoryBuilder` 工厂，这是一个建造者模式的设计思想，由 builder 建造者来创建 SqlSessionFactory 工厂

然后调用 SqlSessionFactoryBuilder 中的 `build` 方法传递一个`InputStream` 输入流，Inputstream 输入流中就是你传过来的配置文件 mybatis-config.xml，SqlSessionFactoryBuilder 根据传入的 InputStream 输入流和`environment`、`properties`属性创建一个`XMLConfigBuilder`对象。SqlSessionFactoryBuilder 对象调用 XMLConfigBuilder 的`parse()`方法。

XMLConfigBuilder 会解析`/configuration`标签，configuration 是 MyBatis 中最重要的一个标签。

#### configuration 的配置

- `properties`，外部属性，这些属性都是可外部配置且可动态替换的，既可以在典型的 Java 属性文件中配置，亦可通过 properties 元素的子元素来传递。

```xml
<properties>
    <property name="driver" value="com.mysql.jdbc.Driver" />
    <property name="url" value="jdbc:mysql://localhost:3306/test" />
    <property name="username" value="root" />
    <property name="password" value="root" />
</properties>
```

一般用来给 `environment` 标签中的 `dataSource` 赋值

```xml
<environment id="development">
  <transactionManager type="JDBC" />
  <dataSource type="POOLED">
    <property name="driver" value="${driver}" />
    <property name="url" value="${url}" />
    <property name="username" value="${username}" />
    <property name="password" value="${password}" />
  </dataSource>
</environment>
```

- `settings` ，MyBatis 中极其重要的配置，它们会改变 MyBatis 的运行时行为。

可以在此标签内设置 缓存，懒加载，自动驼峰命名规则映射等。

```xml
<settings>
  <setting name="cacheEnabled" value="true"/>
  <setting name="lazyLoadingEnabled" value="true"/>
</settings>
```

## SqlSession

在 MyBatis 初始化流程结束，也就是 SqlSessionFactoryBuilder -> SqlSessionFactory 的获取流程后，我们就可以通过 SqlSessionFactory 对象得到 `SqlSession` 然后执行 SQL 语句了。

SqlSession 对象是 MyBatis 中最重要的一个对象，这个接口能够让你执行命令，获取映射，管理事务。SqlSession 中定义了一系列模版方法，让你能够执行简单的 `CRUD` 操作，也可以通过 `getMapper` 获取 Mapper 层，执行自定义 SQL 语句，因为 SqlSession 在执行 SQL 语句之前是需要先开启一个会话，涉及到事务操作，所以还会有 `commit`、 `rollback`、`close` 等方法。这也是模版设计模式的一种应用。

## Executor

#### **Executor 的继承结构**

每一个 SqlSession 都会拥有一个 Executor 对象，这个对象负责增删改查的具体操作，我们可以简单的将它理解为 JDBC 中 Statement 的封装版。也可以理解为 SQL 的执行引擎，要干活总得有一个发起人吧，可以把 Executor 理解为发起人的角色。

Executor 执行器，它有两个实现类，分别是`BaseExecutor`和 `CachingExecutor`。

`BaseExecutor` 是一个抽象类，这种通过抽象的实现接口的方式是`适配器设计模式之接口适配` 的体现，是 Executor 的默认实现，实现了大部分 Executor 接口定义的功能，降低了接口实现的难度。BaseExecutor 的子类有三个，分别是 SimpleExecutor、ReuseExecutor 和 BatchExecutor。

`SimpleExecutor` : 简单执行器，是 MyBatis 中**默认使用**的执行器，每执行一次 update 或 select，就开启一个 Statement 对象，用完就直接关闭 Statement 对象 (可以是 Statement 或者是 PreparedStatment 对象)

`ReuseExecutor` : 可重用执行器，这里的重用指的是重复使用 Statement，它会在内部使用一个 Map 把创建的 Statement 都缓存起来，每次执行 SQL 命令的时候，都会去判断是否存在基于该 SQL 的 Statement 对象，如果存在 Statement 对象并且对应的 connection 还没有关闭的情况下就继续使用之前的 Statement 对象，并将其缓存起来。因为每一个 SqlSession 都有一个新的 Executor 对象，所以我们缓存在 ReuseExecutor 上的 Statement 作用域是同一个 SqlSession。

`BatchExecutor` : 批处理执行器，用于将多个 SQL 一次性输出到数据库

`CachingExecutor`: 缓存执行器，先从缓存中查询结果，如果存在就返回之前的结果；如果不存在，再委托给 Executor delegate 去数据库中取，delegate 可以是上面任何一个执行器。

### Executor 的具体执行过程

- 当有一个查询请求访问的时候，首先会经过 Executor 的实现类 `CachingExecutor` ，先从缓存中查询 SQL 是否是第一次执行，如果是第一次执行的话，那么就直接执行 SQL 语句，并创建缓存，如果第二次访问相同的 SQL 语句的话，那么就会直接从缓存中提取。
- 如果没有的话，就再重新创建 `Executor` 执行器执行 SQL 语句，  创建我们上面提到的三种执行器
- 到这里，执行器所做的工作就完事了，Executor 会把后续的工作交给 `StatementHandler` 继续执行。

### StatementHandler

`StatementHandler` 是四大组件中最重要的一个对象，负责操作 Statement 对象与数据库进行交互，在工作时还会使用 `ParameterHandler` 和 `ResultSetHandler`对参数进行映射，对结果进行实体类的绑定

**StatementHandler 的继承结构**和 `Executor`  比较相似

主要有三个实现类

- **SimpleStatementHandler**: 管理 Statement 对象并向数据库中推送不需要预编译的 SQL 语句。
- **PreparedStatementHandler**: 管理 Statement 对象并向数据中推送需要预编译的 SQL 语句。
- **CallableStatementHandler**：管理 Statement 对象并调用数据库中的存储过程。

#### StatementHandler 的创建

MyBatis 会根据 SQL 语句的类型进行对应 StatementHandler 的创建 。

#### #{}和${}的区别是什么？

\#{}是预编译处理，{}æ¯å­ç¬¦ä¸²æ¿æ¢ãmybatiså¨å¤ç#{}æ¶ï¼ä¼å°sqlä¸­ç#{}æ¿æ¢ä¸º?å·ï¼è°ç¨PreparedStatementçsetæ¹æ³æ¥èµå¼ï¼mybatiså¨å¤ç{}时，就是把${}替换成变量的值。使用#{}可以有效的防止SQL注入，提高系统安全性。

#### Xml 映射文件中，除了常见的 select|insert|updae|delete 标签之外，还有哪些标签？

还有很多其他的标签， resultMap ， parameterMap 加上动态 sql 的 9 个标签，`trim|where|set|foreach|if|choose|when|otherwise|bind`等，其中为 sql 片段标签，通过`标签引入 sql 片段，`为不支持自增的主键生成策略标签。

### mybatis 有几种分页方式？

数组分页

sql分页

拦截器分页

RowBounds分页

### mybatis 逻辑分页和物理分页的区别是什么？

- 物理分页速度上并不一定快于逻辑分页，逻辑分页速度上也并不一定快于物理分页。
- 物理分页总是优于逻辑分页：没有必要将属于数据库端的压力加诸到应用端来，就算速度上存在优势,然而其它性能上的优点足以弥补这个缺点。





### MyBatis 缓存

MyBatis提供了一级缓存和二级缓存

- 一级缓存：也称为本地缓存，用于保存用户在一次会话过程中查询的结果，用户一次会话中只能使用一个sqlSession，一级缓存是自动开启的，不允许关闭。
- 二级缓存：也称为全局缓存，是mapper级别的缓存，是针对一个表的查结果的存储，可以共享给所有针对这张表的查询的用户。也就是说对于mapper级别的缓存不同的sqlsession是可以共享的。

使用缓存 , 设置缓存

但是如果只是设置成如上那样，如果缓存溢出将会报错，解决办法就是将对应的实体序列化（假如对应的实体为User）

public class User implements Serializable{ }

禁用缓存 , usecache = false

#### 3.2 二级缓存配置

1. 需要在MyBatis核心配置文件,通过settings标签开发二级缓存。
   `<setting name="cacheEnabled" value="true"/>`
2. 在对应的Mapper文件中添加cache标签
3. cache标签属性

- type：cache使用的类型，默认是PerpetualCache，这在一级缓存中提到过。
- eviction： 定义回收的策略，常见的有FIFO，LRU。
- flushInterval： 配置一定时间自动刷新缓存，单位是毫秒。
- size： 最多缓存对象的个数。
- readOnly： 是否只读，若配置可读写，则需要对应的实体类能够序列化。
- blocking： 若缓存中找不到对应的key，是否会一直blocking，直到有对应的数据进入缓存。

### MyBatis分为哪几层-架构设计的角度

mybatis架构自下而上分为基础支撑层、数据处理层、API接口层这三层。

基础支撑层，主要是用来做连接管理、事务管理、配置加载、缓存管理等最基础组件，为上层提供最基础的支撑。
数据处理层，主要是用来做参数映射、sql解析、sql执行、结果映射等处理，可以理解为请求到达，完成一次数据库操作的流程。
API接口层，主要对外提供API，提供诸如数据的增删改查、获取配置等接口。

### mybatis防止SQL注入 #{}

| #{}：相当于JDBC中的PreparedStatement |
| ------------------------------------ |
| ${}：是输出变量的值                  |

\#{}是经过预编译的，是安全的；

![img](https://cdn.nlark.com/yuque/__latex/54e175425417a02ccc9b71515c7a9d7b.svg){}，那么不做任何处理的时候是存在SQL注入危险的。你说怎么防止，那我只能悲惨的告诉你，你得手动处理过滤一下输入的内容。如判断一下输入的参数的长度是否正常（注入语句一般很长），更精确的过滤则可以查询一下输入的参数是否在预期的参数集合中

### 1、#{}和${}的区别-

答：![img](https://cdn.jsdelivr.net/gh/houyaogit/Pictures@master/PicGo/28128e4f4214cb7e7713c144697c7104.svg)**{driver}会被静态替换为com.mysql.jdbc.Driver。**#{}是sql的参数占位符**，Mybatis会将sql中的#{}替换为?号，在sql执行前会使用PreparedStatement的参数设置方法，按序给sql的?号占位符设置参数值，比如ps.setInt(0, parameterValue)，#{item.name}的取值方式为使用反射从参数对象中获取item对象的name属性值，相当于param.getItem().getName()。

### 2、MybBatis Xml 常见标签

答：还有很多其他的标签，、、、、，加上动态sql的9个标签，trim|where|set|foreach|if|choose|when|otherwise|bind等，其中为sql片段标签，通过标签引入sql片段，为不支持自增的主键生成策略标签。

### 3、Xml映射文件，Dao接口的工作原理是什么？

答：Dao接口，就是人们常说的Mapper接口，接口的全限名，就是映射文件中的namespace的值，接口的方法名，就是映射文件中MappedStatement的id值，接口方法内的参数，就是传递给sql的参数。Mapper接口是没有实现类的，当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MappedStatement，举例：com.mybatis3.mappers.StudentDao.findStudentById，可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面id = findStudentById的MappedStatement。在Mybatis中，每一个、、、标签，都会被解析为一个MappedStatement对象。

Dao接口里的方法，是不能重载的，因为是全限名+方法名的保存和寻找策略。

Dao接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Dao接口生成代理proxy对象，代理对象proxy会拦截接口方法，转而执行MappedStatement所代表的sql，然后将sql执行结果返回。

### 4、Mybatis是如何进行分页的？分页插件的原理是什么？

答：Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页，可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。

举例：select  *from student，拦截sql后重写为：select t.* from （select * from student）t limit 0，10

### 5、简述Mybatis的插件运行原理，以及如何编写一个插件。

答：Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。

实现Mybatis的Interceptor接口并复写intercept()方法，然后在给插件编写注解，指定要拦截哪一个接口的哪些方法即可，记住，别忘了在配置文件中配置你编写的插件。

### 6、Mybatis执行批量插入，能返回数据库主键列表吗？

答：能，JDBC都能，Mybatis当然也能。

### 7、Mybatis动态sql是做什么的？都有哪些动态sql？能简述一下动态sql的执行原理不？

答：Mybatis动态sql可以让我们在Xml映射文件内，以标签的形式编写动态sql，完成逻辑判断和动态拼接sql的功能，Mybatis提供了9种动态sql标签trim|where|set|foreach|if|choose|when|otherwise|bind。

其执行原理为，使用OGNL从sql参数对象中计算表达式的值，根据表达式的值动态拼接sql，以此来完成动态sql的功能。

### 8、Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？

答：第一种是使用标签，逐一定义列名和对象属性名之间的映射关系。第二种是使用sql列的别名功能，将列别名书写为对象属性名，比如T_NAME AS NAME，对象属性名一般是name，小写，但是列名不区分大小写，Mybatis会忽略列名大小写，智能找到与之对应对象属性名，你甚至可以写成T_NAME AS NaMe，Mybatis一样可以正常工作。

有了列名与属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

### 9、Mybatis能执行一对一、一对多的关联查询吗？都有哪些实现方式，以及它们之间的区别。

答：能，Mybatis不仅可以执行一对一、一对多的关联查询，还可以执行多对一，多对多的关联查询，多对一查询，其实就是一对一查询，只需要把selectOne()修改为selectList()即可；多对多查询，其实就是一对多查询，只需要把selectOne()修改为selectList()即可。

关联对象查询，有两种实现方式，一种是单独发送一个sql去查询关联对象，赋给主对象，然后返回主对象。另一种是使用嵌套查询，嵌套查询的含义为使用join查询，一部分列是A对象的属性值，另外一部分列是关联对象B的属性值，好处是只发一个sql查询，就可以把主对象和其关联对象查出来。

那么问题来了，join查询出来100条记录，如何确定主对象是5个，而不是100个？其去重复的原理是标签内的子标签，指定了唯一确定一条记录的id列，Mybatis根据列值来完成100条记录的去重复功能，可以有多个，代表了联合主键的语意。

同样主对象的关联对象，也是根据这个原理去重复的，尽管一般情况下，只有主对象会有重复记录，关联对象一般不会重复。

举例：下面join查询出来6条记录，一、二列是Teacher对象列，第三列为Student对象列，Mybatis去重复处理后，结果为1个老师6个学生，而不是6个老师6个学生。

t_id t_name s_id

| 1 | teacher | 38 |
| 1 | teacher | 39 |
| 1 | teacher | 40 |
| 1 | teacher | 41 |
| 1 | teacher | 42 |
| 1 | teacher | 43 |

### 10、Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？

答：Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

它的原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。这就是延迟加载的基本原理。

当然了，不光是Mybatis，几乎所有的包括Hibernate，支持延迟加载的原理都是一样的。

### 11、Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？

答：不同的Xml映射文件，如果配置了namespace，那么id可以重复；如果没有配置namespace，那么id不能重复；毕竟namespace不是必须的，只是最佳实践而已。

原因就是namespace+id是作为Map<String, MappedStatement>的key使用的，如果没有namespace，就剩下id，那么，id重复会导致数据互相覆盖。有了namespace，自然id就可以重复，namespace不同，namespace+id自然也就不同。

### 12、Mybatis中如何执行批处理？

答：使用BatchExecutor完成批处理。

### 13、Mybatis都有哪些Executor执行器？它们之间的区别是什么？

答：Mybatis有三种基本的Executor执行器，**SimpleExecutor、ReuseExecutor、BatchExecutor。**

**SimpleExecutor：**每执行一次update或select，就开启一个Statement对象，用完立刻关闭Statement对象。

**ReuseExecutor：**执行update或select，以sql作为key查找Statement对象，存在就使用，不存在就创建，用完后，不关闭Statement对象，而是放置于Map<String, Statement>内，供下一次使用。简言之，就是重复使用Statement对象。

**BatchExecutor：**执行update（没有select，JDBC批处理不支持select），将所有sql都添加到批处理中（addBatch()），等待统一执行（executeBatch()），它缓存了多个Statement对象，每个Statement对象都是addBatch()完毕后，等待逐一执行executeBatch()批处理。与JDBC批处理相同。

作用范围：Executor的这些特点，都严格限制在SqlSession生命周期范围内。

### 14、Mybatis中如何指定使用哪一种Executor执行器？

答：在Mybatis配置文件中，可以指定默认的ExecutorType执行器类型，也可以手动给DefaultSqlSessionFactory的创建SqlSession的方法传递ExecutorType类型参数。

### 15、Mybatis是否可以映射Enum枚举类？

答：Mybatis可以映射枚举类，不单可以映射枚举类，Mybatis可以映射任何对象到表的一列上。映射方式为自定义一个TypeHandler，实现TypeHandler的setParameter()和getResult()接口方法。TypeHandler有两个作用，一是完成从javaType至jdbcType的转换，二是完成jdbcType至javaType的转换，体现为setParameter()和getResult()两个方法，分别代表设置sql问号占位符参数和获取列查询结果。

### 16、Mybatis映射文件中，如果A标签通过include引用了B标签的内容，请问，B标签能否定义在A标签的后面，还是说必须定义在A标签的前面？

答：虽然Mybatis解析Xml映射文件是按照顺序解析的，但是，被引用的B标签依然可以定义在任何地方，Mybatis都可以正确识别。

原理是，Mybatis解析A标签，发现A标签引用了B标签，但是B标签尚未解析到，尚不存在，此时，Mybatis会将A标签标记为未解析状态，然后继续解析余下的标签，包含B标签，待所有标签解析完毕，Mybatis会重新解析那些被标记为未解析的标签，此时再解析A标签时，B标签已经存在，A标签也就可以正常解析完成了。

### 17、简述Mybatis的Xml映射文件和Mybatis内部数据结构之间的映射关系？

答：Mybatis将所有Xml配置信息都封装到All-In-One重量级对象Configuration内部。在Xml映射文件中，标签会被解析为ParameterMap对象，其每个子元素会被解析为ParameterMapping对象。标签会被解析为ResultMap对象，其每个子元素会被解析为ResultMapping对象。每一个、、、标签均会被解析为MappedStatement对象，标签内的sql会被解析为BoundSql对象。

### 18、为什么说Mybatis是半自动ORM映射工具？它与全自动的区别在哪里？

答：Hibernate属于全自动ORM映射工具，使用Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取，所以它是全自动的。而Mybatis在查询关联对象或关联集合对象时，需要手动编写sql来完成，所以，称之为半自动ORM映射工具。

面试题看似都很简单，但是想要能正确回答上来，必定是研究过源码且深入的人，而不是仅会使用的人或者用的很熟的人，以上所有面试题及其答案所涉及的内容，在我的Mybatis系列博客中都有详细讲解和原理分析。

## 1、什么是Mybatis- 半ORM框架

（1）Mybatis是一个半ORM（对象关系映射）框架，它内部封装了JDBC，加载驱动、创建连接、创建statement等繁杂的过程，开发者开发时只需要关注如何编写SQL语句，可以严格控制sql执行性能，灵活度高。

（2）作为一个半ORM框架，MyBatis 可以使用 XML 或注解来配置和映射原生信息，将 POJO映射成数据库中的记录，避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。

称Mybatis是半自动ORM映射工具，是因为在查询关联对象或关联集合对象时，需要手动编写sql来完成。不像Hibernate这种全自动ORM映射工具，Hibernate查询关联对象或者关联集合对象时，可以根据对象关系模型直接获取。

（3）通过xml 文件或注解的方式将要执行的各种 statement 配置起来，并通过java对象和 statement中sql的动态参数进行映射生成最终执行的sql语句，最后由mybatis框架执行sql并将结果映射为java对象并返回。（从执行sql到返回result的过程）。

（4）由于MyBatis专注于SQL本身，灵活度高，所以比较适合对性能的要求很高，或者需求变化较多的项目，如互联网项目。

## 2、Mybaits的优缺点：

（1）优点：

① 基于SQL语句编程，相当灵活，不会对应用程序或者数据库的现有设计造成任何影响，SQL写在XML里，解除sql与程序代码的耦合，便于统一管理；提供XML标签，支持编写动态SQL语句，并可重用。

② 与JDBC相比，减少了50%以上的代码量，消除了JDBC大量冗余的代码，不需要手动开关连接；

③ 很好的与各种数据库兼容（因为MyBatis使用JDBC来连接数据库，所以只要JDBC支持的数据库MyBatis都支持）。

④ 能够与Spring很好的集成；

⑤ 提供映射标签，支持对象与数据库的ORM字段关系映射；提供对象关系映射标签，支持对象关系组件维护。

（2）缺点：

① SQL语句的编写工作量较大，尤其当字段多、关联表多时，对开发人员编写SQL语句的功底有一定要求。

② SQL语句依赖于数据库，导致数据库移植性差，不能随意更换数据库。

## 4、通常一个mapper.xml文件，都会对应一个Dao接口，这个Dao接口的工作原理是什么？Dao接口里的方法，参数不同时，方法能重载吗？

（详细的工作原理请参考这篇文章：https://blog.csdn.net/a745233700/article/details/89308762）

Mapper 接口的工作原理是JDK动态代理，Mybatis运行时会使用JDK动态代理为Mapper接口生成代理对象proxy，代理对象会拦截接口方法，根据类的全限定名+方法名，唯一定位到一个MapperStatement并调用执行器执行所代表的sql，然后将sql执行结果返回。

Mapper接口里的方法，是不能重载的，因为是使用 全限名+方法名 的保存和寻找策略。

Dao接口即Mapper接口。接口的全限名，就是映射文件中的namespace的值；接口的方法名，就是映射文件中Mapper的Statement的id值；接口方法内的参数，就是传递给sql的参数。

当调用接口方法时，接口全限名+方法名拼接字符串作为key值，可唯一定位一个MapperStatement。在Mybatis中，每一个SQL标签，比如、、、标签，都会被解析为一个MapperStatement对象。

举例：com.mybatis3.mappers.StudentDao.findStudentById，可以唯一找到namespace为com.mybatis3.mappers.StudentDao下面 id 为 findStudentById 的 MapperStatement。

## 5、Mybatis的Xml映射文件中，不同的Xml映射文件，id是否可以重复？

不同的Xml映射文件，如果配置了namespace，那么id可以重复；如果没有配置namespace，那么id不能重复；

原因就是namespace+id是作为Map<String, MapperStatement>的key使用的，如果没有namespace，就剩下id，那么，id重复会导致数据互相覆盖。有了namespace，自然id就可以重复，namespace不同，namespace+id自然也就不同。

备注：在旧版本的Mybatis中，namespace是可选的，不过新版本的namespace已经是必须的了。

## 6、Mybatis是如何进行分页的？分页插件的原理是什么？

```plain
Mybatis使用RowBounds对象进行分页，它是针对ResultSet结果集执行的内存分页，而非物理分页。可以在sql内直接书写带有物理分页的参数来完成物理分页功能，也可以使用分页插件来完成物理分页。

   分页插件的基本原理是使用Mybatis提供的插件接口，实现自定义插件，在插件的拦截方法内拦截待执行的sql，然后重写sql，根据dialect方言，添加对应的物理分页语句和物理分页参数。
```

## 7、简述Mybatis的插件运行原理，以及如何编写一个插件。

答：Mybatis仅可以编写针对ParameterHandler、ResultSetHandler、StatementHandler、Executor这4种接口的插件，Mybatis使用JDK的动态代理，为需要拦截的接口生成代理对象以实现接口方法拦截功能，每当执行这4种接口对象的方法时，就会进入拦截方法，具体就是InvocationHandler的invoke()方法，当然，只会拦截那些你指定需要拦截的方法。

编写插件：实现Mybatis的Interceptor接口并复写intercept()方法，然后再给插件编写注解，指定要拦截哪一个接口的哪些方法即可，最后在配置文件中配置你编写的插件。

## 8、Mybatis是否支持延迟加载？如果支持，它的实现原理是什么？

Mybatis仅支持association关联对象和collection关联集合对象的延迟加载，association指的就是一对一，collection指的就是一对多查询。在Mybatis配置文件中，可以配置是否启用延迟加载lazyLoadingEnabled=true|false。

延迟加载的基本原理是，使用CGLIB创建目标对象的代理对象，当调用目标方法时，进入拦截器方法，比如调用a.getB().getName()，拦截器invoke()方法发现a.getB()是null值，那么就会单独发送事先保存好的查询关联B对象的sql，把B查询上来，然后调用a.setB(b)，于是a的对象b属性就有值了，接着完成a.getB().getName()方法的调用。

当然了，不光是Mybatis，几乎所有的包括Hibernate，支持延迟加载的原理都是一样的。

## 9、Mybatis的一级、二级缓存:

（1）一级缓存: 基于 PerpetualCache 的 HashMap 本地缓存，其存储作用域为 Session，当 Session flush 或 close 之后，该 Session 中的所有 Cache 就将清空，默认打开一级缓存。

（2）二级缓存与一级缓存其机制相同，默认也是采用 PerpetualCache，HashMap 存储，不同在于其存储作用域为 Mapper(Namespace)，并且可自定义存储源，如 Ehcache。默认不打开二级缓存，要开启二级缓存，使用二级缓存属性类需要实现Serializable序列化接口(可用来保存对象的状态),可在它的映射文件中配置 ；

（3）对于缓存数据更新机制，当某一个作用域(一级缓存 Session/二级缓存Namespaces)的进行了C/U/D 操作后，默认该作用域下所有 select 中的缓存将被 clear 掉并重新更新，如果开启了二级缓存，则只根据配置判断是否刷新。

## 10、Mybatis是如何将sql执行结果封装为目标对象并返回的？都有哪些映射形式？

第一种是使用标签，逐一定义数据库列名和对象属性名之间的映射关系。

第二种是使用sql列的别名功能，将列的别名书写为对象属性名。

有了列名与属性名的映射关系后，Mybatis通过反射创建对象，同时使用反射给对象的属性逐一赋值并返回，那些找不到映射关系的属性，是无法完成赋值的。

## 11、Mybatis动态sql有什么用？执行原理？有哪些动态sql？

Mybatis动态sql可以在Xml映射文件内，以标签的形式编写动态sql，执行原理是根据表达式的值 完成逻辑判断 并动态拼接sql的功能。

Mybatis提供了9种动态sql标签：trim | where | set | foreach | if | choose | when | otherwise | bind。

## 12、Xml映射文件中，除了常见的select|insert|updae|delete标签之外，还有哪些标签？

、、、、，加上动态sql的9个标签，其中为sql片段标签，通过标签引入sql片段，为不支持自增的主键生成策略标签。

## 13、使用MyBatis的mapper接口调用时有哪些要求？

Mapper接口方法名和mapper.xml中定义的每个sql的id相同；
Mapper接口方法的输入参数类型和mapper.xml中定义的每个sql 的parameterType的类型相同；
Mapper接口方法的输出参数类型和mapper.xml中定义的每个sql的resultType的类型相同；
Mapper.xml文件中的namespace即是mapper接口的类路径。

## 14、 模糊查询like语句该怎么写?

第1种：在Java代码中添加sql通配符。

```plain
string wildcardname = “%smi%”;
list<name> names = mapper.selectlike(wildcardname);
 
<select id=”selectlike”>
 select * from foo where bar like #{value}
</select>
```

第2种：在sql语句中拼接通配符，会引起sql注入

```plain
string wildcardname = “smi”;
list<name> names = mapper.selectlike(wildcardname);
 
<select id=”selectlike”>
     select * from foo where bar like "%"${value}"%"
</select>
```

## 15、当实体类中的属性名和表中的字段名不一样 ，怎么办 ？

第1种： 通过在查询的sql语句中定义字段名的别名，让字段名的别名和实体类的属性名一致。

```plain
<select id=”selectorder” parametertype=”int” resultetype=”me.gacl.domain.order”>
   select order_id id, order_no orderno ,order_price price form orders where order_id=#{id};
</select>
```

第2种： 通过 来映射字段名和实体类属性名的一一对应的关系。

 select * from orders where order_id=#{id} 

```plain
<!–用result属性来映射非主键字段，property为实体类属性名，column为数据表中的属性–>
    <result property = “orderno” column =”order_no”/>
    <result property=”price” column=”order_price” />
</reslutMap>
```

## 16、如何获取自动生成的(主)键值?

insert 方法总是返回一个int值 ，这个值代表的是插入的行数。

如果采用自增长策略，自动生成的键值在 insert 方法执行完后可以被设置到传入的参数对象中。

```java
<insert id="insertname" usegeneratedkeys="true" keyproperty="id">
     insert into names (name) values (#{name})
</insert>
    name name = new name();
    name.setname(“fred”);

int rows = mapper.insertname(name);
// 完成后,id已经被设置到对象中
system.out.println(“rows inserted = ” + rows);
system.out.println(“generated key value = ” + name.getid());
```

## 17、在mapper中如何传递多个参数?

（1）第一种：
//DAO层的函数
Public UserselectUser(String name,String area);
//对应的xml,#{0}代表接收的是dao层中的第一个参数，#{1}代表dao层中第二参数，更多参数一致往后加即可。

```xml
<select id="selectUser"resultMap="BaseResultMap">  
    select *  fromuser_user_t   whereuser_name = #{0} anduser_area=#{1}  
</select>
```

（2）第二种： 使用 [@param]() 注解: 
public interface usermapper {
user selectuser(@param(“username”) string username,@param(“hashedpassword”) string hashedpassword);
}
然后,就可以在xml像下面这样使用(推荐封装为一个map,作为单个参数传递给mapper):

```xml
<select id=”selectuser” resulttype=”user”>
         select id, username, hashedpassword
         from some_table
         where username = #{username}
         and hashedpassword = #{hashedpassword}
</select>
```

（3）第三种：多个参数封装成map

```java
try{
//映射文件的命名空间.SQL片段的ID，就可以调用对应的映射文件中的SQL
//由于我们的参数超过了两个，而方法中只有一个Object参数收集，因此我们使用Map集合来装载我们的参数
Map<String, Object> map = new HashMap();
     map.put("start", start);
     map.put("end", end);
     return sqlSession.selectList("StudentID.pagination", map);
 }catch(Exception e){
     e.printStackTrace();
     sqlSession.rollback();
    throw e; }
finally{
 MybatisUtil.closeSqlSession();
 }
```

## 18、 一对一、一对多的关联查询 ？

 select * from class c,teacher t where c.teacher_id=t.t_id and c.c_id=#{id} 

```plain
<resultMap type="com.lcb.user.Classes" id="ClassesResultMap">  
    <!-- 实体类的字段名和数据表的字段名映射 -->  
    <id property="id" column="c_id"/>  
    <result property="name" column="c_name"/>  
    <association property="teacher" javaType="com.lcb.user.Teacher">  
        <id property="id" column="t_id"/>  
        <result property="name" column="t_name"/>  
    </association>  
</resultMap>  





<!--collection  一对多关联查询 -->  
<select id="getClass2" parameterType="int" resultMap="ClassesResultMap2">  
    select * from class c,teacher t,student s where c.teacher_id=t.t_id and c.c_id=s.class_id and c.c_id=#{id}  
</select>  
 
<resultMap type="com.lcb.user.Classes" id="ClassesResultMap2">  
    <id property="id" column="c_id"/>  
    <result property="name" column="c_name"/>  
    <association property="teacher" javaType="com.lcb.user.Teacher">  
        <id property="id" column="t_id"/>  
        <result property="name" column="t_name"/>  
    </association>  
 
    <collection property="student" ofType="com.lcb.user.Student">  
        <id property="id" column="s_id"/>  
        <result property="name" column="s_name"/>  
    </collection>  
</resultMap>
```

## 19、MyBatis实现一对一有几种方式?具体怎么操作的？

有联合查询和嵌套查询,联合查询是几个表联合查询,只查询一次, 通过在resultMap里面配置association节点配置一对一的类就可以完成；

嵌套查询是先查一个表，根据这个表里面的结果的 外键id，去再另外一个表里面查询数据,也是通过association配置，但另外一个表的查询通过select属性配置。

## 20、MyBatis实现一对多有几种方式,怎么操作的？

```plain
有联合查询和嵌套查询。联合查询是几个表联合查询,只查询一次,通过在resultMap里面的collection节点配置一对多的类就可以完成；嵌套查询是先查一个表,根据这个表里面的 结果的外键id,去再另外一个表里面查询数据,也是通过配置collection,但另外一个表的查询通过select节点配置。
```

## 21、Mapper编写有哪几种方式？

第一种：接口实现类继承SqlSessionDaoSupport：使用此种方法需要编写mapper接口，mapper接口实现类、mapper.xml文件。

（1）在sqlMapConfig.xml中配置mapper.xml的位置




（2）定义mapper接口
（3）实现类集成SqlSessionDaoSupport
mapper方法中可以this.getSqlSession()进行数据增删改查。
（4）spring 配置





第二种：使用org.mybatis.spring.mapper.MapperFactoryBean：

（1）在sqlMapConfig.xml中配置mapper.xml的位置，如果mapper.xml和mappre接口的名称相同且在同一个目录，这里可以不用配置




（2）定义mapper接口：
①mapper.xml中的namespace为mapper接口的地址
②mapper接口中的方法名和mapper.xml中的定义的statement的id保持一致
③Spring中定义





第三种：使用mapper扫描器：

（1）mapper.xml文件编写：
mapper.xml中的namespace为mapper接口的地址；
mapper接口中的方法名和mapper.xml中的定义的statement的id保持一致；
如果将mapper.xml和mapper接口的名称保持一致则不用在sqlMapConfig.xml中进行配置。
（2）定义mapper接口：
注意mapper.xml的文件名和mapper的接口名称保持一致，且放在同一个目录
（3）配置mapper扫描器：




（4）使用扫描器后从spring容器中获取mapper的实现对象。

## 22、什么是MyBatis的接口绑定？有哪些实现方式？

接口绑定，就是在MyBatis中任意定义接口,然后把接口里面的方法和SQL语句绑定, 我们直接调用接口方法就可以,这样比起原来了SqlSession提供的方法我们可以有更加灵活的选择和设置。

接口绑定有两种实现方式,一种是通过注解绑定，就是在接口的方法上面加上 @Select、@Update等注解，里面包含Sql语句来绑定；另外一种就是通过xml里面写SQL来绑定, 在这种情况下,要指定xml映射文件里面的namespace必须为接口的全路径名。当Sql语句比较简单时候,用注解绑定, 当SQL语句比较复杂时候,用xml绑定,一般用xml绑定的比较多。

## 23、MyBatis与Hibernate有哪些不同？

（1）Mybatis和hibernate不同，它不完全是一个ORM框架，因为MyBatis需要程序员自己编写Sql语句。

（2）Mybatis直接编写原生态sql，可以严格控制sql执行性能，灵活度高，非常适合对关系数据模型要求不高的软件开发，因为这类软件需求变化频繁，一但需求变化要求迅速输出成果。但是灵活的前提是mybatis无法做到数据库无关性，如果需要实现支持多种数据库的软件，则需要自定义多套sql映射文件，工作量大。

（3）Hibernate对象/关系映射能力强，数据库无关性好，对于关系模型要求高的软件，如果用hibernate开发可以节省很多代码，提高效率。