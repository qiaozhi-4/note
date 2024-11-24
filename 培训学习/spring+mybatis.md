# 1.【xml】方式整合

#### 依赖：

```xml
        <!--spring-->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring-version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>${spring-version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring-version}</version>
        </dependency>

        <!--servlet-->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>4.0.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>javax.servlet.jsp-api</artifactId>
            <version>2.3.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>

        <!--mysql-->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>druid</artifactId>
            <version>1.2.9</version>
        </dependency>

        <!--mybatis依赖-->
        <!--mybatis-plus-->
        <dependency>
            <groupId>com.baomidou</groupId>
            <artifactId>mybatis-plus</artifactId>
            <version>3.5.1</version>
        </dependency>
        <!--分页-->
        <dependency>
            <groupId>com.github.pagehelper</groupId>
            <artifactId>pagehelper</artifactId>
            <version>5.3.0</version>
        </dependency>

        <!--logback+slf4j日志-->
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>jcl-over-slf4j</artifactId>
            <version>1.7.32</version>
        </dependency>
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.11</version>
            <scope>runtime</scope>
        </dependency>

        <!--lombok-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.24</version>
            <scope>provided</scope>
        </dependency>

        <!--测试-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring-version}</version>
            <scope>test</scope>
        </dependency>
```







#### spring.xml

##### spring+mybatis步骤：

1. 配置获取sql连接的bean

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
   ```

2. 配置mybatis的bean

   ```xml
   <!--省略mybatis.xml-->
   <!--使用MybatisSqlSessionFactoryBean配置mybatis.xml-->
   <!--配置mybatis中的factory-->
   <bean id="mybatisSqlSessionFactoryBuilder" class="com.baomidou.mybatisplus.extension.spring.MybatisSqlSessionFactoryBean">
       <!--配置mybatis需要的数据库连接-->
       <property name="dataSource" ref="dataSource"/>
       
       <!--其他配置：缓存、插件、别名、下横线转驼峰、xml配置文件位置等-->
       
       <!--扫描接口的xml【这里是扫描mappers下所有的xml】-->
       <!--<property name="mapperLocations" value="classpath:mappers/*.xml"/>-->
       <!--扫描mybatis.xml【可以不要mybatis.xml】-->
       <!--<property name="configLocation" value="classpath:mybatis.xml"/>-->
   </bean>
   
   
   <!--省略xxxmapper.xml-->
   <!--使用mybatis-plus注解，开启注解扫描-->
   <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
       <!--扫描指定的包：包包含接口-->
       <property name="basePackage" value="com.xh.mapper"/>
       <!--指定扫描的注解类型：可以省略-->
       <property name="annotationClass" value="org.apache.ibatis.annotations.Mapper"/>
   </bean>
   ```

3. 给需要使用mybatis-plus的类配置

   ```xml
   <bean id="userService" class="com.xh.service.UserService">
   <constructor-arg name="factory"  ref="mybatisSqlSessionFactoryBuilder"/>
   </bean>
   ```



##### AOP管理事务

使用spring管理事务

   -  tx： 表示事务相关的命名空间
   -  tx:advice： 定义切面中的增强方法
   -  transaction-manager 配置管理事务的bean，默认名字为：transactionManager
   - tx:method： 定义如何增强事务相关的方法
        - name：表示哪些方法需要被管理【*：表示全部方法】
        - propagation：表示传播属性，事务传播性质【增删改默认就行，不用写】
          	- REQUIRED - 必须提供事务，用在【增删改】
            - SUPPORTS - 有事务就跟随事务，无事务直接运行，用在【查询】
        - read-only：只读事务，用在【查询】，作用：提高性能
        - isolation：隔离级别【增删改默认就行，不用写】
            -  READ-UNCOMMITTED 读未提交，可能出现脏读、不可重复度、幻读
            -  READ-COMMITTED 读已提交，可能出现不可重复读、幻读（oracle）
            -  REPEATABLE-READ 可重复读，可能会出现幻读（mysql）
            -  SERIALIZABLE 串行化，消灭一切
        -  rollback-for：在何种情况下回滚，默认RuntimeException【增删改默认就行，不用写】
   - aop:advisor： 定义一个事务增强，相当于定义了aspect切面的所有方法，直接配置即可


```xml
<!--管理事务-->
<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>
<tx:advice id="myAdvice" transaction-manager="transactionManager">
    <tx:attributes>
        <tx:method name="*"
                   propagation="REQUIRED"
                   read-only="false"
                   isolation="DEFAULT"
                   rollback-for="java.lang.RuntimeException"/>
        <tx:method name="find*" propagation="SUPPORTS" read-only="true"/>
    </tx:attributes>
</tx:advice>
<aop:config>
    <aop:pointcut id="p1" expression="execution(* com.xh.service.*.*(..))"/>
    <aop:advisor pointcut-ref="p1" advice-ref="myAdvice"/>
