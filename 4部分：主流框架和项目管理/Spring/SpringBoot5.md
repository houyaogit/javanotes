

# spring IOC容器管理必须知道这些操作——基于XML方式



# Spring——IOC（控制反转）

------

## 一、IOC容器

 **1、什么是IOC（控制反转）**

 a）把对象创建和对象之间的调用过程，交给Spring进行管理

 b）使用IOC目的：为了降低耦合度

 **2、IOC底层**

 a）xml解析、工厂模式、反射

 **3、Spring提供的IOC容器实现的两种方式（两个接口）**

 a）BeanFactory接口：IOC容器基本实现是Spring内部接口的使用接口，不提供给开发人员进行使用（加载配置文件时候不会创建对象，在获取对象时才会创建对象。）

 b）ApplicationContext接口：BeanFactory接口的子接口，提供更多更强大的功能，提供给开发人员使用（加载配置文件时候就会把在配置文件对象进行创建）推荐使用！

 **4、ApplicationContext接口的实现类（具体根据API文档查看☺）**

------

## 二、IOC容器-Bean管理

 **1、IOC操作Bean管理**

 a）Bean管理就是两个操作：（1）Spring创建对象；（2）Spring注入属性

 **2、基于XML配置文件创建对象**

```xml
<!--1 配置User对象创建-->
<bean id="user" class="com.atguigu.spring5.User"></bean>
12
```

 **3、基于XML方式注入属性**（DI：依赖注入（注入属性））

 a）set方式注入

```java
//（1）传统方式： 创建类，定义属性和对应的set方法
public class Book {
        //创建属性
        private String bname;

        //创建属性对应的set方法
        public void setBname(String bname) {
            this.bname = bname;
        }
   }
12345678910
```

------

```xml
<!--（2）spring方式： set方法注入属性-->
<bean id="book" class="com.atguigu.spring5.Book">
    <!--使用property完成属性注入
        name：类里面属性名称
        value：向属性注入的值
    -->
    <property name="bname" value="Hello"></property>
    <property name="bauthor" value="World"></property>
</bean>
123456789
```

 b）有参构造函数注入

```java
//（1）传统方式：创建类，构建有参函数
public class Orders {
    //属性
    private String oname;
    private String address;
    //有参数构造
    public Orders(String oname,String address) {
        this.oname = oname;
        this.address = address;
    }
  }
1234567891011
```

------

```xml
<!--（2）spring方式：有参数构造注入属性-->
<bean id="orders" class="com.atguigu.spring5.Orders">
    <constructor-arg name="oname" value="Hello"></constructor-arg>
    <constructor-arg name="address" value="China！"></constructor-arg>
</bean>
12345
```

 c）p名称空间注入（了解即可）

```xml
<!--1、添加p名称空间在配置文件头部-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"		<!--在这里添加一行p-->

<!--2、在bean标签进行属性注入（算是set方式注入的简化操作）-->
    <bean id="book" class="com.atguigu.spring5.Book" p:bname="very" p:bauthor="good">
    </bean>
123456789
```

 **4、注入空值和特殊符号**

```xml
<bean id="book" class="com.atguigu.spring5.Book">
    <!--（1）null值-->
    <property name="address">
        <null/><!--属性里边添加一个null标签-->
    </property>
    
    <!--（2）特殊符号赋值-->
     <!--属性值包含特殊符号
       a 把<>进行转义 &lt; &gt;
       b 把带特殊符号内容写到CDATA
      -->
        <property name="address">
            <value><![CDATA[<<南京>>]]></value>
        </property>
</bean>
123456789101112131415
```

------

 **5、注入属性-外部bean**

 a）创建两个类service和dao类

```Java
public class UserService {//service类

    //创建UserDao类型属性，生成set方法
    private UserDao userDao;
    public void setUserDao(UserDao userDao) {
        this.userDao = userDao;
    }

    public void add() {
        System.out.println("service add...............");
        userDao.update();//调用dao方法
    }
}

public class UserDaoImpl implements UserDao {//dao类

    @Override
    public void update() {
        System.out.println("dao update...........");
    }
}
123456789101112131415161718192021
```

 b）在spring配置文件中进行配置

```xml
<!--1 service和dao对象创建-->
<bean id="userService" class="com.atguigu.spring5.service.UserService">
    <!--注入userDao对象
        name属性：类里面属性名称
        ref属性：创建userDao对象bean标签id值
    -->
    <property name="userDao" ref="userDaoImpl"></property>
</bean>
<bean id="userDaoImpl" class="com.atguigu.spring5.dao.UserDaoImpl"></bean>
123456789
```

 **6、基于XML方式注入内部bean和级联赋值**

 a）注入属性-内部bean

