# pom.xml

#### 必备配置

```xml
<!--实际使用的依赖-->
<dependencies>

    <!--web项目必须的依赖：tomcat服务器，SpringMVC一套-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!--开发工具：运行时使用，支持热跟新-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>
    <!--注解、配置解析器-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <scope>runtime</scope>
        <optional>true</optional>
    </dependency>

    <!--SpringBoot测试工具：依赖junit-->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <scope>test</scope>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>


<dependencyManagement>
    <dependencies>
        <dependency>
            <!--SpringBoot的依赖-->
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-dependencies</artifactId>
            <version>2.7.0</version>
            <!--pom表示依赖的信息-->
            <type>pom</type>
            <!--import导入-->
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>

```





#### mybatis-puls依赖(包含mybatis)

```xml
<!--mybatis依赖-->
<!--<dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.4</version>
        </dependency>-->
<!--mybatis-plus依赖,【包含mybatis】-->
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.2</version>
</dependency>
<!--分页插件依赖-->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.2</version>
</dependency>
<!--mysql依赖-->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```











# 配置文件（yml/application）

#### application语法：

server.port=8081

#### yml语法：

```yml
server:
  #端口
  port: 8081
#  servlet:
    #上下文路径
#    context-path: /qz

# 配置JDBC
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/mydb
    username: root
    password: 12345678

  # 文件上传multipart配置（可以不用配置，都有默认配置）
  servlet:
    multipart:
      location: ./
      enabled: true
      max-file-size: 5MB
      max-request-size: 10MB

# 配置静态路径（默认为public/static路径）
#  web:
#    resources:
#      static-locations: classpath:/public/, classpath:/static/

# mybatis配置
#mybatis:
#  mapper-locations: classpath:mappers/*.xml
#  #配置别名（mapper.xml文件返回值类型就不需要写包，直接写user）
#  type-aliases-package: com.zking.entity
#  configuration:
#    map-underscore-to-camel-case: true # 下横线转驼峰

# mybatis-plus的配置（和mybatis配置一致）
mybatis-plus:
  mapper-locations: classpath:mappers/*.xml
  type-aliases-package: com.zking.entity
  configuration:
    map-underscore-to-camel-case: true # 下横线转驼峰

# pagehelper配置
pagehelper:
  #  dialect:  # 数据库方言类型（得是类）
  helper-dialect: mysql # 方言
  reasonable: true # 负数默认转为第1页

```











# 注解



#### @SpringBootApplication

注解主类，不能放到一个子包中，否则无法扫描其他Bean对象，也不能无任何包



#### @ResponseBody

一般用来处理ajax请求
注解方法，返回值就是响应体；
注解类表示类，里面所有的方法返回值就是响应体。



#### @RestController

@RestController = @ResponseBody + @Controller



#### @RequiredArgsConstructor

 生成带有必需参数的构造函数。

 必需的参数是最终字段和具有约束的字段，例如@NonNull ，final修饰。 



#### @RequestMapping

注解方法，这个方法的请求路径
注解类，里面所有方法的一级路径



#### @GetMapping
#### @PostMapping
#### @PutMapping
#### @DeleteMapping

相当于@RequestMapping(path = "xxx", method = RequestMethod.POST)



#### @RequestParam

注解参数，必须传递次参数



#### @RequestBody

注解参数，请求体的参数



####  @RequestHeader 

请求头参数



#### @PathVariable("id")

注解参数，与@PutMapping("user/{id}")一起使用，
请求方式：http://localhost:8081/user/1，传递过来的参数为1



#### @Data

自动生成get/set/toString



#### @AllArgsConstructor

自动生成全参构造函数



#### @NoArgsConstructor

自动生成无参构造函数



#### @TableName("user2")

注解实体类，绑定某个表



#### @TableId(value = "",type = IdType.AUTO)

注解实体类【主键】属性，【value】对应的字段名，如果一样可以不写，【type】自增



#### @TableField(value = "username")

注解实体类属性，【value】对应的字段名，如果一样可以不写













# 主方法

```java
@SpringBootApplication
public class App3 {
    public static void main(String[] args) {
        SpringApplication.run(App3.class, args);
    }
}
```













# 测试

```java
@SpringBootTest(classes = App3.class)
@RunWith(SpringRunner.class)
public class MybatisPlusTest
{
    @Autowired
    private IUserMapper userMapper;
    @Autowired
    private IUserService userService;
    
    @Test
    public void test3()
    {
        userMapper.selectList(null).forEach(System.out::println);
    }
    
    @Test
    public void test4()
    {
        userMapper.findAll().forEach(System.out::println);
    }
    
    @Test
    public void test5()
    {
        userService.transaction(1, 2, 100);
    }
}
```











# Controller控制层

```java
// @RestController = @ResponseBody + @Controller
@RestController
//自动生成无参带参构造
@RequiredArgsConstructor
public class UserController {
    private final IUserMapper mapper;
    private final IUserService userService;


    @RequestMapping("users")
    public List<User> users() {
        return mapper.findAll();
    }

    /*
    SpringBoot文件上传：
    1. 配置：spring.servlet.multipart.xxx=yyy
    2. Servlet中Part类型可以在SpringBoot中使用MultipartFile
    3. @PostMapping = @RequestMapping(path = "xxx", method = RequestMethod.POST)
        @GetMapping = @RequestMapping(path = "xxx", method = RequestMethod.GET)
     */
    // @RequestMapping(value = "upload", method = RequestMethod.POST)
    @PostMapping("upload")
    public Map<String, Object> upload(String username,
                                      @RequestParam MultipartFile file) throws IOException {
        // 可以直接获取文件名，并且一行代码将文件保存到指定位置
        File image = new File("D:\\user\\train\\java\\springboot\\springboot3\\src\\main\\resources\\static",
                file.getOriginalFilename());
        file.transferTo(image);
        // 返回
        Map<String, Object> map = new HashMap<>();
        map.put("name", username);
        map.put("file", file.getOriginalFilename());
        return map;
    }
}

```







#### 重定向获取属性

【RedirectAttributes】：重定向属性，重定向之后可以获取一次的属性

```java
   @RequestMapping(path = "vip/buyVip/{month}/{price}")
    public String buyGame(RedirectAttributes redirect) throws ParseException {

            redirect.addFlashAttribute("vipInfo", Result.fail("你还未登录"));
            return "redirect:/";
        
    }
```







#### 读取yml文件属性

定义属性【@Value("${这里是属性名}")】注解修饰

```java
@Value("${upload.location}")
private String location;
```



#### 文件上传

```java
//上传图片
@PostMapping("updateFile")
public String updateFile(MultipartFile file, Model model) throws IOException {
    //随机文件名
    String path = "/" + UUID.randomUUID() + file.getOriginalFilename();
    //location为配置文件配置的路径
    File dest = new File(location, path);
    //文件上传到准备好的文件
    file.transferTo(dest);
    model.addAttribute("path", path);
    return "index";
}
```









#### Validate参数验证

##### 添加依赖

```xml
<!--validation参数验证-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```

##### 注解类

【@Validated】注解类用于开启验证功能



##### 注解对象参数

【@Validated】注解参数开启验证（只支持@RequestBody/@RequestPart参数），类字段，根据需求使用下面的注解；当类字段为对象时，使用【@Valid】注解字段，类字段，根据需求使用下面的注解

###### 对象参数获取错误信息：

使用BindingResult/Errors参数类型获取错误信息
如果有多个Bean验证，需要多个BindingResult参数一一对应
顺序为前后顺序：@Validated User user, BindingResult result



【@Valid】无法替换@Validated直接使用
		Bean对象属性中如果使用了嵌套Bean对象，需要使用@Valid注解
		嵌套必须加@Valid，否则嵌套中的验证不生效



###### 普通类型参数获取错误信息

注解普通类型参数，需要配合异常处理器：
		@ExceptionHandler(ConstraintViolationException.class)
		ConstraintViolationException.getConstraintViolations()获取错误信息

普通参数：直接使用，类上面注解开启验证【@Validated】



【@Null 】被注解的元素必须为null
【@NotNull 】被注解的元素必须不能为null
【@Min(value)/@DecimalMin(value)】 被注解的元素是一个数字，值必须大于等于指定的最小值
【@Max(value)/@DecimalMax(value) 】指定元素值的最大值
【@Size(min, max)】	被注解的数组、集合、字符串的大小必须在指定的范围内
【@Past/@Future】 被注解的Calendar对象必须是一个过去或者将来的日期
【@Pattern(regex='') 】被注解的元素必须符合指定的正则表达式
	另外Hibernate Validator新增了一些注解：
【@Email】 被注解的元素必须是电子邮箱地址
【@Length(min, max)】 被注解的字符串的长度必须在指定的范围内
【@NotEmpty】 被注解的集合、数组必须不是null并且长度大于0
【@NotBlank 】被注解的字符串不为null且不能全部为空字符串
【@Range(min, max)】 被注解的元素必须在合适的范围内
	额外：
【@Valid】 被注解的元素需要递归验证，用在对象属性中
【@Validated】 用于注解类，Spring封装，开启验证，也可以注解参数





##### 自定义验证注解

###### 定义注解

```java
package com.zking.validate;

import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.*;

@Target({ElementType.PARAMETER})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Constraint(validatedBy = {MyConstraint.class, MyConstraintUser.class})
public @interface MyValidate
{
    // 可以自定义参数
//    String keyWord() default "SpringBoot";
    
    // 这些都是必须的属性
    // 表示验证失败后的错误信息
    String message() default "非法用户名！";
    
    // 相当于message
    String value() default "非法用户名！";
    
    // 分组
    Class<?>[] groups() default {};
    
    // 验证相关的信息
    Class<? extends Payload>[] payload() default {};
}
```



