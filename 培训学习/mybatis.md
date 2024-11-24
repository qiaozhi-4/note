### pom.xml配置需要的jar包

```xml
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
```

#### 配置日志

```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.17.2</version>
</dependency>
```

还在必须【resources】下创建【log4j2.properties】名字必须一样

```properties
status = warn
name = MyBatis-APP

filter.threshold.type = ThresholdFilter
filter.threshold.level = debug

appender.console.type = Console
appender.console.name = STDOUT
appender.console.layout.type = PatternLayout
appender.console.layout.pattern = [%d %p] [%t] %m%n

appender.rolling.type = File
appender.rolling.name = log
appender.rolling.append = true
appender.rolling.fileName = mybatis-log.log
appender.rolling.layout.type = PatternLayout
appender.rolling.layout.pattern = %d-%m%n

# debug < info < warn < error < fatal < off【这里设置显示什么级别的日志】
rootLogger.level = warn
rootLogger.appenderRef.stdout.ref = STDOUT
rootLogger.appenderRef.log.ref = log
```



### mybatis.xml配置数据库

在【src/main/resources】路径下创建

#### 配置全局参数

```xml
<!--全局参数配置-->
    <settings>
        <!--比如下划线自动转驼峰式-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!--开启二级缓存-->
        <setting name="cacheEnabled" value="true"/>
    </settings>
```



```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    
    <!--启用别名-->
    <typeAliases>
        <!--单个类设置别名：在xml配置里面直接使用student代替com.xh.pojo.Student-->
        <typeAlias type="com.xh.pojo.User"/>
        <!--直接将包中所有类使用别名-->
        <package name="com.xh.pojo"/>
    </typeAliases>
    
    <!--填写mybatis的插件：比如分页插件-->
    <plugins>
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
            <property name="helperDialect" value="oracle"/>
        </plugin>
    </plugins>

    <!--类型转换器:比如将数据jdbc的类型转为java指定类型-->
    <typeHandlers>
        <typeHandler handler="xxx"/>
        <package name="com.xh.xxx"/>
    </typeHandlers>
    
    <!-- 配置 mybatis 的环境 -->
    <environments default="mysql">
        <!-- 配置 mysql 的环境 -->
        <environment id="mysql">
            <!-- 配置事务的类型 -->
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!-- 配置连接数据库的信息 -->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
                <property name="username" value="root"/>
                <property name="password" value="12345678"/>
            </dataSource>
        </environment>
    </environments>
    
    <!-- 实体映射文件配置【resource:里面为映射接口的xml】 -->
    <mappers>
        <mapper resource="mappers/IStudentDao.xml"/>
    </mappers>
    
</configuration>
```

#### 模板

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--全局参数配置-->
    <settings>
        <!--比如下划线自动转驼峰式-->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    <!-- 配置 mybatis 的环境 -->
    <environments default="mysql">
        <!-- 配置 mysql 的环境 -->
        <environment id="mysql">
            <!-- 配置事务的类型 -->
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!-- 配置连接数据库的信息 -->
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mydb"/>
                <property name="username" value="root"/>
                <property name="password" value="12345678"/>
            </dataSource>
        </environment>
    </environments>

    <!-- 实体映射文件配置 -->
    <mappers>
        <mapper resource="mappers/IUserDao.xml"/>
    </mappers>
</configuration>
```







### 映射接口的xml【配置要实现的方法sql】

在【src/main/resources/mappers】路径下创建

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--必须填写namespace属性，是接口路径-->
<mapper namespace="com.xh.dao.IStudentDao">
</mapper>
```



### MyBatis动态SQL

类似JSP的JSTL表达式，这里的EL表达式不需要使用${}

#### sql语句提取简化

```xml
<sql id="defaultSql">select * from user</sql>
<include refid="defaultSql"></include> where 1=1
```



#### 支持的表达式语句

​		and 相当于 &&
​		or 相当于 ||
​		not 相当于 !
​		&lt;/&gt;= 相当于 < <=
​		&gt;/&gt;= 相当于 > >=
​		其他正常使用：!= = +-*/%
​		注意：!=不能在中间加空格，比如(! =)是错误的



####  if条件语句

​		第一个and会自动省略

```xml
<if test="username!=null and username != '' ">and username like #{username}</if>
<if test="address != null">and address like #{address}</if>
```



