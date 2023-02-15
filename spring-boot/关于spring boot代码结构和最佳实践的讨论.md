# 关于spring boot代码结构和最佳实践的讨论

## 代码结构

在java的web开发中，使用了`spring mvc`框架之后，我们的代码组织形式变成了controller，service，dao这种形式。具体分析一下这三层的主要负责的功能：

****************

**controller：**

在GRASP中有关控制器部分的描述是：https://en.wikipedia.org/wiki/GRASP_(object-oriented_design)#Controller

> Controller
>
> The **controller** pattern assigns the responsibility of dealing with system events to a non-[UI](https://en.wikipedia.org/wiki/User_Interface) class that represents the overall system or a [use case](https://en.wikipedia.org/wiki/Use_case) scenario. A controller object is a non-user interface object responsible for receiving or handling a system event.
>
> Problem: Who should be responsible for handling an input system event?
> Solution: A use case controller should be used to deal with *all* system events of a use case, and may be used for more than one use case. For instance, for the use cases *Create User* and *Delete User*, one can have a single class called *UserController*, instead of two separate use case controllers.
>
> The controller is defined as the first object beyond the UI layer that receives and coordinates ("controls") a system operation. The controller should delegate the work that needs to be done to other objects; it coordinates or controls the activity. It should not do much work itself. The GRASP Controller can be thought of as being a part of the application/service layer[[4\]](https://en.wikipedia.org/wiki/GRASP_(object-oriented_design)#cite_note-Yahoo-4) (assuming that the application has made an explicit distinction between the application/service layer and the [domain layer](https://en.wikipedia.org/wiki/Domain_layer)) in an object-oriented system with common layers in an information system logical architecture.
>
> **Related Pattern or Principle**: [Command](https://en.wikipedia.org/wiki/Command_pattern), [Facade](https://en.wikipedia.org/wiki/Facade_pattern), [Layers](https://en.wikipedia.org/wiki/Layer_(object-oriented_design)), Pure Fabrication

大致总结是：

> - 接收业务请求，并将请求转发至业务处理对象
> - 接收业务请求处理结果，并将结果分发到响应页面

在业务开发中希望控制器作为协调和委派的角色，不执行实际的业务逻辑，调用业务层去完成。

1. 处理http请求（前端传递过来的参数，具体的请求，返回视图对象和数据等）
2. 委托业务层去处理业务
3. 设计过程可以将流程进行抽象归纳，设计出可以重复利用的子单元流程模块

主要做法是：

> - 控制器应该是无状态的！默认情况下，控制器是单例，并且任何状态都可能导致大量问题；
> - 控制器不应该执行业务逻辑，而是依赖委托；
> - 控制器应该处理应用程序的HTTP层，这不应该传递给服务；
> - 控制器应该围绕用例/业务能力来设计。

**想深入了解，参照REST ful的最佳实践**

*******

**service：**

Controller层与持久层都是依赖于业务层的，业务层负责逻辑业务处理（数据处理）。

servic层的设计：

> 1. 围绕业务功能构建
> 2. 合理的使用单一职责原则
> 3. 可以决定使用Controler和Service之间的一对一映射，那是理想的情况。但这并不意味着，Service之间不能互相调用！

**DAO层(mapper)**：

**DAO(Data Access Objects)**设计模式是属于J2EE体系[架构](http://lib.csdn.net/base/architecture)中的数据层的操作。

如何更好设计spring boot中与数据交互的操作？

> 答案是：使数据库独立于核心业务逻辑之外

数据库逻辑于服务分离出来。理想情况下，你不希望服务知道它正在与哪个数据库通信，这需要一些抽象来封装对象的持久性(对象持久性是指对象继续存在的知识，即使它不再以任何其他方式被看见，听到或感知)

> 罗伯特C.马丁在`Clear Architecture`强烈地说明，你的数据库是一个“细节”，这意味着不将你的应用程序与特定数据库耦合。过去很少有人会切换数据库，我注意到，使用Spring Boot和现代微服务开发会让事情变得更快。

**一些优秀的持久层框架可以帮助我们做到这些！**

********

其他模块解释：

POJO：

POJO = plain ordinary Java object = 普通Java对象

严格来说：以下几种都属于POJO类

> **enity：**entity层用于定义实体，定义各个属性以及各个属性的getter()和setter()方法，与数据库中的属性值基本保持一致。
>
> **domain：**域是一个大范围，如简历域包括工作经验表、项目经验表、简历基本信息表。 在domain包中，就可以定义一个大的简历对象，将三个表的内容整合在一个对象中，作为整体操作。
>
> **model（模型）**：当用model当包名时，一般里面存的是实体类的模型，是用来给后端用的。比如user表中有name、id、age，出于安全原因，我们需要把用户的密码定义在另一表中，即user_passwd表，但进行相关操作时，我们往往需要将两个表关联使用，每次定义都很麻烦。因此可以在model层中定义user_model类，将user表中的信息与user_passwd表中的信息整合成一张综合表，这样在进行操作时只需调用综合表，就可以完成对两个表的关联操作
>
> view(视图): 当用view当包名时，一般里面存放的是对实体表的映射类(视图类)，是用来给前端用的。即：有时候我们仅仅需要获得某一个表的几个字段，所以此时可以用view存储这几个字段。
>
> **DTO：**DTO = Data Transfer Object = 数据传输对象，与view的用法相同，不过是叫法不同

注意：**在各个实体类中实现序列化接口，避免在微服务中出现错误**

util：

> 存放工具类的地方，对于一些独立性很高的小功能，或重复性很高的代码片段，可以提取出来放到Util层中。

**VO**：

 >业务层之间数据传递的对象

**PO**：

> 持久对象，与数据库中的表相映射的Java对象

**common:**

> 通用工具包，后端返回给前端的数据封装对象等

**config：**

> 存放配置相关类，@Configuration 注解的类都建议放这里，包括但不限于 Redis 配置类，RestTemplate 配置类，Swagger 配置类等。

## spring boot的最佳实践讨论

本篇文章借鉴于此https://mp.weixin.qq.com/s/KpUVxMFjFtzMRoCOb-BHVg

### 1、使用自定义BOM来维护第三方依赖

Spring Boot项目本身使用和集成了大量的开源项目，它帮助我们维护了这些第三方依赖。但是也有一部分在实际项目使用中并没有包括进来，这就需要我们在项目中自己维护版本。如果在一个大型的项目中，包括了很多未开发模块，那么维护起来就非常的繁琐。

怎么办呢？事实上，Spring IO Platform就是做的这个事情，它本身就是Spring Boot的子项目，同时维护了其他第三方开源库。我们可以借鉴Spring IO Platform来编写自己的基础项目platform-bom，所有的业务模块项目应该以BOM的方式引入。这样在升级第三方依赖时，就只需要升级这一个依赖的版本而已。

```xml
<dependencyManagement>
   <dependencies>
       <dependency>
           <groupId>io.spring.platform</groupId>
           <artifactId>platform-bom</artifactId>
           <version>Cairo-SR3</version>
           <type>pom</type>
           <scope>import</scope>
       </dependency>
   </dependencies>
</dependencyManagement>
```

上述是文章中的描述：

```markdo
在平时的练习项目中，为了比避免在pom.xml文件中重复导包的问题，
可以自己创建一个父工程，将所有的依赖版本统一，避免出现jar包冲突和每个项目都要导包的问题。在创建项目时只需要继承父工程即可。
类似于spring boot的父工程
```

springboot中父工程依赖的引入

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.6.7</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

## 在业务开发提供全局处理异常

Spring Boot提供了两种主要方法：

> - 你应该使用HandlerExceptionResolver定义全局异常处理策略；
> - 你也可以在控制器上添加@ExceptionHandler注解，这在某些特定场景下使用可能会很有用。

这与Spring中的几乎相同，并且Baeldung有一篇关于REST与Spring的错误处理的详细文章，非常值得一读。

参考：

https://www.baeldung.com/exception-handling-for-rest-with-spring

https://cloud.google.com/apis/design/errors (谷歌异常处理模型)

## 使用日志框架

应该使用Logger进行日志记录，而不是使用System.out.println()手动执行。这很容易在Spring Boot中完成，几乎没有配置。只需获取该类的记录器实例：

`Logger logger = LoggerFactory.getLogger(MyClass.class);`

lombok的@Slf4j等

## 正确设计代码目录结构

尽管允许你有很大的自由，但是有一些基本规则值得遵守来设计你的源代码结构。

避免使用默认包。确保所有内容（包括你的入口点）都位于一个名称很好的包中，这样就可以避免与装配和组件扫描相关的意外情况；

将Application.java（应用的入口类）保留在顶级源代码目录中。

参照**阿里巴巴开发手册**

```markdown
├── main
│   ├── java
│   │   └── com
│   │       └── bestpractice
│   │           ├── config
│   │           ├── constants
│   │           ├── controller
│   │           ├── dao
│   │           ├── exception
│   │           ├── filter
│   │           ├── manager
│   │           ├── model
│   │           │   ├── bo
│   │           │   ├── dto
│   │           │   └── vo
│   │           ├── service
│   │           │   ├── impl
│   │           ├── task
│   │           ├── utils
│   │           └── BestPracticeApplication.java
│   │
│   └── resources
```

## 代码测试

这不是Spring Boot特有的，但它需要提醒——测试你的代码！如果你没有编写测试，那么你将从一开始就编写遗留代码。

******

使用Spring Boot测试代码可能很棘手——你需要初始化数据层，连接大量服务，模拟事物……实际上并不是那么难！答案是使用测试切片。

使用测试切片，你可以根据需要仅连接部分应用程序。这可以为你节省大量时间，并确保你的测试不会与未使用的内容相关联。来自spring.io的一篇名为Custom test slice with Spring test 1.4的博客文章解释了这种技术。https://spring.io/blog/2016/08/30/custom-test-slice-with-spring-boot-1-4

## 合理使用Swagger，YApi等接口工具

在大多数情况下，其他应用程序将通过REST API调用你的应用程序。因此我们需要小心的维护一份API文档。文档应该由代码生成。当然有一些工具可以做到这一点。其中最受欢迎的是Swagger。你可以使用SpringFox项目轻松地将Swagger 2集成到你的Spring Boot应用程序中。为了使用Swagger，我们需要添加如下依赖。第一个库负责从Spring MVC控制器代码中生成Swagger descriptor，而第二个库负责解析Swagger descriptor并在你的浏览器中展示页面。

## 后续会继续补充

