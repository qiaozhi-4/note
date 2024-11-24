## 乱码：

```java
req.setCharacterEncoding("utf-8"); //对于POST参数乱码有效，GET不需要，没有乱码
resp.setContentType("text/html; charset=utf-8"); //对于客户端网页页面乱码问题

request.setCharacterEncoding("utf-8"); //对于POST参数乱码有效，GET不需要，没有乱码
response.setContentType("text/html; charset=utf-8"); //对于客户端网页页面乱码问题
```









## jsp里面直接使用的对象

1. out 用于页面内容的输出，out.write(输出要到页面) 类似 resp.getWriter().write(内容)
2. request 当前页面请求
3. response 响应
4. application 是一个ServletContext上下文对象，完全等同于req.getServletContext()==application在JSP里
5. session 当前页面的会话





## 常用方法

- 获取上下文

  1. **jsp**里面获取

     request.getContextPath()

     application.getContextPath()

  2. **Servlet**里面获取

     req.getContextPath()
  
- 获取表单属性

  req.getParameter("user");【获取表单name值为user的】

-  ServletContext
    	ServletContext servletContext = req.getServletContext();【获取】
        	相当于Web容器的全局变量，不适合保存每次会话信息，适合保存一些全局变量
        	getAttribute("String") 获取Context中保存的属性值
        	setAttribute("String", obj) 设置Context属性值，全局
    