> （1）一对多关系：部门和员工
> 一个部门有多个员工，一个员工属于一个部门（部门是一，员工是多）
> （2）在实体类之间表示一对多关系，员工表示所属部门，使用对象类型属性进行表示

```java
//部门类
public class Dept {
    private String dname;
    public void setDname(String dname) {
        this.dname = dname;
    }
}

//员工类
public class Emp {
    private String ename;
    private String gender;
    //员工属于某一个部门，使用对象形式表示
    private Dept dept;
    
    public void setDept(Dept dept) {
        this.dept = dept;
    }
    public void setEname(String ename) {
        this.ename = ename;
    }
    public void setGender(String gender) {
        this.gender = gender;
    }
}
12345678910111213141516171819202122232425
```

> （3）在spring配置文件中配置

```xml
<!--内部bean-->
    <bean id="emp" class="com.atguigu.spring5.bean.Emp">
        <!--设置两个普通属性-->
        <property name="ename" value="Andy"></property>
        <property name="gender" value="女"></property>
        <!--设置对象类型属性-->
        <property name="dept">
            <bean id="dept" class="com.atguigu.spring5.bean.Dept"><!--内部bean赋值-->
                <property name="dname" value="宣传部门"></property>
            </bean>
        </property>
    </bean>
123456789101112
```

------

 b）注入属性-级联赋值

```xml
<!--方式一：级联赋值-->
    <bean id="emp" class="com.atguigu.spring5.bean.Emp">
        <!--设置两个普通属性-->
        <property name="ename" value="Andy"></property>
        <property name="gender" value="女"></property>
        <!--级联赋值-->
        <property name="dept" ref="dept"></property>
    </bean>
    <bean id="dept" class="com.atguigu.spring5.bean.Dept">
        <property name="dname" value="公关部门"></property>
    </bean>
1234567891011
 //方式二：生成dept的get方法（get方法必须有！！）
    public Dept getDept() {
        return dept;
    }
1234
 <!--级联赋值-->
    <bean id="emp" class="com.atguigu.spring5.bean.Emp">
        <!--设置两个普通属性-->
        <property name="ename" value="jams"></property>
        <property name="gender" value="男"></property>
        <!--级联赋值-->
        <property name="dept" ref="dept"></property>
        <property name="dept.dname" value="技术部门"></property>
    </bean>
    <bean id="dept" class="com.atguigu.spring5.bean.Dept">
    </bean>
1234567891011
```

 **7、IOC 操作 Bean 管理——xml 注入集合属性**

> 1、注入数组类型属性 2、注入 List 集合类型属性 3、注入 Map 集合类型属性

```java
//（1）创建类，定义数组、list、map、set 类型属性，生成对应 set 方法
public class Stu {
    //1 数组类型属性
    private String[] courses;
    //2 list集合类型属性
    private List<String> list;
    //3 map集合类型属性
    private Map<String,String> maps;
    //4 set集合类型属性
    private Set<String> sets;
    
    public void setSets(Set<String> sets) {
        this.sets = sets;
    }
    public void setCourses(String[] courses) {
        this.courses = courses;
    }
    public void setList(List<String> list) {
        this.list = list;
    }
    public void setMaps(Map<String, String> maps) {
        this.maps = maps;
    }
1234567891011121314151617181920212223
<!--（2）在 spring 配置文件进行配置-->
    <bean id="stu" class="com.atguigu.spring5.collectiontype.Stu">
        <!--数组类型属性注入-->
        <property name="courses">
            <array>
                <value>java课程</value>
                <value>数据库课程</value>
            </array>
        </property>
        <!--list类型属性注入-->
        <property name="list">
            <list>
                <value>张三</value>
                <value>小三</value>
            </list>
        </property>
        <!--map类型属性注入-->
        <property name="maps">
            <map>
                <entry key="JAVA" value="java"></entry>
                <entry key="PHP" value="php"></entry>
            </map>
        </property>
        <!--set类型属性注入-->
        <property name="sets">
            <set>
                <value>MySQL</value>
                <value>Redis</value>
            </set>
        </property>
</bean>
12345678910111213141516171819202122232425262728293031
```

 **8、在集合里面设置对象类型值**

```java
  //学生所学多门课程
    private List<Course> courseList;//创建集合
    public void setCourseList(List<Course> courseList) {
        this.courseList = courseList;
    }
12345
    <!--创建多个course对象-->
    <bean id="course1" class="com.atguigu.spring5.collectiontype.Course">
        <property name="cname" value="Spring5框架"></property>
    </bean>
    <bean id="course2" class="com.atguigu.spring5.collectiontype.Course">
        <property name="cname" value="MyBatis框架"></property>
    </bean>
    
   	<!--注入list集合类型，值是对象-->
       <property name="courseList">
           <list>
               <ref bean="course1"></ref>
               <ref bean="course2"></ref>
           </list>
       </property>
12345678910111213141516
```

