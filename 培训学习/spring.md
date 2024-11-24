### jar包



	org.springframework:spring-context:5.3.10
	org.springframework:spring-core:5.3.10
	org.springframework:spring-beans:5.3.10
	org.springframework:spring-expression:5.3.10









### spring.xml构造bean

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
                           https://www.springframework.org/schema/beans/spring-beans.xsd">
    <!--配置Spring的bean对象：
        在spring中每一个对象就是一个bean：
            id - 表示bean的名字，必须唯一！
            class - 表示bean的类型，必须要能被new出来
			无法使用接口去配置一个Bean对象：
        	但是可以根据接口找到一个实现了该接口的bean对象！！！
			每一个bean都是不同的对象！即使类型相同！-->
    <bean id="userDao1" class="com.xh.dao.UserDao1"/>

    <bean id="userService1" class="com.xh.service.UserService1">
        <!--使用constructor-arg表示依赖的构造函数参数（给构造方法的参数赋值）：
			三选一：
            type="" 表示构造函数的这个参数的类型，根据类型区分构造哪一个参数
            index="" 表示参数的下标，就是第几个，0表示第一个构造参数
            name="" 表示参数的名字
			二选一：
            value="" 参数的值：一般是普通类型（基本类型），比如整数、小数、字符串等
            ref="" 指向另一个bean的id（引用）
            如果只有一个参数，那么可以省略type/index/name-->
        <constructor-arg name="userDao1" ref="userDao1"/>
    </bean>
    
    <!--scope表示bead对象是单例还是多例：
        singleton 默认单例
        prototype 多列-->
    <bean id="user" class="com.zking.entity.User" scope="prototype">
        <!--使用构造函数构造这个对象-->
        <constructor-arg index="0" value="666"/>
        <constructor-arg name="name" value="老王"/>
    </bean>
   
    
     <!--创建一个复杂属性的对象：
        1. 使用bean引用作为赋值对象时，
            可以使用ref引入另一个bean，
            可以直接在内部标签中创建一个独有的bean对象
        2. 给一个List/Map/Set/Array赋值的时候，可以使用对应的标签：
            <list> <map> <set> <array>
            内部使用<value>表示普通类型的容器
            内部使用<bean>表示引用类型的容器-->
    <bean id="userService2" class="com.xh.service.UserService2">
        <!--构造函数的【对象】参数-->
        <constructor-arg name="userDao1">
            <bean class="com.xh.dao.UserDao1"/>
        </constructor-arg>
        <!--构造函数的【list集合】参数-->
        <constructor-arg name="list">
            <list>
                <value>11</value>
                <value>22</value>
                <value>33</value>
            </list>
        </constructor-arg>
        <!--类的属性【set集合】-->
        <property name="set">
            <set>
                <value>11</value>
                <value>22</value>
                <value>33</value>
            </set>
        </property>
        <!--类的属性【map集合】-->
        <property name="map">
            <map>
                <entry key="k1" value="1"/>
                <entry key="k2" value="2"/>
            </map>
        </property>
        <!--类的属性【数组】-->
        <property name="users">
            <array>
                <!--数组里面是【其他对象】-->
                <bean class="com.xh.entity.User" >
                    <property name="id" value="1"/>
                    <property name="name" value="a1"/>
                </bean>
            </array>
        </property>
    </bean>
    
    <!--通过构造函数-->
    <bean id="user1" class="com.job.entity.User">
        <constructor-arg name="id" value="1"/>
        <constructor-arg name="name" value="qz"/>
        <constructor-arg name="pass" value="123"/>
    </bean>
    <!--通过property节点【参数的set】-->
    <bean id="user2" class="com.job.entity.User">
        <property name="id" value="2"/>
        <property name="name" value="tom"/>
        <property name="pass" value="456"/>
    </bean>
    <!--通过p命名空间方式-->
    <bean id="user3" class="com.job.entity.User" p:id="3" p:name="pq" p:pass="789"/>
</beans>
```











#### bean对象单例 / 多例

【scope】表示bead对象是单例还是多例：
		【singleton】 默认单例
        【prototype】 多列


```xml
<bean id="user" class="com.zking.entity.User" scope="prototype">
    <!--使用构造函数构造这个对象-->
    <constructor-arg index="0" value="666"/>
    <constructor-arg name="name" value="老王"/>
</bean>
```











#### bean对象【无参】

​	在spring中每一个对象就是一个bean
​	配置Spring的bean对象：
​        【id】 - 表示bean的名字，必须唯一！
​        【class】 - 表示bean的类型，必须要能被new出来
​		无法使用接口去配置一个Bean对象：
​    	但是可以根据接口找到一个实现了该接口的bean对象！！！
​		每一个bean都是不同的对象！即使类型相同！

```xml
 <bean id="userDao1" class="com.xh.dao.UserDao1"/>
