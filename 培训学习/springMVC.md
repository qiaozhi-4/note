# 配置控制器

1. 【@Controller】：控制层相关的bean

2. 【@RequestMapping() 】： 

   用在类上表示一级路径，下面配置访问方法：【上下文路径+/jsp/test】

   配置请求路径(哪些请求由这个方法处理)

   参数：

   1. 【method】：请求方式（常用：GET，POST，DELETE，PUT）

   2. 【params】： 请求必须携带指定参数

   3. 【headers】：请求头

   4. 【produces】：指定返回的数据类型是什么配合【@ResponseBody】

      指定为html：produces = {"text/html;charset=utf-8"}

      指定为json：produces = {"application/json;charset=utf-8"}

3. 【@ResponseBody】：返回的内容直接为响应体

4. 【@RequestBody】： 表示请求体，对于POST有效

5. 注解的方法参数（**参数名必须和前段传过来的参数名一样**）

   1. 【Model】：可以设置响应的内容

   2. 【name】：请求传过来的参数

   3. 【hobby】：请求传过来的数组参数（比如表单多选框name相同，会自动转为数组）

   4. 使用注解给指定传过来的参数【main(@RequestParam("必须和传过来的参数名一样") String name) 】（指定之后就必须携带改参数）

   5. 支持JavaBean/POJO对象【main(User user)】

      比如表单提交多个属性（表单name必须和实体类字段一致）

   6. 【@PathVariable("name") String name 

      使用场景把请求路径当做请求参数

      ```java
      @RequestMapping(value = "/user/{name}/{oid}")
          public String find(@PathVariable("name") String name, @PathVariable("oid") int oid, Model model) {
              model.addAttribute("date", "name：" + name + "\t订单id：" + oid);
              return "test";
          }
      ```

      

6. 【返回值】：

   1. 返回值为【String】需要转发到的【视图名】，文件路径和后缀都在【springmvc.xml】里面配置

   2. 返回值为【ModelAndView】

      ```java
      @RequestMapping(value = "/a4")
      public ModelAndView a4() {
          ModelAndView view = new ModelAndView();
          //添加响应数据
          view.addObject("date","hhhhhhh");
          //设置转发到的视图名
          view.setViewName("test");
          return view;
      }
      ```



6. 转发和重定向：

   转发：forward

   重定向：redirect

   ```java
   //转发的方式
   @RequestMapping(value = "/a5")
   public String a5(Model model) {
       model.addAttribute("name","转发");
       return "forward:/a4";
   }
   
   //重定向的方式
   @RequestMapping(value = "/a6")
   public String a6(Model model) {
       //Model的属性值在url请求的路径中
       model.addAttribute("name","重定向");
       return "redirect:/a4";
   }
   ```





```java
//- 控制层相关的bean
@Controller
@RequestMapping("/jsp")
public class MyController {
    //配置请求路径
    @RequestMapping(value = "/test", 
                    method = RequestMethod.GET,
                    params={"name","hobby"})
    public String test(String name, String[] hobby, Model model){
        //设置响应内容
        model.addAttribute("name", name);
        model.addAttribute("hobby", Arrays.toString(hobby));
        //转发到这个页面
        return "test";
    }
}
```











# 配置spring.xml

1. 【context:component-scan】：开启注解扫描（扫描配置的**控制器**所在路径）

   【context:include-filter】：筛选（扫描条件，这里是有@Controller注解才扫描）

2. 【mvc:annotation-driven】开启注解驱动，为MVC提供aop注入

3. 配置视图解析器：配置为bean（为控制器的方法配置请求地址）

   相当于："/WEB-INF/jsp/" + "方法返回的参数" + ".jsp"

   属性：

   1. 【prefix】：视图（html，jsp）对应的路径
   2. 【suffix】：什么视图，也就是后缀，这里为jsp

```xml
<!--开启注解扫描-->
<context:component-scan base-package="com.xh.controller">
    <!--只扫描Controller注解-->
    <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
</context:component-scan>

<!--开启注解驱动：相当于提供MVC的aop注入-->
<mvc:annotation-driven conversion-service="conversionServiceFactoryBean"/>

<!--配置视图解析器-->
<bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix" value="/WEB-INF/jsp/"/>
    <property name="suffix" value=".jsp"/>
</bean>

<!--静态资源不需要被拦截分发-->
<mvc:default-servlet-handler/>

<!--自定义转换器：把字符串转日期-->
<bean id="conversionServiceFactoryBean" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <set>
            <bean class="com.xh.converter.DateConverter"/>
        </set>
    </property>
</bean>

<!--上传图片-->
<bean id="multipartResolver" class="org.springframework.web.multipart.support.StandardServletMultipartResolver"/>

```













# 配置web.xml

1. 配置控请求分发器（需要它把请求分给**控制器的对应方法**）
   - 【servlet-class】：请求分发器的类包
   - 【init-param】：分发器需要的参数：springmvc.xml的路径
   - 【load-on-startup】：正数：和tomcat一起启动