###### 定义类

注解的类型由【ConstraintValidator<MyValidate, String>】后面的参数决定，

比如注解类【ConstraintValidator<MyValidate, User>】，

如果需要注解多个类型的参数，就多定义几个类

```java
package com.zking.validate;

import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

public class MyConstraint implements ConstraintValidator<MyValidate, String>
{
    private String key;
    
    @Override
    public void initialize(MyValidate constraintAnnotation)
    {
        // 这里可以进行一些初始化，比如加载文件、数据库……
//        System.out.println("初始化中，必须添加关键字：" + constraintAnnotation.keyWord());
//        key = constraintAnnotation.keyWord();
    }
    
    @Override
    public boolean isValid(String value, ConstraintValidatorContext context)
    {
        // 这里判断是否符合验证的要求，返回false说明验证失败
        if (value == null)
        {
            return false;
        }
        return true;
    }
}

```



















# 实体类entity

```java
//自动生成get/set/toString
@Data
//自动生成全参构造函数
@AllArgsConstructor
//自动生成无参构造函数
@NoArgsConstructor
@TableName("user2")
public class User{
    @TableId(type = IdType.AUTO)
    private Integer id;
    private String username;
    private String password;
    private Double money;
}
```









# mapper

```java
@Mapper
public interface IUserMapper extends BaseMapper<User>{
}
```











# Service

#### 接口

```java
public interface IUserService extends IService<User>{
}
```

#### 实现

```java
@Service
public class UserService extends ServiceImpl<IUserMapper, User> implements IUserService
{
    //开启注解，也可以注解类
    @Transactional
    @Override
    public void transaction(int id1, int id2, int money)
    {
        // 获取
        User user1 = getById(id1);
        User user2 = getById(id2);
        if (user1 == null || user2 == null)
        {
            System.out.println("无此账户！");
            return;
        }
        
        // 修改
        user1.setMoney(user1.getMoney() - money);
        user2.setMoney(user2.getMoney() + money);
        
        // 更新
        updateById(user1);
//        System.out.println(1 / 0); // 异常
        updateById(user2);
        System.out.println("转账成功！");
    }

 return null;
    }
}
```











#### 分页插件使用

```java
PageHelper.startPage(2,5);//使用分页，查询第【pageNum】页，每页【pageSize】条
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







#### 开启事务

@Transactional

注解方法，表示这个方法报错会回滚





 @EnableTransactionManagement 

springboot似乎不用，注解启动类， 该类的所有 public 方法将都具有该类型的事务属性，同时，也可以在方法级别使用该标注来覆盖类级别的定义。 









# Tymeleaf集成



前后端分离：
	分离：效率高、可以使用前端框架、测试方便
	不分离：一套系统启动速度快、利于SEO、简单
	JSP：后端渲染，效率贼低（包含大量String的Java类）、依赖多、配置繁琐、不利于测试
	SpringBoot支持的模板引擎：Fre



> 特点

Thymeleaf是一个用于Web和独立Java环境的模板引擎，用于处理HTML/XML/JS/CSS
动静结合：既可以直接使用浏览器打开查看静态效果，也可以通过Web应用程序进行访问
开箱即用：Thymeleaf与SpringMVC完美集成，可以快速的实现表单绑定、属性编辑等
完美支持SpringBoot：适合前后端分离设计，SpringBoot中默认配置和支持Thymeleaf视图解析器
多方言支持：可以直接套用模板实现JSTL/OGNL表达式



> 依赖

org.springframework.boot:spring-boot-starter-thymeleaf



> 配置

```properties
		# Thymeleaf 模板配置
		spring.thymeleaf.prefix=classpath:/templates/
		spring.thymeleaf.suffix=.html
		spring.thymeleaf.mode=HTML5
		spring.thymeleaf.encoding=UTF-8
		spring.thymeleaf.content-type=text/html
		# 禁用缓存，开发中使用
		spring.thymeleaf.cache=false
		# 是否开启内容版本策略
		spring.resources.chain.strategy.content.enabled=true
		# 指定要应用的版本的路径
		spring.resources.chain.strategy.content.paths=/**
```



#### 常用属性



> 引入命名空间

```html
	<html xmlns="http://www.w3.org/1999/xhtml" xmlns:th="http://www.thymeleaf.org">
```



th:text	标签的文本替换
th:utext 标签的文本替换，支持HTML标签文本
th:value 替换input标签的value属性
th:src 替换src属性，使用@{}表示路径
th:action 替换表单提交地址，使用@{}表示路径
th:style 设置样式
th:onclick 点击事件
th:selected	下拉列表selected属性



> 判断、循环结构



th:if 根据条件判断是否需要展示此标签（JSTL=>c:if | Vue=>v-if | Thymeleaf=>th:if）
			`<a th:if="${userId} == ${user.userId}">条件满足则显示</a>`
			`<a th:unless="${userId} != ${user.userId}">条件不满足则显示</a>`
			示例：`<span th:if="${(age >= 18)">满足条件显示</span>`



th:switch 与 th:case 配合使用，根据不同的条件展示不同的内容	（JSTL=>c:choose+when+otherwise | Thymeleaf=>th:switch+case）
`<div th:switch="${name}">
	<span th:case="a">显示a</span>
	<span th:case="b">显示b</span>
	<span th:case="*">默认值</span>
</div>`





th:each	遍历，支持List/Map/数组（JSTL=>c:forEach | Vue=>v-for | Thymeleaf=>th:each）
	`<tr th:each="m:${session.map}">
		<td th:text="${m.getKey()}"></td>
		<td th:text="${m.getValue()}"></td>
	</tr>
	<tr th:each="m,state:${session.map}">
		<td th:text="${state.first} ? '第一个' : 'Nop'"></td>
		<td th:text="${state.index}">显示下标</td>
	</tr>`



数组、List/Map：
	数组、List：th:each="element, status:${array|list|set}"
		element为元素，status有属性：index下标从0开始，count从1开始
		even为双数，odd为双数，size总大小
	Map：th:each="value, status:${map}"
		value为Map的值，status属性：current表示当前键值对
		status.current.key当前键，status.current.value当前值
		status.first第1个，status.last最后1个
	直接使用属性：th:text="${map.key}"



其他：
	th:object 在父标签选择对象，子标签使用 *{…} 选择表达式选取值	
			`<div th:object="${session.user}" >`
				`<p th:text="*{username}">用户名</p>`
				`<p th:text="${session.user.username}">等同于上一句</p>`
			`</div>`



#### 语法

注释：
	`<!--/* 这是Thymeleaf注释 */-->
	<!-- 这是HTML注释 -->
	<%-- 这是JSP注释 --%>`

基本表达式：
	变量表达式: ${...}，类似EL表达式
	URL 表达式: @{...}，用于路径，比如图片、超链接
	选择变量表达式: *{...}，结合th:object使用
	消息表达式: #{...}，读取properties配置文件中的属性
	代码段表达式: ~{...}，类似iframe内联框架，引用其他代码
常用语法表达式：
	拼接：th:text="'string' + ${var}"、th:text="|字符串值:xxx:${var}|"
	算术运算：th:text="1 + ${var} * 3 / ${some}"
	比较运算：th:text="${var1} > ${var2}"，和EL表达式区别：${var1 > var2}
		可以使用gt/ge/eq/lt/le/ne
	逻辑运算：th:text="! ${var} || (${a} != ${b})"，EL表达式：${! var || a != b}

☆参数拼接：
	读取properties配置文件属性并拼接：th:text="prop.value(${var})"
	路径中拼接URL变量参数（@RequestParam）：th:src="@{/user/profile(id=${user.id})}"
	路径中拼接路径变量参数（@PathVariable）：th:src="@{/users/{id}/info(id=${user.id})}"
	路径中同时拼接路径参数和查询参数：th:src="@{/users/{id}(id=${user.id},name=${user.username})}"
	变量和参数：th:src="@{/${username}/orders/{id}(id=${user.id},name=${user.username})}"



Elvis运算符：
		`<p th:text="${username} ?: '无名氏'"></p>`
		使用并判断变量是否为空，为空则显示为右边的值
		`<p th:text="null ?: _">这里会输出</p>`
		使用_表示不做任何动作



三元运算符：
		`<p th:text="${username} ? ${username} : '无名氏'"></p>`
		`<p th:text="${username == null ? username : '无名氏'}"></p>`



#### 内置对象



使用方法：${#request.contextPath}

内部对象（Web）：
	#ctx 上下文对象
	#vars 上下文变量
	#request 表示HttpServletRequest对象
	#response 表示HttpServletResponse对象
	#session 表示HttpSession对象
	#servletContext 表示ServletContext对象

常用对象：
	${param.x} 请求参数
	${session.x} 会话中的参数
	${application.x} 上下文中的对象

工具对象：
	#dates 对java.util.Date对象的使用方法封装，${#dates.format(xxx, 'yyyy-MM-dd')}
	#calendars 对java.util.Calendar对象的使用封装，${#calendars.createNow()}
	#numbers 对数字的使用方法封装，${#numbers.formatDecimal(xxx,3,2)}
	#strings 字符串有用方法，${#strings.equals(a, b)}/${#strings.length(x)}
	#arrays 对数组相关使用方法封装，${#arrays.length(xxx)}/${#arrays.contains(x, y)}

其他：#lists/#sets/#maps/#aggregates/#messages/#ids/#bools



> 对比EL表达式：
> 	1. EL表达式在任意地方可以使用，thymeleaf只能用在"th:xxx"表达式中
> 	2. EL表达式在大括号内部运行：${first+'-'+last}，thymeleaf在外部：th:text="${first}+'_'+${last}"
> 	3. EL表达式拼接链接直接使用，thymeleaf则使用专门的语法："@{/user/id(id=${id},param=${xxx})}"
> 	4. thymleaf支持Elvis表达式："${login} ?: '游客'"，可以直接判断空："${login} ? '登录' : '未登录'"
> 	5. thymleaf必须内联才能使用在特殊标签中，使用[[]]语法内联变量



#### 内联操作

文本内联：



```html
<div th:inline="text">
	<p>你好：[[${user.name}]]</p>
	<p>非转义，无效：[${user.name}]</p>