------

```xml
<!--第一步：在 spring 配置文件中引入名称空间 util-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:util="http://www.springframework.org/schema/util" <!--添加util名称空间-->
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/util http://www.springframework.org/schema/util/spring-util.xsd">  <!--添加util名称空间-->
    
<!--第二步：使用 util 标签完成 list 集合注入提取-->
<!--把集合注入部分提取出来-->
 <!--1 提取list集合类型属性注入-->
    <util:list id="bookList">
        <value>易筋经</value>
        <value>九阴真经</value>
        <value>九阳神功</value>
    </util:list>

 <!--2 提取list集合类型属性注入使用-->
    <bean id="book" class="com.atguigu.spring5.collectiontype.Book" scope="prototype">
        <property name="list" ref="bookList"></property>
    </bean>
```





# Spring IOC容器-Bean管理——基于XML方式（续集）



## Spring IOC容器-Bean管理——基于XML（续集）

 **1、IOC 操作 Bean 管理（FactoryBean）**

>  1、Spring 有两种类型 bean，一种普通 bean，另外一种工厂 bean（FactoryBean）
>
>  2、普通 bean：在配置文件中定义 bean 类型就是返回类型
>
>  3、工厂 bean：在配置文件定义 bean 类型可以和**返回类型不一样** 第一步 创建类，让这个类作为工厂 bean，实现接口 FactoryBean 第二步 实现接口里面的方法，在实现的方法中定义返回的 bean 类型

```java
public class MyBean implements FactoryBean<Course> {

    //定义返回bean
    @Override
    public Course getObject() throws Exception {
        Course course = new Course();
        course.setCname("abc");
        return course;
    }
}
12345678910
<bean id="myBean" class="com.atguigu.spring5.factorybean.MyBean">
</bean>
12
@Test
public void test3() {
 ApplicationContext context =
 new ClassPathXmlApplicationContext("bean3.xml");
 Course course = context.getBean("myBean", Course.class);//返回值类型可以不是定义的bean类型！
 System.out.println(course);
}
1234567
```

 **2、IOC 操作 Bean 管理（bean 作用域）**

 在 Spring 里面，默认情况下，bean 是单实例对象，下面进行作用域设置：

> （1）在 spring 配置文件 bean 标签里面有属性（scope）用于设置单实例还是多实例
>
> （2）scope 属性值 第一个值 默认值，singleton，表示是单实例对象 第二个值 prototype，表示是多实例对象

```xml
<bean id="book" class="com.atguigu.spring5.collectiontype.Book" scope="prototype"><!--设置为多实例-->
        <property name="list" ref="bookList"></property>
</bean>
123
```

> （3）singleton 和 prototype 区别
>
>  a）singleton 单实例，prototype 多实例
>
>  b）设置 scope 值是 singleton 时候，**加载 spring 配置文件时候就会创建单实例对象** ；设置 scope 值是 prototype 时候，不是在加载 spring 配置文件时候创建对象，在**调用 getBean 方法时候创建多实例对象**

 **3、IOC 操作 Bean 管理（bean 生命周期）**

> 1、生命周期 ：从对象创建到对象销毁的过程
>
> 2、bean 生命周期
>
>  （1）通过构造器创建 bean 实例（无参数构造）
>
>  （2）为 bean 的属性设置值和对其他 bean 引用（调用 set 方法）
>
>  （3）调用 bean 的初始化的方法（需要进行配置初始化的方法）
>
>  （4）bean 可以使用了（对象获取到了）
>
>  （5）当容器关闭时候，调用 bean 的销毁的方法（需要进行配置销毁的方法）
>
> 3、演示 bean 生命周期 ：

```java
        public class Orders {
         //无参数构造
         public Orders() {
         System.out.println("第一步 执行无参数构造创建 bean 实例");
         }
         private String oname;
         public void setOname(String oname) {
         this.oname = oname;
         System.out.println("第二步 调用 set 方法设置属性值");
         }
         //创建执行的初始化的方法
         public void initMethod() {
         System.out.println("第三步 执行初始化的方法");
         }
         //创建执行的销毁的方法
         public void destroyMethod() {
         System.out.println("第五步 执行销毁的方法");
         }
        }
12345678910111213141516171819
```

------