#### foreach标签

​		collection - 可以使用array/list/属性名/别名
​		item - 集合中每一个元素进行迭代时的别名
​		index - 下标
​		open - 开始语句
​		separator - 每次迭代之间分隔符
​		close - 结束语句

```xml
<foreach collection="参数" open="开头" close="结尾" item="变量" separator="分隔符">
    #{变量}
</foreach>
<foreach collection="array" open=" where info in(" close=")" item="i" separator=",">
    #{i}
</foreach>
```



####  choose语句【switch】

```xml
<choose>
    <when test="name != null">and name=#{name}</when>
    <when test="...">...</when>
    <otherwise>and 1=2</otherwise>
</choose>
```



#### where语句：代替sql语句中的where

```xml
select * from user 
<where>
    <if test="name != null">
        and username like #{name}
    </if>
    <if test="pass != null">
        and password = #{pass}
    </if>
    <if test="money >= 0">
        and money = #{money}
    </if>
</where>
```







### 查询

【id:表示接口里面的方法名称】
【resultType:表示返回值实体类路径；注意：List<User>和User都是一样】
【#{name}:要传入的参数,默认为Param1/Param2，当接口使用注解@Param("name")之后，#{name}】

```xml
<select id="findByNameAndMoney" resultType="com.xh.pojo.Student">
    SELECT * FROM user1 WHERE username LIKE #{name} and money like  #{money};
</select>
```

#### 模糊查询：