</div>Thymeleaf 3.0 支持直接文本内联：
<span>你好：[[${user.name}]]</span>
```

js内联：

```javascript
<script th:inline="javascript">
    var username = [[${user.name}]]; // 字符串会自动添加""，数字直接显示
</script>
```



CSS内联：

```css
	<style th:inline="css">
		.[[${classname}]] { text-align: [[${align}]]; }
	</style>
```



取消内联：
	使用th:inline="none"取消内联

	<div th:inline="none">[[1,2,3]] ['a','b','c']</div>



#### 片段引用



Thymeleaf 3.0 支持片段定义和引用
定义：
	在footer.html中定义：
	`<div th:fragment="frag">这是共用部分-底部</div>`
使用：
	直接包含标签（3.0版本后抛弃）：
	`<div th:include="~{footer::frag}"></div>`
    `<div th:include="footer::frag"></div>`

​	替换指定标签：
​	`<div th:replace="~{footer::frag}"></div>`
​	`<div th:replace="footer::frag"></div>`

​	插入到指定标签：
​	`<div th:insert="~{footer::frag}"></div>`
​	`<div th:insert="footer::frag"></div>`

​	其他：
​		~{footer} 全部片段
​		~{this::frag} 当前模板中的片段，this可以省略
区别：
​	th:include 插入片段解析后的内容
​	th:replace 替换，效果和include类似
​	th:insert 直接插入片段，包含被插入的节点
变量：
​	定义：th:fragment="frag(var1,var2)"
​	应用：`<span th:text="${var1} + ${var2}"></span>`
​	传值：`<div th:include="footer::frag('123', ${user})"></div>`



参考：https://www.jianshu.com/p/99a024f80cad













# redis缓存

#### 需要的注解

1. 【@Configuration】使用在RedisConfig，标注为配置类

2. 【@EnableCaching】开启rides缓存注解

3. 【@CacheConfig(cacheNames = "user")】只能用于类，用来统一配置当前类所有方法，表示user是这个类       中共用的缓存名字,方法的注解可以不写【cacheNames = "user"】

4. 【@Cacheable(key="指定key") 】可以用于类和方法，一般用于<font color='red'>查询</font>，表示结果将会被缓存，缓存里面的key为【cacheNames +value】

   ```java
   @Cacheable(key = "'name::' + #name", unless = "#name == null || #result == null ")
   ```

5. 【@CachePut】和@Cacheable的区别是，只会插入redis，不会查询，所以一般用于<font color='red'>插入</font>和<font color='red'>更新</font>

6. 【@CacheEvict(allEntries = true)】清空缓存，allEntries表示当前所有key，一般用于<font color='red'>更新</font>和<font color='red'>删除</font>，也可以加key属性，删除redis与之匹配的key，<font color='red'>开启事务后如果更新或者删除失败就不会清空redis缓存</font>

   ```java
   @CacheEvict(allEntries = true, condition = "#result")
   @CacheEvict(key = "'id::' + #id")
   ```

7. 【@Caching】 类似一个集合，将多个注解结合使用，比如要使用多个同名注解时

   ```java
   @Caching(put = {@CachePut(key = "'id::' + #user.id"),
                       @CachePut(key = "'name::' + #user.username")},
                evict = @CacheEvict(allEntries = true))
   ```

8. 属性：

   - 【cacheNames】唯一必须提供的属性，要么在方法的注解上配置，要么在类上用CacheConfig配置，会和key属性拼接为redis的key
   - 【key】表示缓存的键，结合缓存名字使用：cacheName::key，如果不提供默认使用所有参数拼接
   - 【allEntries 】表示对缓存名字下所有的key生效，只用于<font color='red'>删除</font>。
   - 【condition 】表示什么条件下缓存，使用spEL表达式："#id > 0",#id表示获取方法的id参数
   - 【unless 】和condition刚好相反，使用spEL表达式
   - 【keyGenerator 】可以用来自动生成key，需要实现KeyGenerator接口
   - 【#result】获取返回值



#### RedisConfig--配置redis

```java
//标注为配置类
@Configuration
//开启rides缓存注解
@EnableCaching
public class RedisConfig {

    @Bean
    public CacheManager redisCacheManager(RedisTemplate<String, Object> redisTemplate) {
        RedisCacheConfiguration config = RedisCacheConfiguration
            //默认配置对象
            .defaultCacheConfig()
            //key的序列化器
            .serializeKeysWith(RedisSerializationContext.SerializationPair
                               .fromSerializer(redisTemplate.getStringSerializer()))
            //value的序列化器
            .serializeValuesWith(RedisSerializationContext.SerializationPair
                                 .fromSerializer(redisTemplate.getValueSerializer()))
            //禁止缓存空值
            .disableCachingNullValues()
            //存活时间
            //                .entryTtl(Duration.ofHours(1));//一小时
            .entryTtl(Duration.ofSeconds(10));//10秒

        return RedisCacheManager.RedisCacheManagerBuilder
            .fromConnectionFactory(redisTemplate.getConnectionFactory())
            .cacheDefaults(config)// 配置
            .transactionAware()// 事务感知
            .build();

    }

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory factory) {
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        redisTemplate.setConnectionFactory(factory);
        //配置key-value的序列化器
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        //配置hash表的序列化器
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        //配置生效
        redisTemplate.afterPropertiesSet();
        return redisTemplate;
    }
}
```















# 拦截器

1. preHandle - 处理请求之前可以进行拦截；返回true方向，false不放行，一般用于身份校验、拦截等

2. postHandle - 请求处理之后，视图显示【之前】，可以：【设置视图、数据】，如果爆发异常，【不会】经过此方法，视图渲染之前回调，可以用来添加Model属性

3. afterCompletion - 请求处理之后，视图已经显示【可以进行资源相关的清理工作】，一般用来清理资源

   

4. ​	实现：
   ​		自定义类实现HandlerInterceptor接口
   ​		配置类实现WebMvcConfigurer接口，在addInterceptors方法中添加拦截器
   ​		设置拦截规则：InterceptorRegistry.addInterceptor(new MyInterceptor()).addPathPatterns("/**")

#### 实现代码

```java
//自定义拦截器
public class MyInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("拦截路径 : " + request.getRequestURI());
        return HandlerInterceptor.super.preHandle(request, response, handler);
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
    }
}
```



拦截器支持注解多个拦截器、支持排除规则
	.addPathPatterns("/**");
	.excludePathPatterns("/login", "/login.html"...);
	.order(1) 优先级



#### 拦截器注解多个拦截器、排除规则

```java
public class MyConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new MyInterceptor())
                .addPathPatterns("/**")// 拦截路径 url-pattern
                .excludePathPatterns("/t1")//排除路径 exclude-url-pattern
                .order(1);//拦截器的顺序（优先级）
    }
}
```













# 

# @ControllerAdvice 

用来注解类，类里面的方法有三个注解配合使用

Spring3.2新增注解：@ControllerAdvice
	功能：类似于所有@Controller的全局配置注解类
	属性：basePackages、annotations、assignableTypes用于指定作用范围
	注解类支持的方法注解包括：
		@ExceptionHandler 注解一个异常处理器
		@ModelAttribute 相当于全局@RequestMapping注解对象共享的数据绑定
			通过注解方法设置全局数据绑定
			通过注解参数获取全局数据值
		@InitBinder 全局数据预处理：用来设置WebDataBinder，自动绑定请求参数到Model中
			一般用来设置类型转换器
			方法可以携带参数：WebDataBinder、HttpServletRequest等
			WebDataBinder.registerCustomEditor(转后类型, 转化器)
			常用转化器：CustomDateEditor、StringTrimmerEditor等



#### 异常处理

#### @ExceptionHandler({Exception.class}) 

注解方法
方法依然可以使用Model作为参数、ModelAndView作为返回值、@ResponseBody标注

```java
/*
使用增强注解：
    @ControllerAdvice 专门用于增强控制器的注解
    @ExceptionHandler 注解方法，表示该方法用于处理指定异常
    方法可以传递指定类型的异常作为参数，返回值也可以使用JSON或者View
使用优势：
    1. 不用配置WebMvcConfigurer接口，不用实现HandlerExceptionResolver接口
    2. 单独定义在任意@ControllerAdvice注解的类上
    3. 可以单独处理不同类型的异常，不同异常可以不同方法
    4. 方法签名任意：返回值任意、参数任意，可以使用response，也可以使用view视图，也可以使用json
    5. 可以指定增强的控制器
 */
// @Component // 这里不用@Component这些注解！
@ControllerAdvice(/*basePackages = {"com.zking.*"}, // 指定包*/annotations = {Controller.class})  // 指定被增强注解类型
// @ResponseBody
public class MyAdviceController {