```java
public class MyBeanPost implements BeanPostProcessor {//创建后置处理器实现类
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("在初始化之前执行的方法");
        return bean;
    }
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("在初始化之后执行的方法");
        return bean;
    }
}
123456789101112
<!--配置文件的bean参数配置-->
<bean id="orders" class="com.atguigu.spring5.bean.Orders" init-method="initMethod" destroy-method="destroyMethod">	<!--配置初始化方法和销毁方法-->
    <property name="oname" value="手机"></property><!--这里就是通过set方式（注入属性）赋值-->
</bean>

<!--配置后置处理器-->
<bean id="myBeanPost" class="com.atguigu.spring5.bean.MyBeanPost"></bean>
1234567
 @Test
 public void testBean3() {
// ApplicationContext context =
// new ClassPathXmlApplicationContext("bean4.xml");
 ClassPathXmlApplicationContext context =
 new ClassPathXmlApplicationContext("bean4.xml");
 Orders orders = context.getBean("orders", Orders.class);
 System.out.println("第四步 获取创建 bean 实例对象");
 System.out.println(orders);
 //手动让 bean 实例销毁
 context.close();
 }
123456789101112
```

> **4、bean 的后置处理器，bean 生命周期有七步** （正常生命周期为五步，而配置后置处理器后为七步）
>
>  （1）通过构造器创建 bean 实例（无参数构造）
>
>  （2）为 bean 的属性设置值和对其他 bean 引用（调用 set 方法）
>
>  （3）把 bean 实例传递 bean 后置处理器的方法 postProcessBeforeInitialization
>
>  （4）调用 bean 的初始化的方法（需要进行配置初始化的方法）
>
>  （5）把 bean 实例传递 bean 后置处理器的方法 postProcessAfterInitialization
>
>  （6）bean 可以使用了（对象获取到了）
>
>  （7）当容器关闭时候，调用 bean 的销毁的方法（需要进行配置销毁的方法）

------

 **4、IOC 操作 Bean 管理(外部属性文件)**

> **方式一：直接配置数据库信息** ：（1）配置Druid（德鲁伊）连接池 （2）引入Druid（德鲁伊）连接池依赖 jar 包

```xml
<!--直接配置连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"></property>
        <property name="url" value="jdbc:mysql://localhost:3306/userDb"></property>
        <property name="username" value="root"></property>
        <property name="password" value="root"></property>
    </bean>
1234567
```

> **方式二：引入外部属性文件配置数据库连接池**
>
> （1）创建外部属性文件，properties 格式文件，写数据库信息（**jdbc.properties**）

```properties
    prop.driverClass=com.mysql.jdbc.Driver
    prop.url=jdbc:mysql://localhost:3306/userDb
    prop.userName=root
    prop.password=root
1234
```

> （2）把外部 properties 属性文件引入到 spring 配置文件中 —— 引入 context 名称空间

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd"><!--引入context名称空间-->
    
        <!--引入外部属性文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--配置连接池-->
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="${prop.driverClass}"></property>
        <property name="url" value="${prop.url}"></property>
        <property name="username" value="${prop.userName}"></property>
        <property name="password" value="${prop.password}"></property>
    </bean>
    
</beans>
```





# spring IOC容器 Bean 管理——基于注解方式

![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/original-20201230092251914.png)

置顶 [来点淦货](https://blog.csdn.net/weixin_45496190) 2020-07-01 20:17:49 ![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/articleReadEyes-20201230092251914.png) 2544 ![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/tobarCollect-20201230092251925.png) 收藏 63 ![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/planImg-20201230092251914.png) 原力计划

分类专栏： [spring](https://blog.csdn.net/weixin_45496190/category_9776864.html) 文章标签： [spring](https://www.csdn.net/tags/MtTaEg0sMDg2NTAtYmxvZwO0O0OO0O0O.html)

版权

## IOC 操作 Bean 管理(基于注解方式)

 **1、什么是注解**

 （1）注解是代码特殊标记，格式：@注解名称(属性名称=属性值, 属性名称=属性值…)

 （2）使用注解，注解作用在类上面，方法上面，属性上面

 （3）使用注解目的：简化 xml 配置

 **2、Spring 针对 Bean 管理中创建对象提供注解**

 下面四个注解功能是一样的，都可以用来创建 bean 实例

 （1）@Component

 （2）@Service

 （3）@Controller

 （4）@Repository

 **3、基于注解方式实现对象创建**

 第一步 引入依赖 （引入**spring-aop jar包**）

 第二步 开启组件扫描

```xml
<!--开启组件扫描
 1 如果扫描多个包，多个包使用逗号隔开
 2 扫描包上层目录