2. 【url-pattern】：映射的路径“/”代表所有

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
    <!--spring配置初始化，这样就不用手动读取spring.xml-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring.xml</param-value>
    </context-param>

    <!--配置springmvc的请求分发器-->
    <servlet>
        <!--springmvc提供的servlet-->
        <servlet-name>dispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--配置初始化参数：读取springmvc.xml配置文件-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        <!--正数:和tomcat一起启动，必须和tomcat一起启动-->
        <load-on-startup>1</load-on-startup>
        <!--上传文件-->
        <multipart-config>
            <location>./</location>
            <max-file-size>10485760</max-file-size>
        </multipart-config>
    </servlet>
    <servlet-mapping>
        <!--请求映射配置-->
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--配置post乱码:-->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

</web-app>
```





#### 配置自动读取spring.xml

```xml
<!--spring配置初始化-->
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>classpath:spring.xml</param-value>
</context-param>
```









# 请求传过来的时间字符串转Date

##### 控制器配置

```java
@RequestMapping(value = "/date")
public String date(Date date, String name, Model model) {
    model.addAttribute("date", date);
    model.addAttribute("name", name);
    return "test";
}
```





##### 实现转换的类

```java
public class DateConverter implements Converter<String , Date> {
    @Override
    public Date convert(String source) {
        SimpleDateFormat format = new SimpleDateFormat("yyyy-MM-dd");
        try {
            return format.parse(source);
        } catch (ParseException e) {
            throw new RuntimeException(e);
        }
    }
}
```





##### spring.xml

```xml
<!--开启注解驱动：相当于提供MVC的aop注入-->
<mvc:annotation-driven conversion-service="conversionServiceFactoryBean"/>

<!--自定义转换器：把字符串转日期-->
<bean id="conversionServiceFactoryBean" class="org.springframework.context.support.ConversionServiceFactoryBean">
    <property name="converters">
        <set>
            <bean class="com.xh.converter.DateConverter"/>
        </set>
    </property>
</bean>
```





# 文件上传

##### 表单

```jsp
<form action="${pageContext.servletContext.contextPath}/upload" method="post" enctype="multipart/form-data">
    图片：<input type="file" name="file"><br>
    <button type="submit" class="btn btn-primary">提交</button>
</form>
```





##### 控制器配置

```java
/*文件上传*/
@RequestMapping(value = "/upload1", method = RequestMethod.POST)
//设置上传文件最大值
//    @MultipartConfig(maxFileSize = 1024000)
public String upload1(MultipartFile file, Model model, HttpServletRequest request) throws IOException {
    if (file != null){
        //不重复随机字符串，file.getOriginalFilename()获取上传的文件名
        String  name = UUID.randomUUID().toString() + file.getOriginalFilename();
        //上传到电脑里面穿路径
        file.transferTo(new File(request.getServletContext().getRealPath("static/images/") + name));
        model.addAttribute("date","文件上传成功");
        model.addAttribute("name","static/images/" + name);
    }else {
        model.addAttribute("date","文件上传失败");
    }
    return "test";
}
```





##### spring.xml

```xml
<!--上传图片--><bean id="multipartResolver" class="org.springframework.web.multipart.support.StandardServletMultipartResolver"/>
```





##### web.xml

配置为springmvc的请求分发器servlet的属性

```xml
<!--上传文件-->
<multipart-config>    
    <location>./</location>    
    <max-file-size>1024000</max-file-size>
</multipart-config>
```







# 属性相关

#### @ModelAttribute

修饰方法:**当前控制器类共享属性**(每个方法运行前都会运行这个方法)

```java
@ModelAttribute
public void model(Model model, HttpServletRequest request) {
    String path = request.getContextPath();
    model.addAttribute("$",path);
    User user = new User(1, "qz", "123", 100.0);
    model.addAttribute("user", user);
}
```

获取参数：

```java
//使用【model.getAttribute("user");】获取需要强转不推荐
@RequestMapping("/test2")
public String test2(Model model) {
    User user = (User) model.getAttribute("user");
    user.setUsername("test2");
    model.addAttribute("data", "共用数据");
    model.addAttribute("var1", "其他数据");
    //转发到这个页面
    return "test";
}