    //注解这些异常来这里处理
    @ExceptionHandler({RuntimeException.class,Exception.class})
    public ModelAndView handleExl(HttpServletResponse response) throws IOException {
        response.setContentType("text/html;charset=utf-8");
        response.getWriter().write("你爆了");
        return new ModelAndView();
    }

    @ExceptionHandler({MyException.class})
    @ResponseBody
    public Result<Void> handleEx2(MyException e){
        return new Result<>(false, "报错了" + e.getMessage(),null);
    }
}
```









#### 注解对象共享的数据绑定

#### @ModelAttribute

controller控制层每个方法运行前都会运行

```java
@ControllerAdvice(annotations = {Controller.class})  // 指定被增强注解类型
// @ResponseBody
public class MyControllerAdvice {

    @ModelAttribute
    public void global(Model model){
        model.addAttribute("c0","0000");
    }
}

```







#### 全局数据预处理

#### @InitBinder

controller控制层，方法获取参数时，前端传出“2001/06/29”能直接转为Date对象，

传入“2001-06-29”会报错，但是配置转换规则之后就可以正常转

```java
// @Component // 这里不用@Component这些注解！
@ControllerAdvice(annotations = {Controller.class})  // 指定被增强注解类型
// @ResponseBody
public class MyControllerAdvice {

   @InitBinder
    public void binder(WebDataBinder binder){
        //Date日期转换规则
        binder.registerCustomEditor(Date.class,
                new CustomDateEditor(new SimpleDateFormat("yyyy-MM-dd"),false));
        //list集合转换规则(自己实现的转换器)
        binder.registerCustomEditor(List.class,new MyPropertyEditorSupport());
    }
}
```





#### 自己实现转换器

方法参数需使用@RequestParam注解

```java
public class MyPropertyEditorSupport extends PropertyEditorSupport {
    @Override
    public void setAsText(String text) throws IllegalArgumentException {
        if (text == null || text.trim().isEmpty()) {
            setValue(null); // 空字符串，设置为空
        } else {
            setValue(Arrays.asList(text.split(" "))); // 转换为list集合
        }
    }
}
```











# 日志配置

**@Slf4j**

配置：
		root为根日志级别，支持自定义任意包的日志级别
		级别：debug(调试) < info(消息) < warn(警告) < error(错误) < fatal(严重错误）
		logging.level.root=级别
		logging.level.com.zking.xxx=自定义包的级别
		logging.file.name=日志文件（和路径配置二选一）
		logging.file.path=日志文件保存路径
		logging.config=classpath:/logback配置文件.xml
	无注解方式：
		Logger LOGGER = LoggerFactory.getLogger(XXX.class);
		LOGGER.debug("Debug级别日志");
		LOGGER.info("Info级别日志");
	占位符：
		log.info("{}{}", 1, 2);
		{}为占位符，类似print方法
	@Slf4j
		注解在类上，开启自动日志对象
		方法中直接使用：log.xxx(日志信息)
		注意：必须配合Lombok使用
	内置符号：
		%d{HH:mm:ss.SSS} 日志输出时间
		%thread 输出日志的进程名字
		%-5level 日志级别，指定5个字符靠左对齐
		%logger 日志输出者的名字
		%msg 日志消息
		%n 换行符



#### ym.xml配置

```xml
#日志配置
logging:
  file:
    path: .  #日志文件路径
    name: mylog.log  #日志文件名
  level:
    root: error
    web: error
    com.zking: debug
    org.mybatis: error
```







#### controller控制层

类使用【@Slf4j】注解相当于直接生成一个Log对象

方法里面执行写入日志文件log.info("ggg");

```java
@RestController
//自动生成无参带参构造
@RequiredArgsConstructor
//一级路径
@RequestMapping("/t1")
@ResponseBody
@Slf4j//相当于直接生成一个Log对象
public class Controller1 {

    @GetMapping({"a2"})
    public Date test2(Date date) {
        log.info("ggg");
        return date;
    }

}
```









# 状态码注解：

#### @ResponseStatus(HttpStatus.BAD_REQUEST)

注解方法，当前段请求时返回的状态码，

```java
// 默认情况下，返回的响应码是200，一般在restful中返回是201
@PostMapping("/a2")
@ResponseStatus(HttpStatus.CREATED)
public User a2(@RequestBody User user)
{
    log.debug("保存用户到数据库：{}", user);
    return user;
}
```

使用在异常处理方法上，捕获异常时，设置返回的状态码

```java
@ExceptionHandler({ParseException.class})
@ResponseBody
// 添加【自定义】响应码
// @ResponseStatus(HttpStatus.INTERNAL_SERVER_ERROR)
@ResponseStatus(HttpStatus.BAD_REQUEST)
public User exceptionHandle(ParseException e)
{
    return new User(2, e.getMessage(), e.getClass().toString(), 1.1);
}
```





# 配置404

```java
// 实现自定义异常页面
/*
@Component相比@Configuration：
    @Component必须要实例化一个实在的对象，比如这个对象实现了一个配置接口
    @Configuration一般作为配置，内部的字段、方法可以直接使用，比如@Bean方法
*/
@Component
public class My404 implements ErrorPageRegistrar
{
    @Override
    public void registerErrorPages(ErrorPageRegistry registry)
    {
        // 配置错误页面路径，当爆出404时跳转到index.html
        ErrorPage errorPage404 = new ErrorPage(HttpStatus.NOT_FOUND, "/index.html");
        ErrorPage errorPage500 = new ErrorPage(HttpStatus.INTERNAL_SERVER_ERROR, "/index.html");
        // 注册错误页面
        registry.addErrorPages(errorPage404, errorPage500);
    }
}
```











# spel表达式

SpEL即Spring表达式语言，比JSP的EL更强大的一种表达式语言，可以在运行时查询和操作数据，尤其是数组、列表等
	依赖：
		org.springframework:spring-expression
		

​	ExpressionParser parser = new SpelExpressionParser();
​	Expression expression = parser.parseExpression("表达式");
​	expression.getValue();
​	
​	EvaluationContext context = new StandardEvaluationContext(对象);
​	expression.getValue(context, 类型);
用法：
​	注解的使用：@Value("#{表达式}")
​	XML配置使用：<... value="#{表达式}">
​	注解的条件可以用：@Cacheable(condition="#result != null")
​	内置的SpEL表达式解析API直接操作SpEL表达式
​	Thymeleaf模板引擎部分可以用
字面量：
​	"#{3.1415}"
​	"#{'字符串'}"
​	"#{false}"
运算符：
​	计算：+ - * / % ^
​	"#{10^3}" === 1000
​	比较：< > >= <= == lt gt eq le ge
​	"#{10&lt;=0}" === "#{10<=0}" === #{10 le 0}
​	逻辑运算：and or not || ! &&（用&amp;&amp;表示）
​	三元运算符："#{(10 > 0) ? 'Yes' : 'No'}"
​	正则表达式："#{'a@b.com' matches '.+@.+\.com'}"
方法调用：
​	筛选过滤："#{list.?[this>3]}"
​	静态方法："#{T(java.lang.Math).max(1, 2)}"
​	普通方法："#{'test'.substring(1,3)}"
​	Elvis运算符："#{name ?: '空值'}"
​	安全操作符："#{list?.length}"
​	集合："#{list[0]}"、"#{{1,2,3}.length}"、"#map['name']"



```java
@SpringBootTest(classes = App8.class)
@RunWith(SpringRunner.class)
public class TestSpEL
{
    @Value("123") // 这是一个数字
    private int a1;
    // @Value("${123}") // 这是配置中的属性：123属性
    private int a2;
    @Value("#{123 + 100 / 2}")
    private int a3;
    // @Value("123 + 100 / 2") // 这里会将整体转为整数，而不会作为运算表达式计算
    private int a4;

    @Test
    public void test1()
    {
        System.out.println(a1);
        System.out.println(a3);
        System.out.println(a4);
    }

    @Test
    public void test2()
    {
        String el = "2^3 + 100 / 2"; // 基本运算
        el = "'15812346688' matches '1\\d{11}'"; // false，正则
        // el = "#{123 + 100 / 2}";
        el = "T(java.lang.Math).random()";
        el = "T(Math).random()";
        ExpressionParser parser = new SpelExpressionParser();
        Expression expression = parser.parseExpression(el);
        System.out.println(expression.getValue());
    }

    @Test
    public void test3()
    {
        List<User> users = new ArrayList<>();
        users.add(new User(1, "a", "123", 1.1));
        users.add(new User(2, "ab", "123", 1.1));
        users.add(new User(3, "aaa", "123", 1.1));
        users.add(new User(4, null, "123", 1.1));
        ExpressionParser parser = new SpelExpressionParser();
        EvaluationContext context = new StandardEvaluationContext(users);
        String el = "[0]";
        Expression expression = parser.parseExpression("[0]");
        System.out.println(expression.getValue(context, User.class));
        el = "size";
        System.out.println(parser.parseExpression(el).getValue(context));

        Map<String, Object> map = new HashMap<>();
        map.put("list", users);
        context = new StandardEvaluationContext(map);
        el = "['list'].?[id>2]";
        el = "['list'][3].username.length()"; // null异常
        el = "['list'][3].username?.length()?.toString()";
        System.out.println(parser.parseExpression(el).getValue(context));
    }
}
```













# shiro整合

#### 实体类需要继承Serializable接口



#### yml配置文件

可以不读取yml配置直接填值

```xml
# 自定义shiro属性
shiro:
  chain: #配置路径以及，路径需要的权限，和角色为Map
    '[/shiro]': authc,roles[admin]
    '[/admin/*]': authc,roles[admin]
    '[/ex]': anon
    '[/]': anon
    '[/login]': anon
    '[/logout]': logout
    '[/**]': anon
  login-url: / #登录的路径（当没登录时跳转的路径）
  unauthorized-url: /403 #权限不足的路径
  success-url: /success #登录成功的路径
  hash-algorithm-name: MD5 #加密的方式名称
  hash-iterations: 2 #加密的迭代次数