-->
<context:component-scan base-package="com.atguigu"></context:component-scan>
12345
```

 第三步 创建类，在类上面添加创建对象注解

```java
//在注解里面 value 属性值可以省略不写，
//默认值是类名称，首字母小写
//UserService -- userService
@Component(value = "userService") //注解等同于XML配置文件：<bean id="userService" class=".."/>
public class UserService {
 public void add() {
 System.out.println("service add.......");
 }
}
123456789
```

 **4、开启组件扫描细节配置**

```xml
<!--示例 1
 use-default-filters="false" 表示现在不使用默认 filter，自己配置 filter
 context:include-filter ，设置扫描哪些内容
-->
<context:component-scan base-package="com.atguigu" use-defaultfilters="false">
 <context:include-filter type="annotation"

expression="org.springframework.stereotype.Controller"/><!--代表只扫描Controller注解的类-->
</context:component-scan>
<!--示例 2
 下面配置扫描包所有内容
 context:exclude-filter： 设置哪些内容不进行扫描
-->
<context:component-scan base-package="com.atguigu">
 <context:exclude-filter type="annotation"

expression="org.springframework.stereotype.Controller"/><!--表示Controller注解的类之外一切都进行扫描-->
</context:component-scan>
123456789101112131415161718
```

 **5、基于注解方式实现属性注入**

 （1）@Autowired：根据属性类型进行自动装配

 第一步 把 service 和 dao 对象创建，在 service 和 dao 类添加创建对象注解

第二步 在 service 注入 dao 对象，在 service 类添加 dao 类型属性，在属性上面使用注解

```java
@Service
public class UserService {
 //定义 dao 类型属性
 //不需要添加 set 方法
 //添加注入属性注解
 @Autowired
 private UserDao userDao;
 public void add() {
 System.out.println("service add.......");
 userDao.add();
 }
}

//Dao实现类
@Repository
//@Repository(value = "userDaoImpl1")
public class UserDaoImpl implements UserDao {
    @Override
    public void add() {
        System.out.println("dao add.....");
    }
}
12345678910111213141516171819202122
```

 （2）@Qualifier：根据名称进行注入，这个@Qualifier 注解的使用，和上面@Autowired 一起使用

```java
//定义 dao 类型属性
//不需要添加 set 方法
//添加注入属性注解
@Autowired //根据类型进行注入
//根据名称进行注入（目的在于区别同一接口下有多个实现类，根据类型就无法选择，从而出错！）
@Qualifier(value = "userDaoImpl1") 
private UserDao userDao;
1234567
```

 （3）@Resource：可以根据类型注入，也可以根据名称注入（它属于javax包下的注解，不推荐使用！）

```java
//@Resource //根据类型进行注入
@Resource(name = "userDaoImpl1") //根据名称进行注入
private UserDao userDao;
123
```

 （4）@Value：注入普通类型属性

```java
@Value(value = "abc")
private String name
12
```

 **6、完全注解开发**

 （1）创建配置类，替代 xml 配置文件

```java
@Configuration //作为配置类，替代 xml 配置文件
@ComponentScan(basePackages = {"com.atguigu"})
public class SpringConfig {
    
}
12345
```

 （2）编写测试类

```java
@Test
public void testService2() {
 //加载配置类
 ApplicationContext context
 = new AnnotationConfigApplicationContext(SpringConfig.class);
 UserService userService = context.getBean("userService",
UserService.class);
 System.out.println(userService);
 userService.add();
}
```





# Spring-AOP概念及使用教程

![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/original-20201230092320131.png)

[来点淦货](https://blog.csdn.net/weixin_45496190) 2020-07-02 13:52:25 ![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/articleReadEyes-20201230092320130.png) 2079 ![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/tobarCollect-20201230092320131.png) 收藏 55 ![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/planImg-20201230092320131.png) 原力计划

分类专栏： [spring](https://blog.csdn.net/weixin_45496190/category_9776864.html) 文章标签： [spring](https://www.csdn.net/tags/MtTaEg0sMDg2NTAtYmxvZwO0O0OO0O0O.html) [java](https://www.csdn.net/tags/NtTaIg5sMzYyLWJsb2cO0O0O.html)

版权

## Spring-AOP

### **1、AOP 基本概念**

 （1）面向切面编程（方面），利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得 业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

 （2）通俗描述：不通过修改源代码方式，在主干功能里面添加新功能

 （3）使用登录例子说明 AOP

![在这里插入图片描述](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTQ5NjE5MA==,size_16,color_FFFFFF,t_70.png)

### **2、AOP（底层原理）**

 a）AOP 底层使用动态代理 ，动态代理有两种情况：

> 第一种 有接口情况，使用 JDK 动态代理 ；创建**接口实现类代理对象**，增强类的方法
> ![在这里插入图片描述](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTQ5NjE5MA==,size_16,color_FFFFFF,t_70-20201230092320250.png)

> 第二种 没有接口情况，使用 CGLIB 动态代理；创建**子类的代理对象**，增强类的方法
> ![在这里插入图片描述](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NTQ5NjE5MA==,size_16,color_FFFFFF,t_70-20201230092320225.png)

### **3、AOP（JDK 动态代理）**

 1）使用 JDK 动态代理，使用 Proxy 类里面的方法创建代理对象

> 调用 newProxyInstance 方法，方法有三个参数：
>
> ```java
> public static Object newProxyInstance(ClassLoader loader,
>                                       Class<?>[] interfaces,
>                                       InvocationHandler h)
> 123
> ```
>
>  第一参数，类加载器
>
>  第二参数，增强方法所在的类，这个类实现的接口，*支持多个接口*
>
>  第三参数，实现这个接口 InvocationHandler，创建代理对象，写增强的部分

 2）编写 JDK 动态代理代码

```java
//（1）创建接口，定义方法
public interface UserDao {
 public int add(int a,int b);
 public String update(String id);
}
12345
//（2）创建接口实现类，实现方法
public class UserDaoImpl implements UserDao {
 @Override
 public int add(int a, int b) {
 return a+b;
 }
 @Override
 public String update(String id) {
 return id;
 }
}
1234567891011
//（3）使用 Proxy 类创建接口代理对象
public class JDKProxy {
 public static void main(String[] args) {
 //创建接口实现类代理对象
 Class[] interfaces = {UserDao.class};
 UserDaoImpl userDao = new UserDaoImpl(); 
/** 第一参数，类加载器 
	第二参数，增强方法所在的类，这个类实现的接口，(支持多个接口)
	第三参数，实现这个接口 InvocationHandler，创建代理对象，写增强的部分  */
 UserDao dao =(UserDao)Proxy.newProxyInstance(JDKProxy.class.getClassLoader(), interfaces,
					new UserDaoProxy(userDao));
 int result = dao.add(1, 2);
 System.out.println("result:"+result);
 }
}

