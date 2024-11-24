# 依赖

```xml
<!--shiro相关依赖-->
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-core</artifactId>
    <version>1.9.0</version>
</dependency>
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-spring</artifactId>
    <version>1.9.0</version>
</dependency>
<dependency>
    <groupId>org.apache.shiro</groupId>
    <artifactId>shiro-ehcache</artifactId>
    <version>1.9.0</version>
</dependency>
```



















# web.xml配置

```xml
<!--如果想要拦截webapp下的资源，必须在这里写Filter-->
<!--配置Shiro的过滤器：实际上不是真正的过滤器，而是过滤器代理-->
<!--配置shiro拦截过滤器-->
<filter>
    <!--这里的名字很有讲究：不能随意修改【可以修改，但是需要额外配置】-->
    <!--这里的shiroFilter也就是被代理的bean的名字id-->
    <filter-name>shiroFilter</filter-name>
    <filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>

    <!--配置targetFilterLifecycle属性为true，表示全权代理过滤器的生命周期，无需手动处理生命周期-->
    <init-param>
        <param-name>targetFilterLifecycle</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>shiroFilter</filter-name>

    <!--所有都过滤-->
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

















# 核心类Realm

使用主体登录时会进入这个方法【subject.login(token);】

```java
@RequiredArgsConstructor
public class MyRealm extends AuthorizingRealm {

    private final IUserService userService;

    //授权信息
    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        //来到这里条件：1、访问了带有权限要求的页面；2、已经通过登录认证的用户
        // 获取用户的首要信息【自定义的】
        User principal = (User) principalCollection.getPrimaryPrincipal();
        // 权限信息对象
        SimpleAuthorizationInfo info = new SimpleAuthorizationInfo();
        //根据首要信息获取对应角色，权限信息
        if (principal.getUsername().equals("admin")){
            //授权角色
            info.addRole("admin");//添加角色
            /*
            * 授权实例：根据实际业务情况进行设计
            * 可以是url，比如"/delete"
            * 可以是名字，比如"查询"*/
//            info.addStringPermission("delete");//添加权限
        }else {
            //授权角色
            info.addRole("user");//添加角色
        }
        return info;
    }

    //认证信息
    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken t) throws AuthenticationException {
        //获取用户的令牌
        UsernamePasswordToken token = (UsernamePasswordToken) t;
        //获取用户令牌的信息
        String username = token.getUsername();

        //根据用户名查询用户是否存在
        User user = userService.getOne(new QueryWrapper<User>().eq("username", username));
        //没有查询到抛出用户不存在的异常
        if (user == null){
            throw new UnknownAccountException();
        }
        //金额低于0抛出用户被冻结的异常
        if (user.getMoney() < 0){
            throw new LockedAccountException();
        }

        //返回的主体信息
        Object principal = user;
        //需要验证的证书（数据库查出来的：密码）
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

















# spring.xml配置

认证、权限过滤器：
		anon - 任意访问
		authc - 需要认证才能访问
		logout - 登出
		user - 记住我或者登录的用户
	URL匹配规则：
		? - 匹配一个字符

		* - 一个或者多个字符
		** - 一个或者多个字符、路径
		匹配规则：自上而下，第一次优先匹配，后面的规则被覆盖

```xml

<!--配置Shiro过滤器和规则-->
<!--创建realm：自定义realm对象-->
<bean id="myRealm" class="com.xh.shiro.MyRealm">
    <!--添加加密算法-->
    <property name="credentialsMatcher">
        <!--使用散列算法-->
        <bean class="org.apache.shiro.authc.credential.HashedCredentialsMatcher">
            <!--算法名称-->
            <property name="hashAlgorithmName" value="MD5"/>
            <!--迭代次数-->
            <property name="hashIterations" value="2"/>
        </bean>
    </property>
</bean>
<!--配置安全管理器-->
<bean id="securityManager" class="org.apache.shiro.web.mgt.DefaultWebSecurityManager">
    <!--realm相当于验证核心,实现了Realm接口即可-->
    <property name="realm" ref="myRealm"/>
</bean>
<!--id必须为web.xml里面的filter名字-->
<bean id="shiroFilter" class="org.apache.shiro.spring.web.ShiroFilterFactoryBean">
    <!--配置安全管理器-->
    <property name="securityManager" ref="securityManager"/>
    <!--登录的url配置-->
    <property name="loginUrl" value="/index.jsp"/>
    <!--登录成功的URL-->
    <property name="successUrl" value="/success"/>
    <!--没有授权跳转的页面-->
    <property name="unauthorizedUrl" value="/403"/>
    <!--认证规则的过滤-->
    <property name="filterChainDefinitions">
        <!--过滤规则是从上而下进行，如果前面的匹配，后面的就不会再继续了
        anon-过滤器处理原则 ：随便访问
        authc-需要进行权限认证
        logout-登出路径
        user-认证或者记住我
        roles[admin]-角色要求
        ?/*/** -常用通配符-->
        <value>
            <!--anon：无需登录；authc：需要登录-->
            /index.jsp = anon
            /register = anon
            /page.jsp = authc
            /users =  roles[admin],roles[user]
            /delete = authc
            /update = authc
            /static/** = authc
            <!--配置为登出路径-->
            /logout = logout
            <!--这里页面必须有admin角色（身份）-->
            /admin/** = roles[admin]
        </value>
    </property>
</bean>
<!--配置生命周期管理bean对象-->
<bean id="lifecycleBeanPostProcessor" class="org.apache.shiro.spring.LifecycleBeanPostProcessor"/>
```













# 使用方法

```java
@RequestMapping(value = "/login", method = RequestMethod.POST)
public String login(String username, String password, Model model) {
    //获取主体，任意地方都能获取
    Subject subject = SecurityUtils.getSubject();
    //判读是否已经验证登录，或者记住登录
    if (subject.isAuthenticated() || subject.isRemembered()) {
        //已经登录
        return "redirect:success";
    }
    //还没登录
    try {
        //传入需要验证的用户，以及令牌（密码）
        UsernamePasswordToken token = new UsernamePasswordToken(username,password);
        //验证登录，会进入到核心类Realm验证
        //验证成功继续运行，失败则抛出下面的异常
        subject.login(token);
        //获取Realm里面设置的主体
        User user = (User) subject.getPrincipal();
        //把主体设置到当前会话里面
        subject.getSession().setAttribute("user",user);
        //转发到登录成功
        return "redirect:success";

        //验证失败就抛出不同的异常
    } catch (UnknownAccountException e) {
        model.addAttribute("error", "无此用户");
    } catch (IncorrectCredentialsException e) {
        model.addAttribute("error", "密码不正确");
    } catch (LockedAccountException e) {
        model.addAttribute("error", "用户已经被锁定");
    } catch (ExcessiveAttemptsException e) {
        model.addAttribute("error", "尝试次数过多");
    } catch (AuthenticationException e) {
        model.addAttribute("error", "认证错误");
    }
    //转发到登录失败页面
    return "redirect:fail";
}

```