```



#### MD5加密

```java
ByteSource salt = ByteSource.Util.bytes("admin");//盐值
//参数：加密名称，加密数值，加密盐值，迭代次数
SimpleHash hash = new SimpleHash("MD5", "123", salt, 2);
System.out.println(hash);
```



#### 控制器登录验证的使用

```java
@PostMapping("/login")
public String login(String username, String password, RedirectAttributes redirectAttributes) {
    try {
        //获取主体，任意地方都能获取
        Subject subject = SecurityUtils.getSubject();
        //传入需要验证的用户，以及令牌（密码）
        UsernamePasswordToken token = new UsernamePasswordToken(username, password);
        token.setRememberMe(true);
        //验证登录，会进入到核心类Realm验证
        //验证成功继续运行，失败则抛出下面的异常
        subject.login(token);
        //获取Realm里面设置的主体
        User user = (User) subject.getPrincipal();
        //把主体设置到当前会话里面

        subject.getSession().setAttribute("user", user);
        //转发到登录成功
        return "redirect:/success";
        //验证失败就抛出不同的异常
    } catch (UnknownAccountException e) {
        redirectAttributes.addFlashAttribute("error", "无此用户");
    } catch (IncorrectCredentialsException e) {
        redirectAttributes.addFlashAttribute("error", "密码不正确");
    } catch (LockedAccountException e) {
        redirectAttributes.addFlashAttribute("error", "用户已经被锁定");
    } catch (ExcessiveAttemptsException e) {
        redirectAttributes.addFlashAttribute("error", "尝试次数过多");
    } catch (AuthenticationException e) {
        redirectAttributes.addFlashAttribute("error", "认证错误");
    }
    //重定向到登录失败页面
    return "redirect:/error1";
}
```





#### 方法一：普通配置



###### 所需依赖

```xml
<!--shiro相关依赖-->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.9.0</version>
</dependency>
<!--shiro缓存-->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-ehcache</artifactId>
    <version>1.9.0</version>
</dependency>
<!--shiro的thymeleaf支持-->
<dependency>
    <groupId>com.github.theborakompanioni</groupId>
    <artifactId>thymeleaf-extras-shiro</artifactId>
    <version>2.1.0</version>
</dependency>
```



###### 缓存需要配置

```xml
<ehcache updateCheck="false">
    <!-- 缓存存储的硬盘位置，java.io.tmpdir为默认的临时文件存放路径 -->
    <diskStore path="out/shiro"/>
    <!--1、maxElementsInMemory：最大缓冲量
        2、eternal：物理内存有Java虚拟机进行定时清理
        3、timeToIdleSeconds：最大空闲时间
        4、timeToLiveSeconds：最大存货时间
        5、maxElementsOnDisk：最大溢出大磁盘上
        6、diskPersistent：服务器重启之后是否有效
        7、memoryStoreEvictionPolicy：剔除策略-->
    <defaultCache
            maxElementsInMemory="10000"
            eternal="false"
            timeToIdleSeconds="120"
            timeToLiveSeconds="120"
            overflowToDisk="false"
            diskPersistent="false"
            diskExpiryThreadIntervalSeconds="120"/>
    <cache name="shiro-activeSessionCache"
           maxElementsInMemory="10000"
           eternal="true"
           overflowToDisk="true"
           diskPersistent="true"
           diskExpiryThreadIntervalSeconds="600"/>
    <cache name="org.apache.shiro.realm.SimpleAccountRealm.authorization"
           maxElementsInMemory="100"
           eternal="false"
           timeToLiveSeconds="600"
           overflowToDisk="false"/>

    <!--自定义缓存名-->
    <cache name="authorization"
           maxElementsInMemory="100"
           eternal="false"
           timeToLiveSeconds="600"
           overflowToDisk="false"/>
    <cache name="authentication"
           maxElementsInMemory="100"
           eternal="false"
           timeToLiveSeconds="600"
           overflowToDisk="false"/>
</ehcache>
```





###### 自己实现Realm-MyRealm

```java
//生成带有必需参数的构造函数，必需的参数是最终字段和具有约束的字段，例如@NonNull ，final
@RequiredArgsConstructor
//shiro的授权以及认证的类
public class MyRealm  extends AuthorizingRealm {
    
    private final IUserService userService;

    //【授权方法】认证之后才会来这里，授权角色
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        //获取通过验证的主体【对象】
        User principal = (User) principals.getPrimaryPrincipal();
        // 根据用户名查询用户的权限、角色信息，最后授权
        Set<String> roles = new HashSet<>();
        //查询数据库这个用户为什么角色
        String role = userService.getRoleById(principal.getId());
        if (role != null)
        {
            roles.add(role);
        }
        return new SimpleAuthorizationInfo(roles);
    }

    //【认证方法】查询数据库判断是否存在该用户
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        //获取用户的令牌
        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
        //获取用户令牌的信息
        String username = token.getUsername();
        //根据用户名查询用户是否存在
        User user = userService.getOne(new QueryWrapper<User>().eq("username", username));
        //没有查询到抛出用户不存在的异常
        if (user == null){
            throw new UnknownAccountException();
        }
        //余额低于0抛出用户被冻结的异常
        if (user.getMoney() < 0){
            throw new LockedAccountException();
        }
        //返回的主体信息
        Object principal = user;
        //需要验证的证书（数据库查出来的：密码，用来和用户输入的密码，加密后做比较）
        Object credentials = user.getPassword();
        //加密的盐值
        ByteSource salt =  ByteSource.Util.bytes(user.getUsername());
        //主体的名称
        String realmName = getName();
        //验证密码是否正确（会自动用配置好的加密算法加密）
        return new SimpleAuthenticationInfo(principal,credentials, salt,realmName);
    }
}
```



###### 配置所依赖的bean-ShiroConfig

```java
//注解为配置类
@Configuration
//注解，开启读取yml里面的属性，直接添加类属性，属性名不同，就给属性添加@Value("填yml的属性名")注解
@ConfigurationProperties(prefix = "shiro")
//自动生成get/set/toString
@Data
//生成带有必需参数的构造函数，必需的参数是最终字段和具有约束的字段，例如@NonNull ，final
@RequiredArgsConstructor
//配置shiro所需要的bean
public class ShiroConfig {

    //获取yml配置文件里面的属性
    private LinkedHashMap<String, String> chain;
    private String loginUrl;
    private String successUrl;
    private String unauthorizedUrl;
    private String hashAlgorithmName;
    private int hashIterations;

    private final IUserService userService;

    //构建自己实现的shiro授权以及认证的类
    @Bean
    public MyRealm realm(){
        MyRealm realm = new MyRealm(userService);

        //配置MD5加密：名称
        HashedCredentialsMatcher matcher = new HashedCredentialsMatcher(hashAlgorithmName);
        //配置MD5加密：迭代次数
        matcher.setHashIterations(hashIterations);
        realm.setCredentialsMatcher(matcher);

        //redis缓存相关，具体配置在ehcache.xml里面
        //开启授权缓存
        realm.setAuthorizationCachingEnabled(true);
        //开启身份验证缓存
        realm.setAuthenticationCachingEnabled(true);
        //设置授权缓存
        realm.setAuthorizationCacheName("authorization");
        //设置身份验证缓存
        realm.setAuthenticationCacheName("authentication");
        return realm;
    }

    //缓存管理配置
    @Bean
    public CacheManager cacheManager(){
        EhCacheManager cacheManager = new EhCacheManager();
        //读取缓存管理器配置文件
        cacheManager.setCacheManagerConfigFile("classpath:shiro/ehcache.xml");
        return cacheManager;
    }

    //配置shiro管理器
    @Bean
    public SecurityManager securityManager(Realm realm, CacheManager cacheManager) {
        DefaultWebSecurityManager manager = new DefaultWebSecurityManager(realm);

        //使用redis缓存管理
        manager.setCacheManager(cacheManager);
        return manager;
    }

    //配置shiro管理器工厂
    @Bean
    public ShiroFilterFactoryBean filterFactoryBean(SecurityManager manager){
        ShiroFilterFactoryBean filterFactoryBean = new ShiroFilterFactoryBean();
        //给工厂配置shiro管理器（也就是模型）
        filterFactoryBean.setSecurityManager(manager);
        //读取yml配置里面的map【配置请求路径所需要的权限以及角色】
        filterFactoryBean.setFilterChainDefinitionMap(chain);
        //读取yml配置里面的登录路径
        filterFactoryBean.setLoginUrl(loginUrl);
        //读取yml配置里面的登录成功
        filterFactoryBean.setSuccessUrl(successUrl);
        //读取yml配置里面的权限不足路径
        filterFactoryBean.setUnauthorizedUrl(unauthorizedUrl);
        return filterFactoryBean;
    }
}
```















#### 方法二：注解配置



###### 需要添加的依赖

```xml
<!--shiro注解支持-->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring-boot-web-starter</artifactId>
    <version>1.9.1</version>
</dependency>
```



###### 自己实现Realm-MyRealm

主要实现两个方法：

	1. doGetAuthorizationInfo：【授权方法】认证之后才会来这里，授权角色
 	2. doGetAuthenticationInfo：【认证方法】查询数据库判断是否存在该用户

```java
//生成带有必需参数的构造函数，必需的参数是最终字段和具有约束的字段，例如@NonNull ，final
@RequiredArgsConstructor
//shiro的授权以及认证的类
public class MyRealm  extends AuthorizingRealm {