</aop:config>
```







# 2. 【注解】方式整合

#### 【配置类】

```java
//- 与配置相关的bean
@Configuration
//配置需要扫描的包
@ComponentScan(basePackages = "com.xh")
//读取【driver】配置文件，参数为：读取的文件名
@PropertySource("classpath:jdbc.properties")
//设置mybatis-plus需要扫描mapper的接口
@MapperScan(basePackages = {"com/xh/mapper"})
//开启事务管理
@EnableTransactionManagement
public class MybatisConfig {

    //文件里面的属性
    @Value("${jdbc.driver}")
    private String className;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String user;
    @Value("${jdbc.password}")
    private String pass;

    //配置连接池
    @Bean
    public DataSource createDataSource(){
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        //配置连接池的属性
        dataSource.setDriverClassName(className);
        dataSource.setUrl(url);
        dataSource.setUsername(user);
        dataSource.setPassword(pass);
        return dataSource;
    }

    //配置事务
    @Bean
    public PlatformTransactionManager createManager(DataSource dataSource){
        DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
        transactionManager.setDataSource(dataSource);
        return transactionManager;
    }

    //配置mybatis
    @Bean
    public MybatisSqlSessionFactoryBean transactionManager(DataSource  dataSource){
        MybatisSqlSessionFactoryBean mybatisSqlSessionFactoryBean = new MybatisSqlSessionFactoryBean();
        //需要dataSource连接池
        mybatisSqlSessionFactoryBean.setDataSource(dataSource);
        //配置mybatis-plus自带分页
        MybatisPlusInterceptor interceptor = new MybatisPlusInterceptor();
        interceptor.addInnerInterceptor(new PaginationInnerInterceptor(DbType.MYSQL));
        mybatisSqlSessionFactoryBean.setPlugins(interceptor);
        return mybatisSqlSessionFactoryBean;
    }

}

```









#### 【实体类】

```xml
//自动生成get/set/toString
@Data
//自动生成全参构造函数
@AllArgsConstructor
//自动生成无参构造函数
@NoArgsConstructor
//标记是这个表
@TableName("order2")
public class Order {
	//标记主键，type：自增
    @TableId(value = "id",type = IdType.AUTO)
    private Integer id;
    @TableField("user_id")
    private Integer userId;
    @TableField("info")
    private String info;
    @TableField("address")
    private String address;
    @TableField("price")
    private Double price;
}
```





 

#### 【mapper接口】

```java
@Mapper
public interface IUserMapper  extends BaseMapper<User> {
}

```





#### 【Service服务层】



###### 自己实现功能

```java
//- 服务层相关的bean
@Service
//生产带final属性的构造函数
@RequiredArgsConstructor
//配置事务（配置所有方法）
@Transactional
public class UserService {

    private final IUserMapper userMapper;

    //配置事务（单个方法）
    @Transactional(propagation = Propagation.SUPPORTS, readOnly = true)
    public void find() {
        System.out.println(userMapper.selectOne(new QueryWrapper<User>().eq("id", 1)));
    }

    //借钱事务
    public void transaction(int id1, int id2, double money) {
        User user1 = userMapper.selectOne(new QueryWrapper<User>().eq("id", id1));
        User user2 = userMapper.selectOne(new QueryWrapper<User>().eq("id", id2));
        user1.setMoney(user1.getMoney()-money);
        user2.setMoney(user2.getMoney()+money);
        userMapper.updateById(user1);
        System.out.println("钱以借出！");
        System.out.println(1/0);
        userMapper.updateById(user2);
        System.out.println("钱以收到！");
    }
}
```



###### IService实现

1. 定义接口继承【 IUserService extends IService<自己的实体类>】

   ```java
   public interface IUserService extends IService<User> {
   }
   ```

2. 定义实现类【UserServicePlus extends ServiceImpl<Mapper接口, 自己的实体类> implements IUserService 】

   继承【ServiceImpl<IUserMapper, User>】会帮我们实现接口【IService<User>】

   ```java
   //- 服务层相关的bean
   @Service
   public class UserServicePlus extends ServiceImpl<IUserMapper, User> implements IUserService {
   }
   ```

   







#### 【测试】

```java
//获取配置类
@ContextConfiguration(classes = {MybatisConfig.class})
//规定写法
@RunWith(SpringJUnit4ClassRunner.class)
public class Test1 {

    @Autowired
    private UserService userService;
    @Autowired
    private IUserService userServicePlus;

    @Test
    public void test1() {
        userService.transaction(1,2,1);
    }

    //分页测试
    @Test
    public void test3() {
        Page<User> page =  userService.findPage();
        page.getRecords().forEach(System.out::println);
    }
    
    //测试【userServicePlus】
    @Test
    public void test2() {
        userServicePlus.list().forEach(System.out::println);
    }

}
```