参数传递方式concat('%', #{param1}, '%')

```xml
<select id="searchByName" resultType="com.zking.mybatis.User">
    select * from user where username like concat('%', #{param1}, '%')
</select>
```

#### 分页查询【需要配置】：

https://pagehelper.github.io/docs/howtouse/

1. pom.xml添加依赖

	```xml
	<dependency>
    	<groupId>com.github.pagehelper</groupId>
    	<artifactId>pagehelper</artifactId>
    	<version>5.2.1</version>
	</dependency>
	```
	
2. mybatis.xml配置插件

   ```xml
   <!--分页插件-->
   <plugins>
       <plugin interceptor="com.github.pagehelper.PageInterceptor">
           <property name="helperDialect" value="mysql"/>
       </plugin>
   </plugins>
   ```

3. 使用依赖

   ```java
   IUserDao userDao = session.getMapper(IUserDao.class);
   PageHelper.startPage(2,5);//使用分页，查询第二页，每页5条
   List<User> users = userDao.findAll();
   PageInfo<User> info = new PageInfo<>(users);
   System.out.println("总条数：" + info.getTotal());
   System.out.println("总页数：" + info.getPages());
   System.out.println("每页条数：" + info.getPageSize());
   System.out.println("第几页：" + info.getPageNum());
   System.out.println("当前页的条数：" + info.getSize());
   //获取当前页的数据
   List<User> list = info.getList();
list.forEach(System.out::println);
   ```
   
   

#### 批量起别名：

【resultMap】替换掉【resultType】

【id】：给resultMap起个名字

【column】：数据库的字段名；对应【property】：别名与实体类相同

【javaType/jdbcType】：实体类和数据库的字段类型（一般不写）

```xml
<resultMap id="orderMap" type="com.xh.pojo.Order">
    <id column="id" property="id" javaType="int" jdbcType="INTEGER"/>
    <result property="user_id" column="user_id"/>
    <result property="info" column="info"/>
    <result property="address" column="address"/>
    <result property="price" column="price"/>
</resultMap>

<!--根据订单id查询订单-->
<select id="getById" resultMap="orderMap">
    SELECT *
    FROM order2
    WHERE id = #{id};
</select>
```



#### 联表查询

​	association：对应实体类的

```
private User user;
```

##### 一对一

```xml
<resultMap id="orderMap" type="com.xh.pojo.OrderAndUser">
    <id property="id" column="id"/>
    <result property="userId" column="user_id"/>
    <result property="info" column="info"/>
    <result property="address" column="address"/>
    <result property="price" column="price"/>
    <association property="user" column="user_id" javaType="com.xh.pojo.User">
        <id column="uid" property="id"/>
        <result property="username" column="username"/>
        <result property="password" column="password"/>
        <result property="money" column="money"/>
    </association>
</resultMap>

<select id="selectById" resultMap="orderMap">
        SELECT o.*, u.username, u.password, u.money,u.id uid
        FROM order2 o
                 left join user1 u on o.user_id = u.id
        where o.id = #{id};
    </select>
```

##### 一对多（1）懒加载

【fetchType】：懒加载，当不需要用到某个查询语句，获取的数据时就不会查询

```xml
<resultMap id="userOrdersMap" type="com.xh.pojo.UserAndOrders">
    <id column="id" property="id"/>
    <result column="username" property="username"/>
    <result column="password" property="password"/>
    <result column="money" property="money"/>
    <collection property="order2s" column="id" fetchType="lazy"
                select="com.xh.model.dao.IOrder2Dao.selectOrders"/>
    <!--column：另一个查询条件的变量-->
    <!--select：另外一个sql语句的路径-->
</resultMap>

<select id="selectById" resultMap="userOrdersMap">
    SELECT *
    FROM user1
    where id = #{id}
</select>


<resultMap id="orderMap2" type="com.xh.pojo.Order2">
    <id property="id" column="id"/>
    <result property="userId" column="user_id"/>
    <result property="info" column="info"/>
    <result property="address" column="address"/>
    <result property="price" column="price"/>
</resultMap>

<select id="selectOrders" resultMap="orderMap2">
    SELECT *
    FROM order2
    where user_id = #{userId}
</select>
```

##### 一对多（2）

```xml
<resultMap id="userOrdersMap2" type="com.xh.pojo.UserAndOrders">
    <id column="id" property="id"/>
    <result column="username" property="username"/>
    <result column="password" property="password"/>
    <result column="money" property="money"/>
    <collection property="order2s" ofType="com.xh.pojo.Order2">
        <id property="id" column="oid"/>
        <result property="userId" column="uid"/>
        <result property="info" column="info"/>
        <result property="address" column="address"/>
        <result property="price" column="price"/>
    </collection>
</resultMap>

<select id="selectById2" resultMap="userOrdersMap2">
    SELECT u.*,o.id oid,o.user_id uid,o.info,o.address,o.price
    FROM user1 u
    left join order2 o on u.id = o.user_id
    where u.id = #{id}
</select>
```

##### 一对多（3）返回【Map】

Map<String, List<Map<String, Object>>> 

```xml
<!--这里指定返回值为map-->
<resultMap id="userOrdersMap3" type="map">
    <id column="id" property="id"/>
    <result column="username" property="username"/>
    <result column="password" property="password"/>
    <result column="money" property="money"/>
    <!--javaType：实体类的属性-->
    <!--ofType：集合中存储元素的属性-->
    <collection property="order2s"  javaType="list" ofType="map">
        <id property="id" column="oid"/>
        <result property="userId" column="uid"/>
        <result property="info" column="info"/>
        <result property="address" column="address"/>
        <result property="price" column="price"/>
    </collection>
</resultMap>

<select id="getUserOrders" resultMap="userOrdersMap3">
    SELECT u.*,o.id oid,o.user_id uid,o.info,o.address,o.price
    FROM user1 u
    left join order2 o on u.id = o.user_id
    where u.id = #{id}
</select>
```









### 增加

方式1（所有数据库通用）：

【parameterType：传入的参数类型】

【resultType：返回值类型 (自增类型)，一般mybatis可以自动识别，允许任何简单类型用作主键的类型,包括字符串。 】

【keyProperty：指定结果被设置为实体类的某个属性】

【keyColumn：指定列名】

【order：只有两个属性，AFTER代表在增加数据库语句后执行】

```xml
<!--添加,需要返回自增的id主键-->
<insert id="insert" parameterType="com.xh.pojo.User">
    <selectKey resultType="int" keyProperty="id" keyColumn="id" order="AFTER">
        select last_insert_id()
    </selectKey>
    insert into student1(name ,age) values (#{name}, #{age})
</insert>
```

方式2（mysql独有）：

【useGeneratedKeys：只有两个属性，true表示开启】

```xml
<!--添加,需要返回自增的id主键-->
<insert id="insert1" parameterType="com.xh.pojo.User"
        useGeneratedKeys="true" keyProperty="id" keyColumn="id">
    insert into student1(name ,age) values (#{name}, #{age})
</insert>
```



【parameterType：传入的参数类型】

### 修改

```xml
<update id="update" parameterType="com.xh.pojo.User">
    update student1
    set age=#{age}
    where name = #{name};
</update>
```



【parameterType：传入的参数类型】

### 删除

```xml
<delete id="delete" parameterType="com.xh.pojo.User">
    delete
    from student1
    where id = #{id};
</delete>
```









### java使用【增删改必须提交事务-session.commit(); // 提交事务】

```java
//获取工厂
SqlSessionFactoryBuilder builder = new SqlSessionFactoryBuilder();
SqlSessionFactory factory = builder.build(Resources.getResourceAsStream("mybatis.xml"));
//开启工厂
SqlSession session = factory.openSession();
//工厂开始生产某个产品
IUserDao userDao = session.getMapper(IUserDao.class);
List<User> findByNameAndMoney = userDao.findByNameAndMoney("us%", "3%");
findByNameAndMoney.forEach(System.out::println);
//关闭流
session.close();
```

#### 测试模板

```java

    private static SqlSessionFactory factory = null; // 只有一个全局的工厂
    private SqlSession session = null; // 每一个测试之前都会打开一个会话

    // 在所有测试之前运行，只会运行一次！使用静态方法！
    @BeforeClass
    public static void beforeAll() throws IOException {
        MybatisSqlSessionFactoryBuilder builder = new MybatisSqlSessionFactoryBuilder();
        factory = builder.build(Resources.getResourceAsStream("mybatis.xml"));
    }

    // 在每一个@Test测试之前运行，使用普通方法！
    @Before
    public void before() {
        session = factory.openSession();
    }

    //每个测试之后运行
    @After
    public void after() {
        session.commit();
        session.close();
    }

```



#### 使用注解配置实体类【需要配置】

```java
//表示这是一个实体类
@Data
//自动生成全参构造函数
@AllArgsConstructor
//自动生成无参构造函数
@NoArgsConstructor
```

配置

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.22</version>
</dependency>
```



#### 使用注解实现【增删查改】

mybaits.xml配置

```xml
<!--扫描指定包下的接口-->
<mapper class="com.xh.model.dao.IUserDaoAnno"/>
<!--        <package name="com.xh.model.dao.IUserDaoAnno"/>-->
```



 ```java
//查询
@Select("SELECT * FROM user1 where id=#{id}")
//增加
@Insert("insert into user1(username ,password) values (#{username}, #{password}")
@SelectKey(statement = "select last_insert_id()",
           keyColumn = "id", keyProperty = "id",
           resultType = int.class, before = false)
//一对多查询
@Select("select * from user2 u where id=#{id}")
@Results({
    @Result(id = true, column = "uid", property = "id"),
    @Result(column = "username", property = "username"),
    @Result(column = "password", property = "password"),
    @Result(column = "money", property = "money"),
    // 一对多查询的属性名
    @Result(property = "orders", column = "id",
            many = @Many(select = "findOrders")),}
        )
@Select("select * from order2 where user_id=#{id}")
@Results(value = {
    @Result(id = true, column = "id", property = "id"),
    @Result(column = "user_id", property = "userId"),
    @Result(column = "info", property = "info"),
    @Result(column = "address", property = "address"),
    @Result(column = "price", property = "price")}
        )
 ```





### mybatis-plus

 完整配置见【spring】项目的子项目【oracle2_mybatis】

路径：D:\user\train\java\spring\oracle2_mybatis

#### 依赖

```xml
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
    <version>5.3.18</version>
    <scope>test</scope>
</dependency>

<!--lombok-->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.24</version>
    <scope>provided</scope>
</dependency>

<!--mybatis依赖-->
<!--mybatis-plus-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus</artifactId>
    <version>3.5.1</version>
</dependency>
```

#### mybatis.xml配置

接口与对应的xml都叫【Mapper】

```xml
<!-- 实体映射文件配置 -->
    <mappers>
        <mapper resource="mappers/IUserMapper.xml"/>
    </mappers>
```

#### 接口继承

```jaxa
@Mapper
public interface IUserMapper extends BaseMapper<User> {
}
```

#### 实体类

```java
@Data
//自动生成全参构造函数
@AllArgsConstructor
//自动生成无参构造函数
@NoArgsConstructor
//标记是这个表
@TableName("user1")
public class User {
    //标记对应表的主键
    @TableId("id")
    private Integer id;
    //标记对应表的字段名
    @TableField("username")
    private String username;
    @TableField("password")
    private String password;
    @TableField("money")
    private Double money;
}
```

#### 使用方法

```java
//获取工厂
MybatisSqlSessionFactoryBuilder builder = new MybatisSqlSessionFactoryBuilder();
SqlSessionFactory factory = builder.build(Resources.getResourceAsStream("mybatis.xml"));
//开启工厂
SqlSession session = factory.openSession();
//工厂开始生产某个产品
IUserMapper userMapper = session.getMapper(IUserMapper.class);
User user = userMapper.selectById(2);
System.out.println(user);
userMapper.selectList(null).forEach(System.out::println);
//关闭流
session.close();
```

#### 常用方法

详情https://blog.csdn.net/qq_46201721/article/details/121008180

1. insert: 保存
2. updateById: 根据主键更新
3. deleteById: 根据主键删除
4. selectById: 根据主键查询
5. selectList: 查询所有,参数new EntityWrapper().like(“name”,“m”)
6. selectPage: 分页查询,参数new Page(1,10),new EntityWrapper(new User())
7. save: 保存
8. saveBath: 批量保存
9. updateById: 根据主键更新
10. updateBatchById: 根据主键批量更新
11. removeById : 根据主键删除
12. removeByIds: 根据多个id删除
13. getById : 根据主键查询
14. getOne: 根据条件返回一个结果
15. list: 查询所有,参数new QueryWrapper()
16. listByIds: 根据多个id查询

##### 查询

```java
IUserMapper userMapper = session.getMapper(IUserMapper.class);

//根据指定字段查询【selectByMap()】返回【多条数据】
Map<String, Object> map = new HashMap<>();
map.put("username","user");
map.put("password","123");
userMapper.selectByMap(map).forEach(System.out::println);

//根据指定字段查询【selectList()】返回【多条数据】
order2Mapper.selectList(
                new QueryWrapper<Order2>().eq("user_id", 2))
                .forEach(System.out::println);

//根据根据指定字段查询【selectOne()】返回【1条数据】
QueryWrapper<User> queryWrapper = new QueryWrapper<>();
queryWrapper.eq("username", "user")
    .eq("password", "12344");
User user = userMapper.selectOne(queryWrapper);
System.out.println(user);
```

##### 插入【提交事务：session.commit();】

```java
//插入指定用户【insert()】
//只有表的【非空字段】才会插入，会返回id
IUserMapper userMapper = session.getMapper(IUserMapper.class);
User user = new User(null, "123", "123", null);
userMapper.insert(user);
session.commit();
```

##### 删除【提交事务：session.commit();】

```java
IOrder2Mapper order2Mapper = session.getMapper(IOrder2Mapper.class);

//根据指定字段删除多条deleteByMap()
Map<String, Object> map = new HashMap<>();
map.put("price",12);
order2Mapper.deleteByMap(map);
session.commit();

//根据id删除订单deleteById()
order2Mapper.deleteById(69);
session.commit();

//根据id删除多条订单deleteBatchIds()
List<Integer> list = new ArrayList<>();
list.add(70);
list.add(71);
list.add(72);
order2Mapper.deleteBatchIds(list);
session.commit();
```





### 缓存

#### 一级缓存

默认开启：

```xml
<select id="xxxById" resultType="xxx" parameterType="int" flushCache=true>
```

手动清理一级缓存：sqlSession.clearCache();
一级缓存用于同一个SqlSession之间，如果关闭会话则销毁缓存

#### 二级缓存

mybatis.xml里面开启缓存

```xml
<!--全局参数配置-->
    <settings>
        <!--开启二级缓存-->
        <setting name="cacheEnabled" value="true"/>
    </settings>
```

Mapper映射配置

实体类必须实现Serializable接口支持序列化：xxx implements Serializable

```xml
<!--开启IUserDao的二级缓存：-->
    <cache/>
<!--查询默认开启缓存：-->
<select ... useCache="true">
#缓存配置：
<cache eviction="FIFO" flushInterval="60000" size="512" readOnly="false"/>
#eviction：表示情况缓存的方式，FIFO-先进先出，LRU-最近少用的原则
#flushInterval：缓存时间
#size：缓存对象个数
#readOnly：只读，保存的对象无法直接获取引用，而是对象的拷贝
```

注解方式：
		开启缓存：@CacheNamespace(blocking=true)
	