```













#### bean对象【有参】

使用constructor-arg表示依赖的构造函数参数（给构造方法的参数赋值）：
   三选一：
            【type】="" 表示构造函数的这个参数的类型，根据类型区分构造哪一个参数
            【index】="" 表示参数的下标，就是第几个，0表示第一个构造参数
            【name】="" 表示参数的名字
   二选一：
            【value】="" 参数的值：一般是普通类型（基本类型），比如整数、小数、字符串等
            【ref】="" 指向另一个bean的id（引用）
            如果只有一个参数，那么可以省略type/index/name



##### 通过构造方法

```xml
<bean id="userService1" class="com.xh.service.UserService1">
    <constructor-arg name="userDao1" ref="userDao1"/>
</bean>
```



##### 通过set方法

```xml
<bean id="user2" class="com.job.entity.User">
        <property name="id" value="2"/>
        <property name="name" value="tom"/>
        <property name="pass" value="456"/>
</bean>
```



##### 通过命名空间

xml开头必须配置：xmlns:p="http://www.springframework.org/schema/p"

```xml
<bean id="user3" class="com.job.entity.User" p:id="3" p:name="pq" p:pass="789"/>
```









#### 创建一个复杂属性的bean对象

1. 使用bean引用作为赋值对象时，
            可以使用ref引入另一个bean，
            可以直接在内部标签中创建一个独有的bean对象
2. 给一个List/Map/Set/Array赋值的时候，可以使用对应的标签：
            <list> <map> <set> <array>
            内部使用<value>表示普通类型的容器
            内部使用<bean>表示引用类型的容器

```xml
<!--创建一个复杂属性的对象：
        -->
    <bean id="userService2" class="com.xh.service.UserService2">
        <!--构造函数的【对象】参数-->
        <constructor-arg name="userDao1">
            <bean class="com.xh.dao.UserDao1"/>
        </constructor-arg>
        <!--构造函数的【list集合】参数-->
        <constructor-arg name="list">
            <list>
                <value>11</value>
                <value>22</value>
                <value>33</value>
            </list>
        </constructor-arg>
        <!--类的属性【set集合】-->
        <property name="set">
            <set>
                <value>11</value>
                <value>22</value>
                <value>33</value>
            </set>
        </property>
        <!--类的属性【map集合】-->
        <property name="map">
            <map>
                <entry key="k1" value="1"/>
                <entry key="k2" value="2"/>
            </map>
        </property>
        <constructor-arg name="userMap">
            <map>
                <entry key="1" value-ref="user1"/>
                <entry key="2" value-ref="user2"/>
                <entry key="3" value-ref="user3"/>
            </map>
        </constructor-arg>
        <!--类的属性【数组】-->
        <property name="users">
            <array>
                <!--数组里面是【其他对象】-->
                <bean class="com.xh.entity.User" >
                    <property name="id" value="1"/>
                    <property name="name" value="a1"/>
                </bean>
            </array>
        </property>
    </bean>