//创建代理对象代码
class UserDaoProxy implements InvocationHandler {
 //1 把创建的是谁的代理对象，把谁传递过来
 //有参数构造传递
 private Object obj;
 public UserDaoProxy(Object obj) {
 this.obj = obj;
 }
 //增强的逻辑
 @Override
 public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
 //方法之前
 System.out.println("方法之前执行...."+method.getName()+" :传递的参数..."+ Arrays.toString(args));
 //被增强的方法执行
 Object res = method.invoke(obj, args);
 //方法之后
 System.out.println("方法之后执行...."+obj);
 return res;
 }
}
123456789101112131415161718192021222324252627282930313233343536
```

### **4、AOP（术语）**

 a）连接点：类里面哪些方法可以被增强，这些方法称为连接点

 b）切入点：实际被真正增强的方法称为切入点

 c）通知（增强）：实际增强的逻辑部分称为通知，且分为以下五种类型：

 1）前置通知 2）后置通知 3）环绕通知 4）异常通知 5）最终通知

 d）切面：把通知应用到切入点过程

### **5、AOP操作**

 a）Spring 框架一般都是基于 AspectJ 实现 AOP 操作，AspectJ 不是 Spring 组成部分，独立 AOP 框架，一般把 AspectJ 和 Spirng 框架一起使 用，进行 AOP 操作

 b）基于 AspectJ 实现 AOP 操作：1）基于 xml 配置文件实现 （2）基于注解方式实现（使用）

 c）引入相关jar包

 d）切入点表达式，如下：

```java
（1）切入点表达式作用：知道对哪个类里面的哪个方法进行增强 
（2）语法结构： execution([权限修饰符] [返回类型] [类全路径] [方法名称]([参数列表]) )
（3）例子如下：
    例 1：对 com.atguigu.dao.BookDao 类里面的 add 进行增强
		execution(* com.atguigu.dao.BookDao.add(..))
 	例 2：对 com.atguigu.dao.BookDao 类里面的所有的方法进行增强
		execution(* com.atguigu.dao.BookDao.* (..))
    例 3：对 com.atguigu.dao 包里面所有类，类里面所有方法进行增强
		execution(* com.atguigu.dao.*.* (..))
123456789
```

### **6、AOP 操作（AspectJ 注解）**

```java
//1、创建类，在类里面定义方法
public class User {
 public void add() {
 System.out.println("add.......");
 }
}
//2、创建增强类（编写增强逻辑）
//（1）在增强类里面，创建方法，让不同方法代表不同通知类型
//增强的类
public class UserProxy {
 public void before() {//前置通知
 System.out.println("before......");
 }
}