    private final IUserService userService;

    //【授权方法】认证之后才会来这里，授权角色
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principals) {
        //获取通过验证的主体【对象】
        User principal = (User) principals.getPrimaryPrincipal();
        // 根据用户名查询用户的权限、角色信息，最后授权
        Set<String> roles = new HashSet<>();
        //查询数据库这个用户为什么角色
        String role = userService.getRoleById(principal.getId());
        if (role != null)
        {
            roles.add(role);
        }
        return new SimpleAuthorizationInfo(roles);
    }

    //【认证方法】查询数据库判断是否存在该用户
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        //获取用户的令牌
        UsernamePasswordToken token = (UsernamePasswordToken) authenticationToken;
        //获取用户令牌的信息
        String username = token.getUsername();
        //根据用户名查询用户是否存在
        User user = userService.getOne(new QueryWrapper<User>().eq("username", username));
        //没有查询到抛出用户不存在的异常
        if (user == null){
            throw new UnknownAccountException();
        }
        //余额低于0抛出用户被冻结的异常
        if (user.getMoney() < 0){
            throw new LockedAccountException();
        }
        //返回的主体信息
        Object principal = user;
        //需要验证的证书（数据库查出来的：密码，用来和用户输入的密码，加密后做比较）
        Object credentials = user.getPassword();
        //加密的盐值
        ByteSource salt =  ByteSource.Util.bytes(user.getUsername());
        //主体的名称
        String realmName = getName();
        //验证密码是否正确（会自动用配置好的加密算法加密）
        return new SimpleAuthenticationInfo(principal,credentials, salt,realmName);
    }
}
```



###### 配置所依赖的bean-ShiroConfig

```java
//注解为配置类
@Configuration
//注解，开启读取yml里面的属性，直接添加类属性，属性名不同，就给属性添加@Value("填yml的属性名")注解
@ConfigurationProperties(prefix = "shiro")
//自动生成get/set/toString
@Data
//生成带有必需参数的构造函数，必需的参数是最终字段和具有约束的字段，例如@NonNull ，final
@RequiredArgsConstructor
//配置shiro所需要的bean
public class ShiroConfig {

    //获取yml配置文件里面的属性
    private LinkedHashMap<String, String> chain;
    private String hashAlgorithmName;
    private int hashIterations;

    private final IUserService userService;

    //注解配置shiro所需bean
    @Bean
    public Realm realm() {
        AuthorizingRealm realm = new MyRealm(userService);
        //开启缓存
        realm.setCachingEnabled(true);
        //配置加密
        HashedCredentialsMatcher matcher = new HashedCredentialsMatcher();
        matcher.setHashAlgorithmName(hashAlgorithmName);
        matcher.setHashIterations(hashIterations);
        realm.setCredentialsMatcher(matcher);
        return realm;
    }

    //注解配置shiro所需bean
    @Bean
    public ShiroFilterChainDefinition chainDefinition() {
        //配置页面所需要的角色，和权限（可以不配置，直接在控制器使用注解配置）
        DefaultShiroFilterChainDefinition definition = new DefaultShiroFilterChainDefinition();
        definition.addPathDefinitions(chain);
        return definition;
    }
}
```



###### 注解的使用

注解在类，或者方法上

- 【@RequiresAuthentication】注解这个路径，需要验证
- 【@RequiresRoles({"admin"})】注解这个路径，需要特定角色

- 【@RequiresPermissions("document:read")】注解这个路径，拥有某些特定权限
- 【@RequiresGuest】不需要经过认证或者在原Session中存在记录可访问
- 【@RequiresUser】 必须为用户，比如登录或者记住我，和RequiresGuest刚好相反

```java
//游客也可以访问
@GetMapping("/t1")
public String test1(Model model){
    model.addAttribute("info", "这个页面不需要登录");
    return "test";
}

//    @RequiresPermissions("document:read")//注解这个路径，拥有某些特定权限
//    @RequiresGuest //不需要经过认证或者在原Session中存在记录可访问
//    @RequiresUser //必须为用户，比如登录或者记住我，和RequiresGuest刚好相反
@RequiresAuthentication//注解这个路径，需要验证
@GetMapping("/t2")
public String test2(Model model){
    model.addAttribute("info", "这个页面需要登录");
    return "test";
}

@RequiresRoles({"admin"})//注解这个路径，需要特定角色
@GetMapping("/t3")
public String test3(Model model){
    model.addAttribute("info", "这个页面需要admin角色");
    return "test";
}
```



###### 异常的拦截（权限，角色）

使用注解后抛出的异常拦截【**这里是直接转发到html页面**】

```java
@ControllerAdvice  // 指定被增强注解类型
public class MyControllerAdvice {

    //注解这些异常来这里处理
    @ExceptionHandler({UnauthorizedException.class})
    public String handleExl(UnauthorizedException e, Model model) throws IOException {
        model.addAttribute("error","没有权限");
        model.addAttribute("info", e.getMessage());
        return "error";
    }

    //注解这些异常来这里处理
    @ExceptionHandler({UnauthenticatedException.class})
    public String handleExl(UnauthenticatedException e, Model model) throws IOException {
        model.addAttribute("error","没有认证（没有登录）");
        model.addAttribute("info", e.getMessage());
        return "error";
    }
}
```





#### 中文路径名404问题

```java
public class CustomShiroFilterFactoryBean extends ShiroFilterFactoryBean {
    @Override
    protected FilterChainManager createFilterChainManager() {
        FilterChainManager manager = super.createFilterChainManager();
        // URL携带中文400，servletPath中文校验BUG
        Map<String, Filter> filterMap = manager.getFilters();
        Filter invalidRequestFilter = filterMap.get(DefaultFilter.invalidRequest.name());
        if (invalidRequestFilter instanceof InvalidRequestFilter) {
            ((InvalidRequestFilter) invalidRequestFilter).setBlockNonAscii(false);
        }
        return manager;
    }
}
```







#### thymeleaf支持：

​	依赖：com.github.theborakompanioni:thymeleaf-extras-shiro:2.1.0
​	配置：
​		@Bean public ShiroDialect getShiroDialect() {
​			return new ShiroDialect();
​		}
​	标签：
​		xmlns:shiro="http://www.pollix.at/thymeleaf/shiro"
​		shiro:hasPermission="user:add" 表示具有指定权限
​		shiro:lacksPermission="user:delete" 无此权限
​		shiro:hasRole="admin" 表示具有指定角色
​		shiro:lacksRole="admin" 表示无该角色
​		shiro:guest="" 访客
​		shiro:user="" 认证用户、记住我的用户
​		shiro:authenticated="" 已认证用户
​		shiro:notAuthenticated="" 未认证
​		shiro:principal="" 获取首要信息
​		shiro:hasAllRoles="developer, manager" 指定所有角色
​		shiro:hasAnyRoles="developer, manager" 含有某一个角色
​		shiro:hasAllPermissions="user:create, user:delete"
​		shiro:hasAnyPermissions="user:create, user:delete"
​	提取用户信息：
​		<shiro:principal>可以作为标签使用
​		<shiro:principal property="username">







# 反射代理生成接口对象-Proxy代理

1. 创建代理对象：
		Proxy.newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)
		loader - 类加载器，可以是当前类的类加载器、父类加载器、系统类加载器
		interfaces - 对象要实现哪些接口，可以使用多个接口
		InvocationHandler - 必须自定义的对象，调用方法时会触发InvocationHandler的invoke方法
	2. 创建InvocationHandler对象：
		class ProxyHandler implements InvocationHandler
		public Object invoke(Object proxy, Method method, Object[] args)
		proxy - 代理类代理的真实代理对象（com.sun.proxy.$Proxy0）
		method - 对象被调用的真实方法（Method对象）
		args - 方法被调用时传递的参数
	3. 应用：
		代理接口创建接口实例
		代理实体类，拦截行为



#### 创建参考：

```java
public class Main {
    @Test
    public void test1(){

        // 获取类加载器（几种方法）
        ClassLoader loader = Main.class.getClassLoader();
        loader = this.getClass().getClassLoader();
        loader = getClass().getClassLoader().getParent();
        loader = Thread.currentThread().getContextClassLoader();

        // 接口类型（必须是接口，可以是多个）
        Class<?>[] interfaces = {UserDao.class};

        // InvocationHandler对象
        InvocationHandler handler = new MyInvocationHandler();
        
        // Proxy.newProxyInstance可以创建一个UserDao对象
        UserDao userDao = (UserDao) Proxy.newProxyInstance(loader, interfaces, handler);
        // 可以随意正常使用这个对象了：
        System.out.println(userDao.findAll());
        System.out.println(userDao.findById());
    }
}
```



#### 实现需要的InvocationHandler对象

```java
public class MyInvocationHandler implements InvocationHandler {

    private final Object object = new Object();

