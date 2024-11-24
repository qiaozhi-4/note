### 项目结构(大)


```xml
my-project (父项目)
│
├── my-project-common
│   └── src
│       ├── main
│       │   ├── java
│       │   └── resources
│
├── my-project-service
│   └── src
│       ├── main
│       │   ├── java
│       │   └── resources
│
├── my-project-repository
│   └── src
│       ├── main
│       │   ├── java
│       │   └── resources
│
├── my-project-api
│   └── src
│       ├── main
│       │   ├── java
│       │   └── resources
│
├── pom.xml (父项目的POM)

```

### 项目结构(小)

```xml
my-project/                          # 父项目目录
│
├── my-project-core/                 # 核心模块，包含共享实体、枚举、异常等
│   ├── src/
│   │   └── main/
│   │       ├── java/
│   │       │   └── com/
│   │       │       └── example/
│   │       │           └── myproject/
│   │       │               └── core/
│   │       │                   ├── entity/
│   │       │                   ├── exception/
│   │       │                   └── util/
│   │       └── resources/
│   └── pom.xml                      # core模块的Maven配置文件
│
├── my-project-dao/                  # 数据访问模块，包含Repository接口
│   ├── src/
│   │   └── main/
│   │       ├── java/
│   │       │   └── com/
│   │       │       └── example/
│   │       │           └── myproject/
│   │       │               └── dao/
│   │       └── resources/
│   │           └── mapper/          # MyBatis的XML映射文件（如果使用MyBatis）
│   └── pom.xml                      # dao模块的Maven配置文件
│
├── my-project-service/              # 服务模块，包含业务逻辑
│   ├── src/
│   │   └── main/
│   │       ├── java/
│   │       │   └── com/
│   │       │       └── example/
│   │       │           └── myproject/
│   │       │               └── service/
│   │       └── resources/
│   └── pom.xml                      # service模块的Maven配置文件
│
├── my-project-web/                  # Web模块，包含Spring MVC控制器
│   ├── src/
│   │   └── main/
│   │       ├── java/
│   │       │   └── com/
│   │       │       └── example/
│   │       │           └── myproject/
│   │       │               └── web/
│   │       │                   └── controller/
│   │       └── resources/
│   │           ├── static/          # 存放静态资源，如HTML、CSS、JavaScript
│   │           ├── templates/      # 存放模板文件，如Thymeleaf模板
│   │           └── application.properties # Spring Boot配置文件
│   └── pom.xml                      # web模块的Maven配置文件
│
├── my-project-configuration/        # 配置模块，包含全局配置类
│   ├── src/
│   │   └── main/
│   │       ├── java/
│   │       │   └── com/
│   │       │       └── example/
│   │       │           └── myproject/
│   │       │               └── configuration/
│   │       └── resources/
│   └── pom.xml                      # configuration模块的Maven配置文件
│
└── pom.xml                          # 父项目的Maven配置文件

```

### 父POM文件配置

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>my-project</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <modules>
        <module>my-project-common</module>
        <module>my-project-service</module>
        <module>my-project-repository</module>
        <module>my-project-api</module>
    </modules>

    <properties>
        <spring.boot.version>2.7.0</spring.boot.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring.boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>

```

### 子模块POM文件配置


```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.example</groupId>
        <artifactId>my-project</artifactId>
        <version>1.0-SNAPSHOT</version>
    </parent>

    <artifactId>my-project-api</artifactId>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <!-- 其他依赖 -->
    </dependencies>
</project>