```















### 使用【注解】构造bean



必须配置spring开启扫描，依然可以通过以下两种方式获取这个bean：

       1. by type：就是根据类型获取（安全地）【UserList.class】
       2. by name：默认就是类的首字母小写名字，相当于xml配置bean的id







#### xml开启注解扫描

【base-package】扫描包路径

```xml
<!--开启注解扫描-->
<context:component-scan base-package="com.xh"/>
```









#### 【注解】开启注解扫描【替换xml】

【basePackages】扫描包路径

【basePackageClasses】需要扫描的类

```java
//配置需要扫描的包
@ComponentScan(basePackages = "com.xh")
```

单独配置某个类

```java
@Import({MyComponent.class})
```









#### 配置bean是否单例

【@Scope("prototype")】
    可以设置注解注入的bean对象的注入方式：
    prototype - 多例
    singleton - 单例，不配置时默认值









#### 类构造


可以使用这些注解自动注入bean：

【@Component("userDao1")】里面可以写该bean的id

```java
//- 通用、普通的bean、其他
@Component 
// - 数据库dao层相关的bean
@Repository
//- 服务层相关的bean
@Service 
//- 控制层相关的bean
@Controller
//- 与配置相关的bean
@Configuration
```









#### 方法构造--@Bean

【@Bean(initMethod = "init", destroyMethod = "close")】：
一般不写参数，这里是数据库连接池所以有【初始化方法】和【销毁方法】

```java
//读取的文件名
@PropertySource("classpath:driver.properties")
public class MyConfig {

//文件里面的属性
@Value("${jdbc.driver}")
private String className;
@Value("${jdbc.url}")
private String url;
@Value("${jdbc.username}")
private String user;
@Value("${jdbc.password}")
private String pass;

//一般不写参数，这里是数据库连接池所以有【初始化方法】和【销毁方法】
@Bean(initMethod = "init", destroyMethod = "close")
public DruidDataSource createDataSource(){
DruidDataSource dataSource = new DruidDataSource();
dataSource.setDriverClassName(className);
dataSource.setUrl(url);
dataSource.setUsername(user);
dataSource.setPassword(pass);
return dataSource;
}
```









### bean的字段（属性）



##### @Autowired

【@Autowired】可以使用spring自动注入另一个bean对象，可以用在：

1. 直接用在字段上，【不推荐】
2. 使用在setter方法上，自动注入依赖【推荐1】
3. 可以使用在参数上，比如构造函数、setter的参数【推荐2】
4. 不写，使用构造函数，无需加@Autowired【最推荐的方式】

注意：使用注解才能自动注入，XML配置不行！！！

参数：required表示是否必须注入，默认是true，可以设置为false

【@Qualifier("bean的名字")】

​    结合@Autowired使用，指定要注入的bean的特定名字
​    注意：注解中无法使用xml中配置的多个不同id的bean

```java
//1.直接用在字段上，【不推荐】
@Autowired
private UserDao1 userDao1;

//2. 使用在setter方法上，自动注入依赖【推荐1】
@Autowired
public void setUserDao1(UserDao1 userDao1) {}

//3. 可以使用在参数上，比如构造函数、setter的参数【推荐2】/
public UserService111(@Autowired UserDao1 userDao1) {
        this.userDao1 = userDao1;
    }
```







##### @Resource

类似@Autowired注册Bean，可以指定名称，相当于：@Autowire+@Qualifier
区别：@Resource 也可以指定注入类型；@Autowired 根据类型注入，如果存在同类型Bean则需要增加@Qualifier注解
另外：@Resource 可以用在类、方法中，他并不是Spring的注解，而是由J2EE提供

```java
//指定注入类型
@Resource(type = UserDao1.class)
private IUserDao userDao1;
```











### 获取bean

#### 【xml】获取

```java
ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
//传入类，不需要强转
UserList userList = context.getBean(UserList.class);
//传入id，需要强转
User user1 = (User) context.getBean("user1");
```



#### 【注解】获取

```java
ApplicationContext context = new AnnotationConfigApplicationContext("com/xh/config");
UserService userService = context.getBean(UserService.class);
List<User> users = userService.findAll();
```











### 配置数据库连接池



##### 【xml】获取

【location】读取的文件路径和名字

【file-encoding】读取的编码格式

```xml
<!--spring支持读取外部配置文件的属性值-->
<context:property-placeholder location="classpath:driver.properties" file-encoding="utf-8"/>

<!--使用spring创建数据库连接池-->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
    <property name="driverClassName" value="${jdbc.driver}"/>
    <property name="url" value="${jdbc.url}"/>
    <property name="username" value="${jdbc.username}"/>
    <property name="password" value="${jdbc.password}"/>
    <property name="initialSize" value="${jdbc.initialSize}"/>
    <property name="maxActive" value="${jdbc.maxActive}"/>
    <property name="maxWait" value="${jdbc.maxWait}"/>
</bean>

<bean id="userDao2" class="com.xh.dao.UserDao2">
    <constructor-arg ref="dataSource"/>
</bean>
```









##### 【注解】获取

【@PropertySource("classpath:driver.properties")】：读取这个文件

【@Value("${jdbc.driver}")】：文件里面的属性

```java
//读取的文件名
@PropertySource("classpath:driver.properties")
public class MyConfig {

//文件里面的属性
@Value("${jdbc.driver}")
private String className;
@Value("${jdbc.url}")
private String url;
@Value("${jdbc.username}")
private String user;
@Value("${jdbc.password}")
private String pass;

//一般不写参数，这里是数据库连接池所以有【初始化方法】和【销毁方法】
@Bean(initMethod = "init", destroyMethod = "close")
public DruidDataSource createDataSource(){
DruidDataSource dataSource = new DruidDataSource();
dataSource.setDriverClassName(className);
dataSource.setUrl(url);
dataSource.setUsername(user);
dataSource.setPassword(pass);
return dataSource;
}
```











### 测试bean配置

依赖：spring-test
注解：【@RunWith(SpringJUnit4ClassRunner.class) 】用于注解【测试类】
配置：【@ContextConfiguration(classes = {MyConfig.class}) 】测试使用的配置【一般为配置相关的bean】
			【@ContextConfiguration(locations = {"classpath:spring.xml"})】获取配置文件
使用：【@Autowired 】自动注入

```java
//获取配置类
@ContextConfiguration(classes = {MyConfig.class})
//规定写法
@RunWith(SpringJUnit4ClassRunner.class)
public class Test2 {
    //标记这是bean里面有的
    @Autowired
    private UserService1 userService1;
    //标记这是bean里面有的
    @Autowired
    private UserDao3 userDao3;