    // proxy代理对象；method调用的方法；args调用方法的参数
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("你在调用方法：" + method.getName());
        System.out.println("\t携带的参数：" + Arrays.toString(args));
        switch (method.getName()){
            case "findAll":
                return new ArrayList<User>();
            case "findById":
                return new User(0,"kk","55",10.0);
        }
        // 其他方法都使用默认的object去调用：
        return method.invoke(object, args);
    }
}
```











# security登录验证，权限管理

映射路径所需，权限以及角色可以在Config配置，也可以开启注解在映射方法上配置

Config启用注解配置权限以及角色

@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)

**配置/Filter链**
	主要配置：
		http.authorizeRequests 配置哪些请求需要签名
		http.anyRequest 限定所有请求
		http.authenticated 设定所有签名成功的用户允许的方法
	其他方法：
		not取反；fullyAuthenticated必须完全验证，不能记住；denyAll拒绝访问所有；
		hasIpAddress指定IP地址访问；remenberme只要记住就可以访问，access指定SPEL规则
	强制HTTPS:
		.requiresChannel().antMatchers("/xx")
		.requiresSecure()/requresInsecure() // 开启、关闭HTTPS
	记住我：
		添加TokenBasedRememberMeServices的Bean，注意设置SECRET_KEY
		.rememberMe() // 开启记住密码功能
		.rememberMeServices(TokenBasedRememberMeServices) // 必须提供
		.key(SECRET_KEY) // 和生成TokenBasedRememberMeServices的密钥相同
	Basic认证：
		http.httpBasic().realmName("模态对话框的标题")
		基本认证会弹出模态对话框
	Filter链：
		@Bean public WebSecurityCustomizer customizer() {
			return web -> {
				web.ignoring().antMatchers("/static/**");
				// 其他配置
			}
		}

**记住我**
	要求：必须有UserDetailsService的Bean、自动注入DataSource的Bean
	HTML配置：<input name="remember-me" type="checkbox"/>记住我
	直接配置：
        http.rememberMe()
            .tokenValiditySeconds(60)
            .key("123456123456123456")
	使用JDBC持久化：
        http.rememberMe()
            .tokenRepository(repository) // 配置持久层
            .userDetailsService(users) // 处理自动登录
	Bean：
		@Bean public PersistentTokenRepository repository(DataSource dataSource) {
			JdbcTokenRepositoryImpl repository = new JdbcTokenRepositoryImpl();
			repository.setDataSource(dataSource);
			repository.setCreateTableOnStartup(false); // 如果存在表则配置为false
			return repository;
		}



#### 需要依赖

```xml
<!--spring-Security登录验证，权限管理-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<!--thymeleaf支持security-->
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-springsecurity5</artifactId>
</dependency>
<!--security记住功能需要-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```





#### 配置类Config

```java
/*
自定义SpringSecurity相关配置：
    1. 必须有一个且只有一个UserDetailsService的Bean
    2. 配置核心安全拦截过滤器：SecurityFilterChain的Bean对象，自动注入一个默认的HttpSecurity
开启认证注解：
    @EnableWebSecurity 开启Web项目验证注解
    @EnableGlobalMethodSecurity 开启方法注解
 */
@Configuration
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)
@RequiredArgsConstructor
public class SecurityConfig {

    //加密需要的bean
    @Bean
    public PasswordEncoder encoder() {
        return new BCryptPasswordEncoder();
    }

    //核心
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http, UserDetailsService userDetailsService, JdbcTokenRepositoryImpl repository) throws Exception {
        //1.必须有请求认证过滤配置
        http.authorizeRequests()
                        .anyRequest().permitAll();//允许任何请求访问

        //2.自定义登录表单
        http.formLogin()
                .permitAll()// 保证页面可以访问，因为默认情况下都必须登录
                .loginPage("/login.html")// 自定义的登录页：有表单，对应Controller的映射
                .loginProcessingUrl("/login") // 固定死：内置的请求处理路径，那么在表单上action必须用这个
                .failureUrl("/login.html?error")// 登录失败，任意
                .defaultSuccessUrl("/success.html");// 自定义成功后转发的页面，任意

        //3.自定义登出页
        http.logout()
                .logoutUrl("/logout")// 固定死：内置请求处理路径，用在登出表单action中，登出表单要加csrf
                .logoutSuccessUrl("/login.html?logout")// 自定义登录成功，和下面的二选一，随意
//                .logoutSuccessHandler((request, response, authentication) -> response.sendRedirect("/login.html?logout"))
//                .invalidateHttpSession(true)// 登出后默认就会清除会话
//                .deleteCookies("remember-me")// 登出后可以手动删除一些自定义的cookie
                .addLogoutHandler((request, response, authentication) -> System.out.println("【日志】登出了"));

        //4.CSRF自动开启
//        http.csrf().disable();

        //5.记住我的功能
        http.rememberMe()//开启记住我的功能：1.jdbc依赖；2.mysql；3.配置JdbcTokenRepositoryImpl
                .tokenValiditySeconds(600)//有效时间【600秒】
                .userDetailsService(userDetailsService)//Cookie关联用户服务
                .rememberMeParameter("remember-me")// 默认，可以自定义参数名
                .tokenRepository(repository)// 自动注入JdbcTokenRepositoryImpl的Bean
                .key("123");//唯一的一个key，自定义

        //6.登录权限异常处理
        http.exceptionHandling()
                .accessDeniedPage("/403.html");//自定义页面；无访问权限

        //7.其他配置
//        http.httpBasic()//默认开启

        //8.返回
        return http.build();
    }

    // 依赖：1. JDBC依赖，2. MySQL依赖，3. 自动注入DataSource
    @Bean
    public JdbcTokenRepositoryImpl repository(DataSource dataSource){
        JdbcTokenRepositoryImpl repository = new JdbcTokenRepositoryImpl();
        repository.setDataSource(dataSource);
        repository.setCreateTableOnStartup(false);
        return repository;
    }
}
```







#### 验证用户登录类,授权角色及权限

```java
//用于数据库就验证以及加密
@RequiredArgsConstructor
@Service
public class MyUserDetailsService implements UserDetailsService {

    private final PasswordEncoder encoder;
    private final IUserService userService;

    //用户登录验证方法
    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        if (username == null || username.trim().isEmpty()) {
            throw new UsernameNotFoundException("用户名不能为空");
        }
        //查询数据库
        com.zking.entity.User user = userService.getOne(
                new QueryWrapper<com.zking.entity.User>().eq("username", username));
        if (user == null){
            throw new UsernameNotFoundException("用户不存在");
        }
        String name = user.getUsername();
        String password = user.getPassword();
        StringBuilder authorities = new StringBuilder();
        //查询用户的角色
        userService.getRolesByUserId(user.getId())
                .forEach(role -> {
                    assert false;
                    authorities.append("ROLE_").append(role.getName()).append(",");
                });
        //查询用户的权限
        userService.getAuthoritySByUserId(user.getId())
                .forEach(role -> {
                    assert false;
                    authorities.append(role.getName()).append(",");
                });
        // 返回User对象，这里的User对象是Spring自带的类
        return new User(
                name, password, AuthorityUtils.commaSeparatedStringToAuthorityList(String.valueOf(authorities)));
    }
}
```







#### 控制器Controller

**注解在类上代表所有映射路径**

【@DenyAll 】拒绝访问
【@PermitAll 】允许访问
【@RolesAllowed】 == 【@Secured 】安全认证，设置角色
【@PreAuthorize】 支持spEL表达式，类似@Secured注解，进入方法之前验证授权
【@PostAuthorize 】支持spEL表达式，检查授权方法之后才被执行
【@PreFilter】 在方法执行之前执行，可调用使用方法的参数
【@PostFilter】 在方法执行之后执行，可调用使用方法的返回值

SPEL表达式：
	hasRole() 是否拥有某一个权限
	hasAnyRole() 多个权限中有一个即可
	hasAuthority() 和Role一样，前缀多了ROLE_ 
	hasAnyAuthority() 多个权限中有一个即可
	hasPermission() 权限
	permitAll()/denyAll()/isAnonymous()/isRememberMe()
	isAuthenticated()/isFullyAuthenticated() 登录用户认证【后者不能为记住的用户】
	principal() 首要信息，等同于User
	authentication() 认证相关信息



###### 自定义注解配置：

​	核心：
​		@Conditional 标识一个Bean或者Configuration配置文件满足指定条件才开启配置
​	常见注解：
​		@ConditionalOnBean/@ConditionalOnMissingBean 当容器中有/没有指定的Bean才开启配置
​		@ConditionalOnClass/@ConditionalOnMissingClass 当容器中有/没有指定的Class才开启配置
​		@ConditionalOnWebApplication/@ConditionalOnNotWebApplication 当前项目类型是/不是WEB项目才开启配置
​		@ConditionalOnProperty 当指定的属性有指定的值时才开启配置
​		@ConditionalOnExpression 当SpEL表达式为true时才开启配置
​	自定义：
```java
@Target({ElementType.TYPE, ElementType.METHOD})//方法和类型可用
@Retention(RetentionPolicy.RUNTIME)//运行是配置
@Inherited
@Documented
@PreAuthorize("hasRole('user') and hasAuthority('insert')")//需要角色以及权限
public @interface IsUserWithInsert {
}
```





###### 允许所有访问

```java
@GetMapping("/t1")
public String test1(HttpSession session, Model model) {
    model.addAttribute("path", "test1");
    model.addAttribute("info", "不需要要任何权限");
    return "test";
}
```





###### 拒绝所有访问

```java
@DenyAll
@GetMapping("/t2")
public String test2(HttpSession session, Model model) {
    model.addAttribute("path", "test2");
    return "test";
}
```





###### 需要指定角色

可以写多个只需要一个就行

```java
//需要指定角色
@RolesAllowed({"admin"})
@GetMapping("/t3")
public String test3(HttpSession session, Model model) {
    model.addAttribute("path", "test3");
    model.addAttribute("info", "需要admin角色");
    return "test";
}
```



###### 需要多个指定角色

```java
@PreAuthorize("hasRole('admin') and hasRole('user')")
```



###### 需要多个角色中的一个

```java
@PreAuthorize("hasRole('admin') or hasRole('user')")
```





###### 需要指定权限

```java
//需要指定权限
@PreAuthorize("hasAuthority('delete')")
@GetMapping("/t4")
public String test4(HttpSession session, Model model) {
    model.addAttribute("path", "test4");
    model.addAttribute("info", "需要delete权限");
    return "test";
}
```



###### 需要多个指定权限

```java
@PreAuthorize("hasAuthority('delete') and hasAuthority('read')")
@GetMapping("/t444")
public String test444(HttpSession session, Model model) {
    model.addAttribute("path", "test44");
    model.addAttribute("info", "需要delete权限和read权限");
    return "test";
}
```



###### 需要多个权限中的一个

```java
@PreAuthorize("hasAuthority('delete') or hasAuthority('read')")
@GetMapping("/t44")
public String test44(HttpSession session, Model model) {
    model.addAttribute("path", "test44");
    model.addAttribute("info", "需要delete权限或者read权限");
    return "test";
}
```







#### 前端获取验证的用户信息

```html
<div>
    <p>登录用户：<span th:text="${user}"></span></p>
    <div th:if="${#authentication.authenticated}">
        <p>已经登录的用户名：<span th:text="${#authentication.name}"></span></p>
        <hr/>
        <p>所有权限：<span th:text="${#authentication.authorities}"></span></p>
        <p>Details信息：<span th:text="${#authentication.details}"></span></p>
        <p>Session凭证：<span th:text="${#authentication.credentials}"></span></p>
        <p>主题信息：<span th:text="${#authentication.principal}"></span></p>
    </div>