// 【@ModelAttribute("user") User user】使用注解修饰参数获取推荐
@RequestMapping("/test3")
public String test3(Model model, @ModelAttribute("user") User user) {
    user.setUsername("test3");
    model.addAttribute("data", "共用数据");
    model.addAttribute("var2", "其他数据");
    //转发到这个页面
    return "test";
}
```









#### @SessionAttributes

如果需要在**不同控制器**之间共享数据使用，只能修饰类
【@SessionAttributes({"user","data"})】：标注共享的属性名

用在属性为【@SessionAttribute】

**设置**：【Model】和【session】设置的同名属性都会给他赋值

**获取**：【@SessionAttribute("user") User user】可以多个控制器获取，如果没有设置这个参数会报错

​			【@ModelAttribute("user") User user】只能当前控制器获取，如果没有设置这个参数会是null

**清除**：SessionStatus.setComplete()可以清除该属性，但是request.getSession.setAttribute("xxx",null)无法清除

```java
//- 控制层相关的bean
@Controller
//标注共享的属性名
@SessionAttributes({"user","data"})
public class MyController2 {
    @ModelAttribute
    public void model(Model model){
        model.addAttribute("data","data");
    }
    //配置请求路径
    @RequestMapping("/page1")
    public String page1(Model model){
        //转发到这个页面
        return "test";
    }
    //配置请求路径
    @RequestMapping("/page2")
    public String page2(HttpSession session, Model model){
        User user = new User(1, "乔治", "123", 100.0);
        session.setAttribute("user", user);
        model.addAttribute("var2","其他数据");
        //转发到这个页面
        return "test";
    }
    //配置请求路径
    @RequestMapping("/page3")
    public String page3(@ModelAttribute("user") User user, @SessionAttribute("data") String data){
        System.out.println(user);
        System.out.println(data);
        //转发到这个页面
        return "test";
    }
    //配置请求路径
    @RequestMapping("/page4")
    public String page4(@SessionAttribute("user") User user, @ModelAttribute("data") String data){
        System.out.println(user);
        System.out.println(data);
        //转发到这个页面
        return "test";
    }
}
```







# 异常处理

1. 创建异常处理类
2. springMVC配置异常处理bean



#### 自己创建异常类

```java
@Getter @Setter
public class MyException extends RuntimeException{

    private String message;//错误信息

    public MyException(String msg) {
        super(msg);
        this.message = msg;
    }
}
```



#### 异常处理类

```java
//异常处理器
//需要配置到springMVC配置文件中
public class MyExceptionHandler implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) {
        //创建视图
        ModelAndView view = new ModelAndView();
        //判断是不是自己写的异常
        if (ex instanceof MyException){
            view.addObject("error","自定义异常信息" + ex);
        }else {
            view.addObject("error","其他异常信息" + ex);
        }
        //设置视图名称
        view.setViewName("error");
        return view;
    }
}
```



#### springMVC配置异常处理类

```xml
<!--异常处理配置-->
<bean class="com.xh.exception.MyExceptionHandler"/>
```













# 拦截器

1. 定义拦截器
2. springMVC配置拦截器



#### 定义拦截器

1. preHandle - 处理请求之前可以进行拦截；返回true方行，false不放行
2. postHandle - 请求处理之后，视图显示【之前】，可以：【设置视图、数据】，如果爆发异常，【不会】经过此方法
3. afterCompletion - 请求处理之后，视图已经显示【可以进行资源相关的清理工作】

```java
//自定义拦截器
public class MyInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle");
        return true;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion");
    }
}
```





#### springMVC配置拦截器

```xml
<!--配置拦截器-->
<mvc:interceptors>
    <!--配置多个拦截器-->
    <mvc:interceptor>
        <!--拦截哪些-->
        <mvc:mapping path="/*"/>
        <!--不拦截哪些-->
        <!--<mvc:exclude-mapping path=""/>-->
        <bean class="com.xh.interceptor.MyInterceptor"/>
    </mvc:interceptor>
</mvc:interceptors>
```









# 配置静态资源

访问：webapp/static/images/拉克丝狗年限定.jpg

输入：http://localhost:8080/springMVC02Job_war_exploded/img/拉克丝狗年限定.jpg

```xml
<!--给静态资源配置路径-->
<!--location:从webapp开始的路径-->
<!--mapping:替代访问路径-->
<mvc:resources location="/static/images/" mapping="/img/**"/>
<mvc:resources location="/static/js/" mapping="/js/**"/>
```

























# Ajax请求

#### sprMVC配置

```xml
<!--配置json转换器-->
<bean class="org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter">
    <property name="messageConverters">
        <list>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="defaultCharset" value="utf-8"/>
            </bean>
        </list>
    </property>
</bean>
```







jQuery发起请求：$.get()/$.post()/$.ajax()

#### 前段接受和发送

参数：

	1. 请求地址，
 	2. 请求发送的数据（为json格式）

方法：

	1. 【.done】请求成功后调用data为返回的数据
 	2. 【.fail】请求失败调用

```jsp
$.post('login', data)
            .done(function (data) {
                console.log(data)
            })
            .fail(data => console.log(data))
```







#### 后端接受和发送

接收数据：json数据可以直接用实体类对象

返回数据：直接返回对象或者集合

注解：

 	1. 【@RequestMapping】：
      	1. value：配置请求路径
      	2. produces：配置返回什么参数（这里为json）
	2. 【@ResponseBody】：标记返回的是数据而不是视图

```java
//配置请求路径
@RequestMapping(value = "/login", produces = {"application/json;charset=utf-8"})
@ResponseBody
public List<Order> login(User user) {
    //获取bean
    ApplicationContext context = new ClassPathXmlApplicationContext("spring.xml");
    //获取bean
    UserService userService = context.getBean(UserService.class);
    user = userService.findByNameAndPass(user.getUsername(), user.getPassword());
    if (user != null){
        OrderService orderService = context.getBean(OrderService.class);
        return orderService.findByUid(user.getId());
    }
    return null;
}
```