```





### core模块 (`my-project-core`)

**职责**：提供整个应用共享的核心功能，如实体类、工具类、常量、异常定义等。

**包含内容**：

- **实体类** (`Entity`): 定义应用的数据模型和数据库映射。
- **枚举和常量** (`Enum`, `Constants`): 应用共享的常量值和枚举类型。
- **异常类** (`Exception`): 自定义异常，用于整个应用。
- **工具类** (`Utils`): 提供静态工具方法，如日期处理、加密解密等。

### service模块 (`my-project-service`)

**职责**：包含业务逻辑和服务接口定义，处理应用的核心业务流程。

**包含内容**：

- **服务接口** (`Service`): 定义业务逻辑的接口。
- **服务实现** (`ServiceImpl`): 实现上述接口的具体业务逻辑。

### dao模块 (`my-project-dao`)

**职责**：数据访问层，包含与数据库交互的逻辑。

**包含内容**：

- **数据访问接口** (`Repository`): 使用Spring Data JPA的接口，定义数据访问和查询方法。
- **数据传输对象** (`DTO`): 用于层与层之间数据交换的对象。

###  **`web`** 模块 (`my-project-web`)

**职责**：表现层，处理外部请求和响应。

**包含内容**：

- **控制器** (`Controller`): 定义处理HTTP请求的端点。
- **请求/响应对象** (`Request/Response`): 定义API接收和返回的数据结构。

### configuration模块 (`my-project-configuration`)

**职责**：提供应用的配置类，如安全配置、Swagger配置等。

**包含内容**：

- **安全配置** (`SecurityConfig`): 配置应用的安全策略，如用户认证和授权。
- **Swagger配置** (`SwaggerConfig`): 配置API文档的生成和展示。
- **其他配置** (`*Config`): 如数据库配置、消息队列配置等。

### 总结

每个模块都应该是自包含的，只与需要的其他模块交互。例如，`service`模块可能依赖于`core`和`dao`模块，而`api`模块依赖于`service`模块。这样的结构使得项目清晰、模块间耦合低，便于管理和维护。

在项目的`pom.xml`文件中，你需要为每个模块添加相应的依赖关系，并可能需要在父`pom.xml`中使用``标签列出所有模块，以及在``中管理共享依赖的版本。这样的项目结构有利于大型项目的开发，使得不同的团队可以在不同的模块上协作，而不会互相干扰。









### 临时

在Spring Security中，`AccessDecisionManager`的`decide`方法是用来决定当前用户是否有权限访问某资源的核心方法。它的三个参数分别是：

### 1. `Authentication`对象

- **作用**：代表当前正在尝试访问受保护资源的用户的身份信息。
- **内容**：包含了用户的认证信息，如用户的权限（Granted Authority）、用户名、凭证（如密码，通常不包括明文密码）、是否已认证等。
- **用途**：在`decide`方法中，你可以使用这个对象来获取当前用户的权限信息，并基于这些信息来判断用户是否有权访问请求的资源。

### 2. `Object`对象

- **作用**：代表客户端正在请求的受保护资源。
- **内容**：在Web安全环境下，这个对象通常是一个`FilterInvocation`实例，它包含了当前请求的信息，如请求URL、HTTP方法等。
- **用途**：通过这个对象，你可以获取到请求的细节，如请求的路径和方法。这些信息通常用于根据请求路径或者请求类型来决定访问权限。

### 3. `Collection`对象

- **作用**：包含了与当前请求的资源相关联的安全配置属性。
- **内容**：这个集合包含了访问当前请求资源所需的权限信息。这些权限信息是通过安全元数据源（如`FilterInvocationSecurityMetadataSource`）提供的。
- **用途**：`decide`方法可以使用这些属性来决定当前用户是否满足访问资源的要求。例如，可以检查用户是否拥有集合中指定的任一权限。

### 总结

- `Authentication`参数让你了解请求者是谁，以及他们拥有的权限。
- `Object`参数（通常是`FilterInvocation`）让你了解请求的是什么，包括请求的URL和HTTP方法。
- `Collection`参数让你了解访问当前请求的资源需要哪些权限。

在`decide`方法的实现中，你需要使用这三个参数来编写逻辑，判断当前用户是否有权访问正在请求的资源。如果用户没有足够的权限，你应该抛出一个`AccessDeniedException`异常。这个方法的实现通常涉及检查`Authentication`对象中的权限（如角色或权限标识符），并将它们与`Collection`中定义的所需权限进行对比。







```
{
  "code": 200,
  "message": "ok",
  "data": {
    "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJqYWNvYi11c2VyIiwiaWF0IjoxNzEzMDYxODA5LCJleHAiOjE3MTMxNDgyMDksImlkIjoiMSIsImFjY291bnQiOiJhZG1pbiJ9.DaCnrSEhQKh91ZYvkKNRL-pz9B_XbRldtDpkp3sTAMI"
  }
}
```