    @Test
    public void test1(){
        userService1.test();
        userDao3.find(1);
    }

}
```









### AOP面向切面编程

AOP相关关键字：
	Aspect-切面
	Pointcut-切入点
	Advice-通知，用于方法增强
	Joinpoint-连接点
	Introduction-引介
	Target-目标对象
	Weaving-织入
	Proxy-代理





#### AOP依赖：

spring-aop、spring-aspects





#### 配置方法1--【xml】

```xml
<!--AOP:面向切面编程
    需要：
        1.那些类需要增强【切入点】
        2.如何增强【方法，切面】
        <bean ...> 自定义类，包含增强时调用的方法
        <aop:config> 就是配置切点、切面、增强方法
        <aop:pointcut> 定义切点，表示哪些方法被增强
            id：切点的名字，可以定义很多切点
            expression：表达式，切入点的spEL表达式，指定哪些类的哪些方法
            表达式的意思：
                * com.zking.*.find*(..)
              返回值       包   方法  参数
              * 表示任意返回值、任意包、任意方法
              .. 表示任意参数
        <aop:before> 在方法运行之前增强
        <aop:after> 方法运行之后运行增强的方法
        <aop:after-returning> 方法正常返回后增强的方法
        <aop:after-throwing> 方法抛出异常后运行该方法
        <aop:around> 完全替换被增强的方法-->
<bean id="myAspect" class="com.xh.config.MyAspect"></bean>
<aop:config>
    <aop:pointcut id="p1" expression="execution(* com.xh.service.UserService.transaction(..))"/>
    <aop:aspect ref="myAspect">
        <aop:before method="before" pointcut-ref="p1"/>
        <aop:after method="after" pointcut-ref="p1"/>
        <aop:after-returning method="afterReturn" pointcut-ref="p1"/>
        <aop:after-throwing method="afterThrow" pointcut-ref="p1"/>

        <aop:around method="around" pointcut-ref="p1"/>
    </aop:aspect>
</aop:config>
```







#### 配置方法2--【注解】



##### spring注解

【@EnableAspectJAutoProxy】：在配置相关的bean开启切面代理



##### AOP注解

定义了【增强的方法】类配置为bean

【@Aspect】：表示切面，把类配置为切面，有定义的增强方法

【@Order(0)】：多个切面时，运行顺序，越小越早

【@Pointcut("execution(* com.xh..UserService.transaction(..))")】：
											用在方法上，表示一个切点(需要增强的方法)

【@Before("p1()")】：方法运行开始执行的增强方法（参数为配置切入点的方法）

【@After("p1()")】：方法运行结束的增强方法（参数为配置切入点的方法）

【@AfterReturning("p1()")】：方法运行结束，并且没有出现异常的增强方法（参数为配置切入点的方法）

【@AfterThrowing("p1()")】：方法运行结束，但是出现异常的增强方法（参数为配置切入点的方法）

【@Around("p1()")】：替换掉需要增强的方法体，基本不用（参数为配置切入点的方法）

```java

//- 通用、普通的bean、其他
@Component
//表示切面
@Aspect
//多个切面时，运行顺序，越小越早
@Order(0)
public class MyAspect {
    //注解一个方法，表示一个切点
    @Pointcut("execution(* com.xh..UserService.transaction(..))")
    public void p1(){}

    @Before("p1()")
    public void before() {
        System.out.println("运行开始");
    }

    @After("p1()")
    public void after() {
        System.out.println("运行结束");
    }

    @AfterReturning("p1()")
    public void afterReturn() {
        System.out.println("运行结束，正常返回");
    }

    @AfterThrowing("p1()")
    public void afterThrow() {
        System.out.println("运行结束，抛出异常");
    }

//    @Around("p1()")
//    public void around() {
//        System.out.println("---around---");
//    }
}

```











### JdbcTemplate

依赖：spring-jdbc

spring自带DriverManagerDataSource可以作为DataSource数据源