123456789101112131415
<!--3、进行通知的配置-->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
                        http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd">
    <!-- 开启注解扫描 -->
    <context:component-scan base-package="com.atguigu.spring5.aopanno"></context:component-scan>

    <!-- 开启Aspect生成代理对象-->
    <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
</beans>
123456789101112131415
//增强的类
@Component
@Aspect  //生成代理对象
public class UserProxy {}

//被增强的类
@Component
public class User {}
12345678
```

------

```java
//4、配置不同类型的通知
@Component
@Aspect  //生成代理对象
public class UserProxy {
      //相同切入点抽取
    @Pointcut(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void pointdemo() {

    }

    //前置通知
    //@Before注解表示作为前置通知
    @Before(value = "pointdemo()")//相同切入点抽取使用！
    public void before() {
        System.out.println("before.........");
    }

    //后置通知（返回通知）
    @AfterReturning(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void afterReturning() {
        System.out.println("afterReturning.........");
    }

    //最终通知
    @After(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void after() {
        System.out.println("after.........");
    }

    //异常通知
    @AfterThrowing(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void afterThrowing() {
        System.out.println("afterThrowing.........");
    }

    //环绕通知
    @Around(value = "execution(* com.atguigu.spring5.aopanno.User.add(..))")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        System.out.println("环绕之前.........");

        //被增强的方法执行
        proceedingJoinPoint.proceed();

        System.out.println("环绕之后.........");
    }
}

1234567891011121314151617181920212223242526272829303132333435363738394041424344454647
```

### **7、有多个增强类对同一个方法进行增强，设置增强类优先级**

```java
//（1）在增强类上面添加注解 @Order(数字类型值)，数字类型值越小优先级越高
@Component
@Aspect
@Order(1)
public class PersonProxy{ }

123456
```

### **8、AOP 操作（AspectJ 配置文件）**

```xml
<!--1、创建两个类，增强类和被增强类，创建方法（同上一样）-->
<!--2、在 spring 配置文件中创建两个类对象-->
<!--创建对象-->
<bean id="book" class="com.atguigu.spring5.aopxml.Book"></bean>
<bean id="bookProxy" class="com.atguigu.spring5.aopxml.BookProxy"></bean>
<!--3、在 spring 配置文件中配置切入点-->
<!--配置 aop 增强-->
<aop:config>
 <!--切入点-->
 <aop:pointcut id="p" expression="execution(* com.atguigu.spring5.aopxml.Book.buy(..))"/>
 <!--配置切面-->
 <aop:aspect ref="bookProxy">
 <!--增强作用在具体的方法上-->
 <aop:before method="before" pointcut-ref="p"/>
 </aop:aspect>
</aop:config>
```



# 在spring中使用JdbcTemplate进行数据库管理操作

![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/original-20201230092338283.png)

[来点淦货](https://blog.csdn.net/weixin_45496190) 2020-07-02 20:06:10 ![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/articleReadEyes-20201230092338283.png) 1965 ![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/tobarCollect-20201230092338283.png) 收藏 51 ![img](/Users/houyao/Documents/notes/typora/javanotes/4%E9%83%A8%E5%88%86%EF%BC%9A%E4%B8%BB%E6%B5%81%E6%A1%86%E6%9E%B6%E5%92%8C%E9%A1%B9%E7%9B%AE%E7%AE%A1%E7%90%86/Spring/planImg-20201230092338283.png) 原力计划

分类专栏： [spring](https://blog.csdn.net/weixin_45496190/category_9776864.html) 文章标签： [java](https://www.csdn.net/tags/NtTaIg5sMzYyLWJsb2cO0O0O.html) [spring](https://www.csdn.net/tags/MtTaEg0sMDg2NTAtYmxvZwO0O0OO0O0O.html)

版权

## JdbcTemplate

### 1、JdbcTemplate概念及使用

 a）Spring 框架对 JDBC 进行封装，使用 JdbcTemplate 方便实现对数据库操作

 b）引入相关 jar 包

 c）在 spring 配置文件配置数据库连接池

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
 destroy-method="close">
 <property name="url" value="jdbc:mysql:///test" />
 <property name="username" value="root" />
 <property name="password" value="root" />
 <property name="driverClassName" value="com.mysql.jdbc.Driver" />
</bean>
1234567
```

 d）配置 JdbcTemplate 对象，注入 DataSource

```xml
<!-- JdbcTemplate 对象 -->
<bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
 <!--注入 dataSource-->
 <property name="dataSource" ref="dataSource"></property><!--set方式注入-->
</bean>
12345
```

 e）创建 service 类，创建 dao 类，在 dao 注入 jdbcTemplate 对象

```xml
<!-- 组件扫描 -->
<context:component-scan base-package="com.atguigu"></context:component-scan>
12
@Service
public class BookService {
 //注入 dao
 @Autowired
 private BookDao bookDao;
}

@Repository
public class BookDaoImpl implements BookDao {
 //注入 JdbcTemplate
 @Autowired
 private JdbcTemplate jdbcTemplate;
}
12345678910111213
```

### 2、JdbcTemplate 操作数据库（添加）

 a）对应数据库创建实体类

 b）创建service和dao

 （1）在 dao 进行数据库添加操作

 （2）调用 JdbcTemplate 对象里面 update 方法实现添加操作

```java
@Repository
public class BookDaoImpl implements BookDao {
 //注入 JdbcTemplate
 @Autowired
 private JdbcTemplate jdbcTemplate;
 //添加的方法
 @Override
 public void add(Book book) {
 //1 创建 sql 语句
 String sql = "insert into t_book values(?,?,?)";
 //2 调用方法实现
 Object[] args = {book.getUserId(), book.getUsername(),book.getUstatus()};
 int update = jdbcTemplate.update(sql,args);
 System.out.println(update);
 }
}
12345678910111213141516
```

### 3、JdbcTemplate 操作数据库（修改和删除）

```java
//1、修改
@Override
public void updateBook(Book book) {
 String sql = "update t_book set username=?,ustatus=? where user_id=?";
 Object[] args = {book.getUsername(), book.getUstatus(),book.getUserId()};
 int update = jdbcTemplate.update(sql, args);
 System.out.println(update);
}
//2、删除
@Override
public void delete(String id) {
 String sql = "delete from t_book where user_id=?";
 int update = jdbcTemplate.update(sql, id);
 System.out.println(update);
}
//使用JdbcTemplate 模板所实现的 “增删改” 都是调用了同一个 “update” 方法
12345678910111213141516
```

### 4、JdbcTemplate 操作数据库（查询返回某个值）

```java
//查询表记录数
@Override
public int selectCount() {
 String sql = "select count(*) from t_book";
//queryForObject方法中：第一个参数代表--sql语句；第二个参数代表--返回类型class  
 Integer count = jdbcTemplate.queryForObject(sql, Integer.class);
 return count;
}
JdbcTemplate 操作数据库（
123456789
```

### 5、JdbcTemplate 操作数据库（查询返回对象）

```java
//查询返回对象
@Override
public Book findBookInfo(String id) {
 String sql = "select * from t_book where user_id=?";
 //调用方法
/*
	queryForObject方法中：
		第一个参数：sql语句
		第二个参数：RowMapper 是接口，针对返回不同类型数据，使用这个接口里面 实现类 完成数据封装
		第三个参数：sql 语句值
*/
 Book book = jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper<Book>(Book.class), id);
 return book;
}
1234567891011121314
```

### 6、JdbcTemplate 操作数据库（查询返回集合）

```java
//所用场景：查询图书列表分页、、
//查询返回集合
@Override
public List<Book> findAllBook() {
 String sql = "select * from t_book";
 //调用方法
 List<Book> bookList = jdbcTemplate.query(sql, new BeanPropertyRowMapper<Book>(Book.class));
 return bookList;
}
123456789
```

### 7、JdbcTemplate 操作数据库（批量操作）

```java
//批量添加
@Override
public void batchAddBook(List<Object[]> batchArgs) {
 String sql = "insert into t_book values(?,?,?)";
//batchUpdate方法 第一个参数：sql语句		第二个参数：List集合，添加多条记录数据
 int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
 System.out.println(Arrays.toString(ints));
}

//批量添加测试
List<Object[]> batchArgs = new ArrayList<>();
Object[] o1 = {"3","java","a"};
Object[] o2 = {"4","c++","b"};
Object[] o3 = {"5","MySQL","c"};
batchArgs.add(o1);
batchArgs.add(o2);
batchArgs.add(o3);
//调用批量添加
bookService.batchAdd(batchArgs);
12345678910111213141516171819
```

### 8、JdbcTemplate 实现批量修改操作

```java
//批量修改(同批量添加一样，调用同一个方法)
@Override
public void batchUpdateBook(List<Object[]> batchArgs) {
 String sql = "update t_book set username=?,ustatus=? where user_id=?";
 int[] ints = jdbcTemplate.batchUpdate(sql, batchArgs);
 System.out.println(Arrays.toString(ints));
}
```