</div>
```







#### Thymeleaf：

​	依赖：
​		org.thymeleaf.extras:thymeleaf-extras-springsecurity5
​	命名空间：
​		xmlns:sec="http://www.thymeleaf.org/extras/spring-security"
​	表达式：
​		th:text="#authentication.name"
​		th:if="${#authorization.expression('hasRole(''ROLE_ADMIN'')')}"
​	自带属性：
​		sec:authentication="name"
​		sec:authorize="hasRole('ROLE_ADMIN')"
​		sec:authorize="${hasRole(#vars.expectedRole)}"
​		sec:authorize-url="/admin"
​		<th:block sec:authentication="principal.id|username|authorities"/>











# 拉取第三方登录

#### RestTemplate

​	Restful客户端，用于在服务端远程调用一个HTTP接口
​	测试可以使用TestRestTemplate类
​	主要方法：
​		getForObject/getForEntity 发起GET请求
​		postForObject/postForEntity 发起POST请求
​		headForHeaders 获取头部信息
​		optionsForAllow 获取允许的HTTP方法
​		exchange 通用，支持GET、POST、DELETE、PUT等常规REST方法
​		execute 通过Callback接口对请求和返回做更加全面的自定义控制
​	创建：
​		@Bean SimpleClientHttpRequestFactory
​		@Bean RestTemplate
​	返回值：
​		可以直接返回实体类：getForObject/postForObject
​		也可以返回响应体：getForEntity/postForEntity
​		响应体能获取头部信息、状态码、响应体等
​	请求体：
​		// 添加键值对
​		MultiValueMap<String, Object> body = new LinkedMultiValueMap<>();
​		body.add("键", 值);
​		// 设置头部
​		HttpHeaders headers = new HttpHeaders();
​        headers.add("Accept", MediaType.APPLICATION_JSON_VALUE);
​        headers.setContentType(MediaType.APPLICATION_FORM_URLENCODED);
​        // 构建请求
​        HttpEntity<MultiValueMap<String, Object>> request = new HttpEntity<>(body, headers);
​        // 或者：
​		RequestEntity.post(uri)
​			.header(HttpHeaders.COOKIE,"key=value") // 添加Cookie
​			.header(("键", "值") // 添加其他头部
​			.accept(MediaType.APPLICATION_JSON) // 设置accept
​			.contentType(MediaType.APPLICATION_JSON)
​			.body(requestParam); // 表单内容





###### 配置dean

```java
//配置发送请求所需的bean(如果使用无参构造就不需要配置ClientHttpRequestFactory)
@Bean
public RestTemplate restTemplate(ClientHttpRequestFactory factory) {
    return new RestTemplate(factory);
}

//用于配置RestTemplate
@Bean
public ClientHttpRequestFactory simpleClientHttpRequestFactory() {
    SimpleClientHttpRequestFactory factory = new SimpleClientHttpRequestFactory();
    factory.setReadTimeout(5000);//响应超时
    factory.setConnectTimeout(15000);//连接超时
    // 设置代理
    //factory.setProxy(null);
    return factory;
}
```



###### 请求的发送

**getForObject**

```java
String s = restTemplate.getForObject(url, String.class);
```



**getForEntity**

```java
ResponseEntity<String> forEntity = restTemplate.getForEntity(url, String.class);
String body = responseEnforEntitytity.getBody(); // 获取响应体
```



**postForObject**

```java
String s = restTemplate.postForObject(url, token, String.class);
```

**postForEntity** 

```java
// 发送post请求，并输出结果
ResponseEntity<String> responseEntity = restTemplate.postForEntity(url, token, String.class);
String body = responseEntity.getBody(); // 获取响应体
```









# 监听事件：

​	注解：@EventListener({监听的类.class})
​	参数：ApplicationEvent接口的实现类
​	示例：
​		AuthenticationSuccessEvent 认证成功事件
​		AbstractAuthenticationFailureEvent 认证失败事件
​		ContextStartedEvent 上下文启动事件
​		ContextClosedEvent 上下文销毁事件
​	自定义：



根据参数来调用发生的事件

```java
//监听器
@EventListener
public void loginSuccess(AuthenticationSuccessEvent event){
    System.out.println("登录成功：" + event.getAuthentication());
}

// 登录认证失败的消息处理
@EventListener
public void loginFail(AbstractAuthenticationFailureEvent event){
    System.out.println("登录失败：" + event.getException());
}
```









# JSON注解

用来注解实体类属性，作用在实体类转json时

​	@JsonInclude(JsonInclude.Include.NON_NULL) 序列化条件
​	@JsonIgnore/@JsonIgnoreProperties({}) 忽略（可以设置多个属性在类上）
​	@JsonProperty/JsonAlias 给属性设置别名
​	@JsonFormat(shape=Shape.STRING,pattern="",timezone="GMT+8")
​	@JsonRawValue 属性的值为json字符串，不会序列化为对象
​	@JsonFilter 过滤器
​	@JsonAnyGetter/JsonAnySetter 扁平化处理用在Map类上
​	@JsonUnwrapper 扁平化处理，用在对象属性上
​	@JsonManagedReference+@JsonBackReference 解决父子嵌套外链问题







# 异步任务

​	开启：@EnableAsync，注解在启动类或者配置类
​	实现接口：AsyncConfigurer 用于配置自定义线程池，可以不配置，使用默认的
​	应用：@Async标记Service的方法即可



直接调用即可，会从线程池里面拿出一个线程执行

```java
@Async
public void testAsync(){
    System.out.println("异步当前线程" + Thread.currentThread().getName());
}
```











# 定时任务

​	开启：@EnableScheduling，注解在启动类或者配置类
​	使用：@Component+@Scheduled+@Async
​	配置：
​		cron 表达式定义执行时间（秒 分 时 日 月 星期 年）
​		zone 区域时间
​		fixedDelay/fixedDelayString 固定间隔（完成->开始）
​		initialDelay/initialDelayString 首次延迟
​		fixedRate/fixedRateString 固定间隔（开始->开始）
​	示例：
​		@Scheduled(cron="0 * 11 * * ?") 从11:00到11:59每分钟一次
​		@Scheduled(cron="0 0-5 11 * * ?") 从11:00到11:05每分钟一次
​		@Scheduled(cron="0 0 20 ? * MON-FRI") 每周一到周五的20:00
​		@Scheduled(cron="0 0 20 ? * 6#3") 每月第3周的周五的20:00



无需调用，会自动运行

```java
@Scheduled(fixedDelay = 5000)//每5秒运行一次
public void test3(){
    System.out.println("每5秒运行一次");
}

private int count;
@Scheduled(fixedRate = 3000, initialDelay = 5000)//5秒后每3秒运行异常
public void test4(){
    System.out.println("5秒后每3秒运行异常" + count++);
}
```









# 配置条件（bean注入的条件）

​	核心：
​		@Conditional 标识一个Bean或者Configuration配置文件满足指定条件才开启配置
​	常见注解：
​		@ConditionalOnBean/@ConditionalOnMissingBean 当容器中有/没有指定的Bean才开启配置
​		@ConditionalOnClass/@ConditionalOnMissingClass 当容器中有/没有指定的Class才开启配置
​		@ConditionalOnWebApplication/@ConditionalOnNotWebApplication 当前项目类型是/不是WEB项目才开启配置
​		@ConditionalOnProperty 当指定的属性有指定的值时才开启配置
​		@ConditionalOnExpression 当SpEL表达式为true时才开启配置
​	自定义：



```java
//按条件注入bean，自定义条件
@Conditional(MyCondition.class)
@Bean
public User User1(){
    User user = new User();
    user.setUsername("狗管理");
    return user;
}

//按条件注入：如果user类型的bean不存在就注入
@ConditionalOnMissingBean(User.class)
@Bean
public User User2(){
    User user = new User();
    user.setUsername("狗群员");
    return user;
}
```











# SpringBoot打包插件

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.zking.App</mainClass>
            </configuration>
            <executions>
            <execution>
                <goals>
                    <goal>repackage</goal>
                </goals>
            </execution>
        </executions>
        </plugin>
    </plugins>
</build>
```





