- Cookie方法

     [https://blog.csdn.net/weixin_42697074/article/details/88904020](https://blog.csdn.net/weixin_42697074/article/details/88904020)





## 转发/重定向

```java
//转发
req.getRequestDispatcher("/home.jsp").forward(req, resp);
//重定向
resp.sendRedirect(req.getContextPath() + "/error.jsp");
```







## 创建Cookie

创建Cookie：Cookie cookie = new Cookie(名字，值);【两个字符串】
添加Cookie到客户响应：response.addCookie(cookie); 可以添加多个
查询Cookie：Cookie[] cookies = request.getCookies() 可能是null，也可能是一个Cookie数组
Cookie的方法：
	cookie.getName() 获取名字
	cookie.getValue() 获取值
	cookie.setMaxAge(30) 设置生命周期，设置失效时间
		如果设置为0表示立即关闭；
		正数表示存活周期单位秒；
		负数一般表示浏览器自行处理，比如关闭浏览器就没了
特点：
	保存在客户端（删除、修改）
	不是特别安全（不能保存敏感信息
	大小有限制、字符串（4kb左右）





## 创建Session

会话：服务器端处理
获取会话：HttpSession session = request.getSession() 如果没有会自动创建一个
getAttribute 获取session中保存的属性值
setAttribute(名字，属性) 设置session属性值【名字是字符穿，属性是obj对象】
session.invalidate() 让session失效
特点：
	保存在服务器
	安全
	大小没有限制/任意类型
在JSP中可以直接使用session，表示当前页面的会话

===> F12浏览器中查看Cookie和Session



## EL表达式

获取上下文：${pageContext.servletContext.contextPath}

${表达式}
		empty表示空：值为null、数组列表没有元素、字符串""
		${empty list} 表示列表、数组list是否为空
		${! empty list} 或者 ${not empty list} 表示是否不是空
		${+-*/%><>=<===!=} 或者 ${gt lt ge le eq not div mod}
	属性：pageScope < requestScope < sessionScope < applicationScope
	对象：pageContext可以在EL中使用，可以获取其他7个对象（不包括out）：
		${pageContext.servletContext} == <%=application%>
		${pageContext.request} == <%=request%>
		${pageContext.session} == <%=session%>
		${pageContext.page} == <%=page%>
		${pageContext.response} == <%=response%>
		${pageContext.exception} == <%=exception%>
		${pageContext.servletConfig} == <%=servletConfig%>
	说明：
		${pageContext.request.getAttribute("xxx") == requestScope.xxx == xxx}
		EL表达式中，get/set属性使用语法和JS一样
	获取请求参数不能直接使用${参数名}：
		${param.参数名} => 单个值的参数
		${paramValues.参数名} => 数组参数





## JSTL

表达复杂的HTML页面显示逻辑：

使用JSTL结合EL表达式

- 在jsp里面添加一行代码，导包 **standard.jar 和 jstl.jar** 

  ```jsp
  <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
  ```

-  **逻辑标签** 

  1. if判断

     **作用：**进行逻辑判断，相当于Java中的但分支判断

     **注意：**逻辑判断标签需要依赖于EL的逻辑运算，也就是表达式中涉及到的数据必须从作用域中获取

     ```jsp
     <c:if test="${表达式}">
         前端代码
     </c:if>
     ```

  2. switch多分支

     **作用：**用来进行多条件的逻辑判断，类似于Java中的多分支语句

     **注意：**条件成立只会执行一次，都不成立则执行otherwise

     ```jsp
     <c:choose>
         <c:when test="">执行内容</c:when>
         ......
         <c:otherwise>执行内容</c:otherwise>
     </c:choose>
     ```

  3. forEach循环

     作用：循环内容进行处理

     使用：

     begin：声明循环开始位置

     end：声明循环结束位置

     step：设置步长

     varStatus：声明遍历记录每次循环的数据（角标，次数，是否是第一次循环，是否是最后一次循环）

     注意：数据存储在作用域中，需要使用EL表达式获取

     items：声明要遍历的对象。结合EL表达式获取对象

     var：声明变量记录每次循环的结果。存储在作用域中，需要使用EL表达式获取。

     ```jsp
     <c:forEach items="${集合，数组}" var="每次遍历出来的结果" begin="1" end="3" step="2" varStatus="第几次循环">
         循环体
     </c:forEach>
     <c:forEach items="${集合，数组}" var="每次遍历出来的结果">
         循环体
     </c:forEach>
     ```
  
- **基本内容** 

  1. 数据可以为常量也可以是EL表达式
     作用：将数据输出给客户端

  	```jsp
     <c:out value="数据" default="默认值"></c:out>
     ```

  2. 作用：存储数据到作用域对象中

     var：表示存储的键名

     value：表示存储的数据

     scope：表示要存储的作用域对象 page request session application

     ```jsp
   <c:set var="键名" value="数据" scope="page"></c:set>
     ```
     
  3. 作用：删除作用域中的指定键的数据
  
     var：表示要删除的键的名字
  
     scope：表示要删除的作用域（可不写）
  
     注意：如果不指定作用域的情况使用该标签删除数据，会将四个作用域对象中的符合要求的数据全部删除。
  
     ```jsp
     <c:remove var="hello" scope="page"></c:remove>
     ```
  
- **JSTL的格式化标签库**
  常用的格式化标签：
```jsp
  <fmt:formatNumber>		使用指定的格式或精度格式化数字
  <fmt:parseNumber>		解析一个代表着数字，货币或百分比的字符串
  <fmt:parseDate>			解析一个代表着日期或时间的字符串
  <fmt:requestEncoding>	设置request的字符编码
```















## 过滤器（拦截器）

Filter
	1. 继承自HttpFilter
	2. 配置，可以使用Web.xml，也可以使用@WebFilter("拦截配置")
	3. 重写，doFilter()方法，使用chain.doFilter()进行放行，否则拦截
	生命周期：init初始化只运行一次；doFilter符合条件的请求会被拦截并运行；destroy销毁只运行一次
	处理乱码：拦截请求，设置编码：request.setCharacterEncoding("utf-8")
    脏字过滤：包装请求，返回该请求

Wrapper包装请求
	包装类：HttpServletRequestWrapper，也是一个HttpServletRequest请求接口的类型
	包装了一个request请求，可以在请求的基础上做一些包装、修改
	编写自定义Request请求类，继承HttpServletRequestWrapper
	必须编写一个带有request请求参数的构造函数，可以保存该原始请求
	编写需要进行拦截设置的请求方法，比如：getParameter对获取参数的方法进行重写

```java
//配置为拦截所有的拦截器
@WebFilter("/*")
public class OverFilter extends HttpFilter {
    @Override
    protected void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain) throws IOException, ServletException {
        request.setCharacterEncoding("utf-8"); //对于POST参数乱码有效，GET不需要，没有乱码
        //放行
        chain.doFilter(request,response);
    }
}
```





## 监听器

接口：

1.	ServletRequestListener 【监听请求的创建和销毁，比如用于页面访问次数】
 	2.	HttpSessionListener【监听会话的创建和销毁，比如用于当前访问人数】
 	3.	ServletContextListener 【监听ServletContext的创建和销毁，可用于服务器的配置】

```java
//配置为监听器
@WebListener()
public class ContextListener implements ServletContextListener {
    
    //创建事件
    @Override
    public void contextInitialized(ServletContextEvent sce) {
    }

    //销毁事件
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
    }

}
```





## 错误页面配置

```xml
<!--配置错误页面-->
    <error-page>
        <error-code>500</error-code>
        <location>/error/error500.jsp</location>
    </error-page>
```







## jsp模板

```jsp
<%--
  Created by IntelliJ IDEA.
  User: 18890
  Date: 2022/4/13
  Time: 17:14
  To change this template use File | Settings | File Templates.
--%>
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
<html>
<head>
    <title>管理员登录页</title>
    <%--    添加bootstrap.css，和fontawesome-all.css的样式--%>
    <link href="${$}/static/css/bootstrap.css" rel="stylesheet">
    <link href="${$}/static/css/fontawesome-all.css" rel="stylesheet">
</head>
<body>
</body>
<script src="${$}/static/js/bootstrap.bundle.js"></script>
<script src="${$}/static/js/jquery-3.6.0.js"></script>
</html>

```





## 序列化和Ajax，JSON

###### 直接发送请求

```jsp
        //异步发送get请求
        $.get('<%=request.getContextPath()%>/user')
            //请求成功调用
            .done(function (data) {
                //隐藏加载图片
                $('img:eq(0)').css({
                    display: 'none',
                }).after($(data));//把返回的数据插入到页面
                console.log("返回的数据：", data);
            })
            //请求失败调用
            .fail(function (e) {
                //隐藏加载图片
                $('img:eq(0)').css({
                    display: 'none',
                }).after($('<h2>请求失败</h2>'));//插入到页面
                console.log("请求失败：", e);
            })
```

###### 前端处理

```jsp
$.ajax({
            type: "get",// get或者post
            url: "abc.php",// 请求的url地址
            data: {},//请求的参数
            dataType: "json",//json写了jq会帮我们转换成数组或者对象 他已经用JSON.parse弄好了 
            timeout: 3000,//3秒后提示错误
            beforeSend: function () {
                // 发送之前就会进入这个函数
                // return false 这个ajax就停止了不会发 如果没有return false 就会继续
            },
            success: function (data) { // 成功拿到结果放到这个函数 data就是拿到的结果
            },
            error: function () {//失败的函数
            },
            complete: function () {//不管成功还是失败 都会进这个函数
            }
        })
        // 常用
        $.ajax({
            type: "get",
            url: "",
            data: {},
            dataType: "json",
            success: function (data) {
            }
        })

```

###### 封装ajax请求

```jsp
//url为要访问的地址，date为json文件字符串，method为成功后要调用的方法
let myAjax = function (url, data ,method) {
        $.ajax({
            type: "post", //请求方式
            url: '上下文路径', //请求地址
            data: data,//json文件
            dataType: "json",//发送的文件类型
            success: function (resp) {// 请求成功调用的函数resp就是拿到的结果
                console.log("请求成功：" + resp.id)
                //调用添加节点的方法
                method(resp)
            },
            error: function (e) {//请求失败调用的函数e是错误信息
                console.log("请求失败：" + e)
            }
        })
    }
```



###### 后端发送到前端

```java
//设置发送的为json对象
resp.setContentType("application/json;charset=utf-8");
//把json数据返回给浏览器：work为实体类对象
new ObjectMapper().writeValue(resp.getOutputStream(), work);
```

###### 前端获取后端发送的

```jsp
//请求成功调用
.done(function (data) {
console.log("请求成功：", data);
console.log("请求成功：",data.id);
})

```



​	JavaScript Object Notation，一种JS对象格式
​	使用第三方库解析：Jackson和Gson等
​	依赖：jackson-annotations-2.11.3.jar/jackson-core-2.11.3.jar/jackson-databind-2.11.3.jar
​	JavaBean使用@JsonProperty注解需要序列化的属性
​	读写：new ObjectMapper().readValue(new File("students.json"), School.class);
​	使用Servlet发送JSON指定响应类型为：
​		resp.setContentType("application/json;charset=utf-8");
​		发送为普通HTML：text/html
​		发送为普通文本：text/plain


Ajax原始实现（参考即可）：
	创建请求：var ajax = new XMLHttpRequest()
    打开一个请求，传递请求方法和url链接地址：ajax.open("get", "url");
    发起请求，可以发送数据：ajax.send(); // get请求不需要参数，post请求可以发送数据
    通过回调方法获取数据：ajax请求发生变化会调用这个方法：onreadystatechange
    ajax.onreadystatechange = function () { 当请求的状态发生变化的时候这里会被调用 }
    readyState为4说明有结果返回：ajax.readyState === 4
    页面请求成功，可以获取数据：ajax.status === 200、404


Ajax jQuery实现：
	HTML中引入jquery.js链接：<script src="jquery.js"></script>
	发起异步请求：
		$.get() - 发起get请求
		$.post() - 发起post请求
		请求就相当于浏览器浏览地址填写URL回车，只不过这是后台发起、接收
	参数：$.get/post(url [, data ] [, success ] [, dataType ] )
		url-表示请求连接地址
		data-可以省略，表示参数、数据，比如{age:18,sex:"男",score:100}
		success-可以省略，响应成功后的结果回调方法function(response){}
		dataType-不用写，会自动识别，但是也可以手写，比如json/text/html/xml
	done(function(data){// data为返回数据 }) - Ajax请求成功后调用
	fail(function(data){// data为返回数据 }) - Ajax请求失败后调用

__________________________________________________原生实现
window.onload = function () {
    var msg = document.getElementById("msg");
    msg.innerHTML = "发起请求……"; // 刚开始发起请求之前
    // 手动实现一个简单的AJAX
    var ajax = new XMLHttpRequest();
    // 打开一个请求：设置请求，传递请求方法和url链接地址
    ajax.open("get", "ajaxServlet1?sex=男&age=18"); // 相当于表单中的(method, action)
    // 发起请求，可以发送数据
    ajax.send(); // 对于get请求不需要参数，如果是post请求可以发送数据，相当于表单的submit()
    // 通过回调方法获取数据：ajax请求发生变化会调用这个方法：onreadystatechange
    ajax.onreadystatechange = function () {
        // 当请求的状态发生变化的时候这里会被调用
        if (ajax.readyState === 4) {
            // readyState为4说明有结果返回
            if (ajax.status === 200) {
                // 页面请求成功，可以获取数据
                msg.innerHTML = ajax.responseText;
            } else if (ajax.status === 404) {
                msg.innerHTML = "<b>404页面不存在！</b>";
            }
        }
    };
}





## 验证码

###### 1.创建验证码的工具类

```java
package com.util;

import java.awt.*;
import java.awt.image.BufferedImage;
import java.util.HashMap;
import java.util.Map;
import java.util.Random;

public class VerificationCode {
    private static  String[] randomStr = {"0", "1", "2", "3", "4",
            "5", "6", "7", "8", "9", "A", "B",
            "C", "D", "E", "F", "G", "H", "I", "J", "K", "L", "M",
            "N", "O", "P", "Q", "R",
            "S", "T", "U", "V", "W", "X", "Y", "Z"};
    public static Map<String,Object> getVerificationCode(){

        // 定义验证码图片大小
        int width = 200,height = 50;
        // 在内存中创建 图像
        BufferedImage bufferedImage = new BufferedImage(width,height,BufferedImage.TYPE_INT_RGB);
        // 为内存中要创建的图像生成画布，
        Graphics2D graphics2D = bufferedImage.createGraphics();
        // 画一个白色矩形，作为验证码背景
        graphics2D.setColor(Color.LIGHT_GRAY);
        // 填充
        graphics2D.fillRect(0,0,width,height);

        // 画 100 条 灰色的 随机干扰线
        graphics2D.setColor(Color.GRAY);
        Random random = new Random();
        for (int i = 0; i < 100; i++) {
            graphics2D.drawLine(random.nextInt(width),random.nextInt(height),random.nextInt(width),random.nextInt(height));
        }

        // 创建字体
        Font font = new Font("Times New Roman",Font.BOLD , 25);
        graphics2D.setFont(font);

        // 设置默认生成 4个 长度的验证码
        int strLength = 4;
        StringBuffer str = new StringBuffer();
        // 取得 4 位数的 随机字符串
        for (int i = 0; i < strLength; i++) {
            // 返回一个 随机数，在 1 和 20 之间
            String randomNumber = String.valueOf(  randomStr[ random.nextInt(36) ] );
            int red = random.nextInt(255);
            int green = random.nextInt(255);
            int blue = random.nextInt(255);
            //获得一个随机红蓝绿的配合颜色
            graphics2D.setColor(new Color(red, green, blue));
            //把该数字用画笔在画布画出，并指定数字的坐标
            graphics2D.drawString(randomNumber, 50 * i + 6, (height / 2)  + 10);
            //把该数字加到缓存字符串中。用于等会生成验证码字符串set到session中用于校对
            str.append(randomNumber);
        }
        // 清除内存的图片
        bufferedImage.flush();
        // 释放资源
        graphics2D.dispose();

        // 返回结果
        Map<String,Object> code = new HashMap();
        code.put("img",bufferedImage);
        code.put("str",str);
        return code;
    }
}

```

2.创建【单独】响应页面验证码的Servlet

```java
//获取验证码
Map<String,Object> verificationCode = VerificationCode.getVerificationCode();
//获取图片
BufferedImage img = (BufferedImage) verificationCode.get("img");
//获取验证码
String str = verificationCode.get("str").toString();
req.getSession().setAttribute("str" , str);
//设置响应格式
resp.setContentType("image/jpeg");
//获取输入流
ServletOutputStream outputStream = resp.getOutputStream();
//响应图片【img】：图片【"jpeg"】：图片格式【outputStream】输入流
ImageIO.write(img,"jpeg",outputStream);
```

3.配置前端

```jsp
//验证码图片就是Servlet路径
<img src="${$}/code">
//创建点击事件，点击就换验证码 new Date()获取当前时间，保证请求会变
$('.code').click(function () {
        $(this).prev().attr("src","${$}/code?t=" + new Date())
        return false
    })
```





## 注册的时候验证两个密码是否相同

```jsp
//比较两个密码框输入的值是否一样
    let verifyPass = function () {
        if ($('#exampleInputPassword1').val() != $('#exampleInputPassword2').val()) {
            $('.spanPass').show()//显示密码不一致的提示标签
            $('button').addClass('disabled')//禁用按钮
        }
        if ($('#exampleInputPassword1').val() == $('#exampleInputPassword2').val()) {
            $('.spanPass').hide()//隐藏密码不一致的提示标签
            $('button').removeClass('disabled')//取消禁用按钮
        }
    }
    $(function () {
        $("#exampleInputPassword1").bind('input', verifyPass)
        $("#exampleInputPassword2").bind('input', verifyPass)
    })
```



