# 1，Java servlet

`jakarta.servlet.annotation.webServlet  @WebServlet`: **标注此类是一个servelt**

> 1. name：用来指定servlet的名字，等同于web.xml中的<servlet-name>
> 2. urlPatterns (value)：是一个数组，用来指定servlet的映射路径，相当于web.xml中的<url-pattern>
> 3. loadOnStartup：用来指定在服务器启动阶段是否加载该servlet，等同于<load-on-sratup>
> 4. initParams： 用来指定servlet的初始化参数
> 5. 当注解的属性名是value的时候可以省略，

```java
@WebServlet(name = "hello",
        urlPatterns = {"/hello1", "/hello2", "/hello3"},
        //loadOnStartup = 1,
        initParams = {@WebInitParam(name = "username", value = "root"), @WebInitParam(name = "password", value = "123")})
// @WebServlet("/hello")
public class HelloServlet extends HttpServlet { 
```

`jakarta.servlet.annotation.WebFilter `: 标注Filter过滤的路径，代替web.xml文件的配置

```java
import jakarta.servlet.*;
import jakarta.servlet.annotation.WebFilter;

import java.io.IOException;

@WebFilter("*.do")
public class Filter2 implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        System.out.println("Filter2 doFilter begin");
        chain.doFilter(request, response);
        System.out.println("Filter2 doFilter end");
    }

    @Override
    public void destroy() {
    }
}
```

`WebListener`: 在servlet中标注此类是监听器

```java
@WebListener
public class MyServletContextListener implements ServletContextListener {
```

# 2，Java mybatis

`@Param`: **给参数命名，参数命名后就能根据名字得到参数值，正确的将参数传入sql语句中**

```java
/**
 * 使用${}接受参数
 * @param name 要查询的学生姓名
 * @return studentList
 */
List<Student> selectQueryStudent(@Param("studentName") String name);
```

```xml
<select id="selectQueryStudent" resultType="indi.yuluo.domain.Student">
    select id,name,email,age from student where name=${studentName}
</select>
```

`@Select, Insert, Update, Delete`: 写入相应的sql语句

`@Result, @ResultMap`: 对于表的字段名和对象的属性名没有太大相同点并且表中的字段挺多的情况下，应该使用ResultMap做适配

`@MapperScan`: 该注解存在着争议，但不可否认的是这个注解确实是Mybatis的注解，是为了集成Spring而写的注解。该注解主要是扫描某个包目录下的Mapper，将Mapper接口类交给Spring进行管理

# 3， Java mybatis-plus

`@TableName`: 处理数据库的表名和实体类名不一致的情况

```java
@Data
@TableName("t_user")
public class User {
```

`@TableId`: 将属性所对应的字段指定为主键

```java
@Data
@TableName("t_user")
public class User {
    @TableId
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

- `value属性`：作用：用于指定主键的字段

  ```java
  // 数据库中的主键字段为uId时
  // value可以省略
  @TableId(value="uId")
  private Long id;
  ```

- `type属性`：设置主键生成策略 **(自动递增和雪花算法)** 默认为雪花算法、

  ```java
  // 数据库中的主键设置为自动递增，否则无效
  @TableId(value = "uId", type = IdType.AUTO)
  private Long id;
  ```

`@TableField`: 指定属性对应的字段名

```jaav
`@TableField("user_name")`
```

`@TableLogic`: 

- 物理删除：真实删除，将对应数据从数据库中删除，之后查询不到此条被删除的数据
- 逻辑删除：假删除，将对应数据中代表是否被删除字段的状态修改为“被删除状态”。此后在数据库中任然可以看见此条数据记录
- 使用场景：可以进行数据恢复

逻辑删除的实现步骤：

- 在数据库中新建一个字段，默认值设置为0，

- 在User实体类中设置isDeleted属性，加上`@TableLogic`注解

- ```java
  @TableLogic
  private Integer isDeleted;
  ```

`@Mapper`: 扫描dao接口到spring容器中

# 4， Java Spring5

### Spring的基础注解 （Spring2.X）

这一阶段的注解，仅仅是简化xml的配置，不能完全替代xml

#### 对象创建相关注解

`@Component`: 替换原有spring配置文件中的bean标签

> 属性：
>
> - (默认设置方式)id是类名的首单词首字母小写，
>
> - class通过反射获取
>
> ```java
> @Compoment
> public class User() {
> }
> 
> <bean id="user" class="indi.yuluo.annotation.User"/>
>     
> Component id = user    
> ```
>
> - 自己指定工厂创建对象的id值
>
>   `@Component("User")`: 指定User对象的Id值为**User**

`@Component的衍生注解`: 本质上这些衍生注解就是@Component **作用 细节 用法**都是完全一致的

目的是为了更准确的表达一个类型的作用

> 1. @Repository   ---> XXXDao
>
> ```java
> // @Component(可以使用，但是spring更加推荐使用这个注解)
> @Repository
> public class UserDao {
> }
> ```
>
> 1. @Controller
>
> ```java
> @Controller
> public class UserController {
> }
> ```
>
> 1. @Service
>
> ```java
> @Service
> public class UserService {
> }
> ```

**注意：在spring整合mybatis的开发过程中，不使用@Repository @Component注解**

`@Scope`:  控制简单对象的创建次数

> ```java
> @Component
> @Scope("singleton")
> public class Customer {    
> }
> 
> <bean id="" class="indi.yuluo.scope.Customer" scope="singleton|prototype"/>
> ```
>
> 属性值：
>
> - singleton：spring工厂只创建一个简单类型的对象
> - prototype：spring工厂创建多个简单类型的对象

**注意：不添加`@Scope`注解时，spring提供的默认值是 singleton ** 

`@Lazy`: 延迟创建单实例对象, Spring在使用该对象的时候才会被创建

> ```java
> @Component
> @Lazy
> public class Customer {    
> }
> 
> <bean id="" class="" lazy=="false/>
> ```
>
> 

#### 生命周期方法相关注解

`@PostConstruct`: 标注是初始化方法

`@PerDestory`: 标注是销毁方法

```java
@Component
public class Product {
    
    @PostConstruct
    public void myInit() {
        System.out.println("Product.myInit");
    }
    
    @PerDestory
    public void myDestory() {
        System.out.println("Product.myDestory");
    }
    
}

InitializingBean
<bean init-method="" />
DisposableBean    
<bean destory-method="" />    
```

**注意：上述的这两个注解并不是Spring提供的，是JSR（JavaEE规范）520提供的**

#### 注入相关的注解

##### 用户自定义类型注入

```java
@Repository
public class UserDAOImpl implements UserDAO {
    @Overeide
    public void save() {
        System.out.println("UserDAOImpl.save");
    }
}
```

- 基于类型注入`@Autowired`:  用户自定义类型的注入**（ 基于类型进行注入 与目标成员变量类型相同或者是子类，实现类）**

  ```java
  // 基于配置文件的类型注入
  <bean id="userDAO" class="indi.yuluo.dao.UserDAOImpl"/>
      
  <bean id="userService" class="indi.yuluo.service.UserServiceImpl">
  	<property name="userDAO" ref="userDAO"/>
  </bean>
      
  // java程序注入
  @Service    
  public class UserServiceImpl implements UserService {
      
      private UserDAO userDAO;
      
      public void geUserDAO() {
          return userDAO;
      }
      
      @Autowired
      public void setUserDAO() {
          System.out.println("UserServiceImpl.setUserDAO");
          this.userDAO = usrDAO;
      }
      
  }  
  ```

- **(了解即可)**基于名字的注入：注入对象的id值，必须与`@Qualifier`注解中设置的名字相同

  - `@Autowired`和`@Qualifier`注解共同使用

    ```java
    @Service    
    public class UserServiceImpl implements UserService {
        
        private UserDAO userDAO;
        
        public void geUserDAO() {
            return userDAO;
        }
        
        @Autowired
        @Qualifier("userDAOImpl")
        public void setUserDAO() {
            System.out.println("UserServiceImpl.setUserDAO");
            this.userDAO = usrDAO;
        }
        
    }
    ```

- `@Autowired`注解的放置位置

  - 放置在对应成员变量的set方法之上

  - 放置在成员变量上**(通过反射完成)**

    ```java
    @Service    
    public class UserServiceImpl implements UserService {
     
        @Autowired
        private UserDAO userDAO;
        
    }
    ```

- JavaEE规范中类似功能的注解

  - JSR250 `@Resource`: 基于名字进行注入

    ```java
    @Service    
    public class UserServiceImpl implements UserService {
     
        @Resource(name="userDAOImpl")
        private UserDAO userDAO;
        
    }
    ```

  - 如果在应用`@Resource`注解的时候，名字没有配对成功，那么他会继续安装类型进行注入

  - JSR330 `@Inject` 作用与`@Autowired`注解作用完全相同，基于类型进行注入 ---> 在EJB3.0中常用

##### JDK类型注入

`@Value`：

> 1. 不能修饰静态的成员变量，如果应用，注入将失败
> 2. 不能注入集合类型

`@PropertySource`: 替换配置文件中的`<context:property-placeholder location="classpath: xxx.properties">`标签 **(添加在相对应的JavaBean上)**

```xml
1, 设置 xxx.properties
	id = 10
	name = yuluo
2, spring的工厂读取这个文件
	@PropertySources("classapth:xxx.properties")
	<context:property-placeholder location="classpath:xxx.properties"/>
3， 代码
	对应的属性上添加@Value("${key}")注解
```

### Spring的高级注解（Spring3.X以上）

**Spring在3.X中提供新的注解，用于替换XML配置文件**

#### 配置Bean

```java
@Configuration
public class AppConfig {
}
```

> **需要注意的问题：**
>
> 1. `@Configuration`: 替代application.xml
>
> 2. 使用`@Configuration`的同时spring的工厂也发生了变化
>
>    ```java
>    // 通过class的形式指定配置Bean
>    ApplicationContext ctx = new AnnotationConfigApplicationContext(AppConfig.class);
>                            
>    // 通过指定包名的形式指定配置Bean
>    ApplciationContext ctx = new AnnoataionConfigApplicationContext("indi.yuluo");
>    ```
>
> `@Configuration`注解的本质：
>
> - 也是`@Component`注解的衍生注解

`@Bean`: 完成对象的创建

> - 简单对象的创建: 
>
> ```java
> @Configuration
> public class AppConfig() {
>     
>    /**
>    * 简单对象
>    * @return
>    */
>     @Bean
>    	public User user() {
>    		// 书写创建对象的过程
>    		return new User();
> 	}
> }
> ```
>
> - 创建复杂对象(connection, sqlSessionFactory……)：
>
> ```java
> @Configuration
> public class AppConfig() {
>     
>     /**
>     * 创建复杂对象
>     * @return
>     */
>     @Bean
>     public Connection conn() throws Exceprion{
>         
>         Class.forName("com.mysql.cj.jdbc.Driver");
>         Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/test", "root", "123456");
>         
>         return conn;
>     }
>     
> }
> ```
>
> - 属性：
>
>   - 设置自定义id名
>
>     ```java
>     @Bean("id")
>     ```
>
>   - 基于`@Bean`注解控制对象的创建次数
>
>     ```java
>     @Bean
>     @Scope("singleton|prototype") 默认值是 singleton(只创建一次)
>     public Connection conn() {
>     }    
>     ```

`@ComponentScan`: 在配置Bean中进行使用，等同于xml配置文件中的`<context:component-scan base=package="">`标签

> **进行相关注解的扫描**
>
> ```java
> @Configuration
> @ComponentScan("indi.yuluo")
> public class AppConfig{
> }
> ```

### 纯注解版AOP开发

> 开发步骤
>
> 1. 原始对象
>
>    ```java
>    @Service(@component)
>    public class UserServiceImpl implements UserSErvice {
>        
>    }
>    ```
>
> 2. 创建切面类 （额外功能，切入点，组装切面）
>
>    ```java
>    @Aspect
>    @Component
>    public class MyAspect{
>        
>        // 切入点
>        @Pointcut("execution(* indi.yuluo.aop..*.*(..))")
>        public void pointCut(){}
>        
>        // 组装切面
>        @Around("pointCut()")
>        public Object arround(ProceedingJoinPoint joinPoint) throws Throwable {
>            
>            // 额外功能
>            System.out.println("-----aspect log-----");
>            
>            Obejct ret = joinPoint.proceed();
>            
>            return ret;
>        }
>        
>    }
>    ```
>
> 3. Spring的配置文件中
>
>    `<aop:aspect-autoproxy />` （书写在application.xml配置文件中）
>
>    使用`@EnableAspectjAutoProxy`注解替换以上标签 （加入位置在AppConfig类中，因为AppConfig的`@Configuration`替代了application.xml配置文件）
>
>    - 切换jdk和cglib代理方式的方法
>      - cglib: `@EnableAspectjAutoProxy(proxyTargetClass = true)`
>      - 默认为jdk代理方式

### 纯注解版spring和mybatis的整合

> 1. 连接池
>
>    ```java
>    @Bean
>    public DataSource dataSource() {
>        DruidDataSource dataSource = new DriudDataSource();
>        dataSource.setDriverClassName("com,mysql.cj.jdbc.Driver");
>        dataSource.setUrl("jdbc:mysql://localhost:3306/test");
>        dataSource.setUsername("root");
>        dataSource.setPassword("123456");
>        
>        return dataSource;
>    }
>    ```
>
> 2. SqlSessionFactoryBean
>
>    ``` java
>    @Bean
>    public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource) {
>    	SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
>    
>        sqlSessionFactoryBean.setDataSource(dataSources);
>      sqlSessionFactoryBean.setTypeAliasesPackage("indi.yulou.enity");
>        sqlSessionFactoryBean.setMapperLocations(new ClassPathResource("UserDAOMapper.xml"));
>        
>        return sqlSessionFactoryBean;
>        
>    }
>    ```
>
> 3. MapperScannerConfigure  (在配置Bean中书写)
>
>    ```java
>    @MapperScan(basePackages={"indi.yuluo.dao"})
>    ```

# 5， Java SpringMVC

`@RequestMapping`: 设置映射请求路径的具体信息**（默认支持post和get请求）**

> 1. 使用`@RequestMapping`时的位置：
>
>    - 在方法上时：表示映射请求路径的具体信息
>
>      ```java
>      @RequestMapping("/index")
>      public String index() {
>          return "index";
>      }
>      
>      // 表示请求路径是http://localhost:8080/index
>      ```
>
>    - 在类上时：用来标识不同模块中的请求
>
>      ```java
>      @Controller
>      @RequestMapping("/demo")
>      public class TestController {
>                          
>          @RequestMapping("/")
>          public String index() {
>              return "index";
>          }
>      }
>                          
>      // 表示的请求路径是：http://localhost:8080/demo/index
>      ```
>
> 2. 属性：
>
>    - value属性：
>
>      - 通过请求的请求地址匹配请求映射，
>
>      - 如果是一个字符串类型的数组，表示该请求映射能够匹配多个请求地址所对应的请求
>
>      - 必须设置，至少通过请求地址匹配请求映射
>
>      - ```java
>        @RequestMapping(
>                value = {"/testRequestMapping", "/test"}
>        )
>        public String testRequestMapping(){
>            return "success";
>        }
>        ```
>
>      - 
>
>    - method属性：
>
>      - 通过请求的请求方式（get或post）匹配请求映射
>
>      - 如果是一个RequestMethod类型的数组，表示该请求映射能够匹配多种请求方式的请求
>
>      - 若当前请求的请求地址满足请求映射的value属性，但是请求方式不满足method属性，则浏览器报错405：Request method 'POST' not supported
>
>      - ```java
>        @RequestMapping(
>                value = {"/testRequestMapping", "/test"},
>                method = {RequestMethod.GET, RequestMethod.POST}
>        )
>        public String testRequestMapping(){
>            return "success";
>        }
>        ```
>
>      - 
>
>    - params属性
>
>      - 通过请求的请求参数匹配请求映射
>
>      - 如果是一个字符串类型的数组，可以通过四种表达式设置请求参数和请求映射的匹配关系
>
>      - "param"：要求请求映射所匹配的请求必须携带param请求参数
>
>        "!param"：要求请求映射所匹配的请求必须不能携带param请求参数
>
>        "param=value"：要求请求映射所匹配的请求必须携带param请求参数且param=value
>
>        "param!=value"：要求请求映射所匹配的请求必须携带param请求参数但是 param!=value
>
>      - 若当前请求满足@RequestMapping注解的value和method属性，但是不满足params属性，此时页面回报错400：Parameter conditions "username, password!=123456" not met for actual request parameters: username={admin}, password={123456}

`@RequestMapping`的派生注解

> - 处理get请求的映射-->@GetMapping  **（通常在查询的时候使用）**
>
>   ```java
>   @GetMapping("/testGetMapping") // 要求请求方式为GET请求方式
>   public String testGetMapping() {
>    return "target";
>   }
>   
>   <a th:href="@{/hello/testGetMapping}">测试由RequestMapping的派生注解 --> GETMapping注解</a>
>   ```
>
> - 处理post请求的映射-->@PostMapping **（通常用在新增数据的时候）**
>
> - 处理put请求的映射-->@PutMapping **（修改数据的时候使用）**
>
> - 处理delete请求的映射-->@DeleteMapping **（删除数据的时候使用）**

`@RequestParam`: 将请求参数和控制器方法的形参创建映射关系

> 属性：
>
> - value：指定为形参赋值的请求参数的参数名
>
> - defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值为""时，则使用默认值为形参赋值
>
> - required：设置是否必须传输此请求参数，默认值为true
>
>   若设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置defaultValue属性，则页面报错400：Required String parameter 'xxx' is not present；若设置为false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为null

```html
<form th:action="@{/testParam}" method="post">
    用户名：<input type="text" name="user_name"><br>
    密码：<input type="password" name="password"><br>
    爱好：<input type="checkbox" name="hobby" value="a">
         <input type="checkbox" name="hobby" value="b">
         <input type="checkbox" name="hobby" value="c"><br>
    <input type="submit" value="测试使用控制器的形参获取请求参数">
</form>
```

```java
/**
 * springMVC的传值方式，通过方法的形参传递参数
 * @RequestParam注解的作用处理前端传值的变量名和后端形参名不对应的问题
 * @return
 */
@RequestMapping("/testParam")
public String testParam(
        @RequestParam("user_name") String username,
        String password,
        String hobby) {

    System.out.println("username: " + username + " password: " + password + " 爱好：" + hobby);

    return "success";
}
```

`@RequestHeader`:  将请求头信息和控制器方法的形参创建映射关系

> 属性：用法同@RequestParam
>
> - value、
> - required、
> - defaultValue，
>
> ```java
> /**
>  * springMVC的传值方式，通过方法的形参传递参数
>  * @RequestParam注解的作用处理前端传值的变量名和后端形参名不对应的问题
>  * @return
>  */
> @RequestMapping("/testParam")
> public String testParam(
>         @RequestParam("user_name") String username,
>         String password,
>         String hobby,
>         @RequestHeader(value="Host", required=true, defaultValue="haha") String host) {
> 
>     System.out.println("username: " + username + " password: " + password + " 爱好：" + hobby);
>     System.out.println("host: " + host);
>     
>     
>     return "success";
> }
> ```

`@CookieValue`:  将cookie数据和控制器方法的形参创建映射关系

> 属性：用法同@RequestParam
>
> - value、
> - required、
> - defaultValue，
>
> ```java
> @RequestMapping("/testParam")
> public String testParam(
>         @RequestParam("user_name") String username,
>         String password,
>         String hobby,
>         @RequestHeader(value="Host", required=true, defaultValue="haha") String host,
>         @CookieValue("HSESSIONID") String JSESSIONID) {
> 
>     System.out.println("username: " + username + " password: " + password + " 爱好：" + hobby);
>     System.out.println("host: " + host);
>     System.out.println("cookie: " + JSESSIONID);
> 
> 
>     return "success";
> }
> ```

将java对象转换为响应报文: 

`@RequestBody`: @RequestBody可以获取请求体，需要在控制器方法设置一个形参，使用@RequestBody进行标识，当前请求的请求体就会为当前注解所标识的形参赋值

`@ResponseBody`: 用于标识一个控制器方法，可以将该方法的返回值直接作为响应报文的响应体响应到浏览器

```java
@RequestMapping("/testResponseBody")
@ResponseBody
public String testResponseBody(){
    return "success";
}
```

结果：浏览器页面显示success

`*@RestController`: 注解是springMVC提供的一个复合注解，标识在控制器的类上，就相当于为类添加了@Controller注解，并且为其中的每个方法添加了@ResponseBody注解

`@EcxeptionHandler`: 用于设置所标识方法处理的异常

```java
 @ExceptionHandler(ArithmeticException.class)
    //ex表示当前请求处理中出现的异常对象
    public String handleArithmeticException(Exception ex, Model model){
        model.addAttribute("ex", ex);
        return "error";
    }
```

`@EnableWebMvc`: 开启mvc的注解驱动，用在配置bean处理springMVC配置文件中

# 6， Java Spring Boot

1 `@SpringbootApplication`：开启springboot的配置

2 `@ConfigurationProperties(prefix = "abc")  `：读取properties配置文件并映射到对应得实体类

要设置相应得属性前缀

```properties
abc.name=yuluo
abc.website=http://www.yuluo.com
```

```java
@Component  // 将此类交给spring容器进行管理
@ConfigurationProperties(prefix = "abc")  // 读取properties配置文件
public class School {
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    
    public String getWebsite() {
        return website;
    }
    public void setWebsite(String website) {
        this.website = website;
    }
    private String name;
    private String website;
}
```

lombok注解：

> ```
> @Data  : 生成toStirng和getter和setter方法
> @AllArgsConstructor  带所有参数的有参构造方法
> @NoArgsConstructor 不带任何参数得无参构造方法
> ```

springboot和dubbo的整合配置：

> 1. 接口工程：存放实体bean和业务接口
> 2. 服务提供者：要我去接口的实现类并将服务暴露且注册到注册中心，调用数据持久层
>    1. 添加依赖 （dubbo，接口工程，注册中心）
>    2. 配置服务提供者核心配置文件
> 3. 服务消费者：处理浏览器客户端发送的请求，从注册中心调用服务提供者所提供的服务
>    1. 添加依赖（dubbo，注册中心，接口工程）
>    2. 配置服务消费者核心配置文件

**注意这里使用的是dubbo的注解，不是spring的注解  要暴露的类名, 取代之前写的dubbo配置**
**dubbo:service interface="" version="" timeout=""**

***注解使用在service的实现类上***

```java
@Service(interfaceClass = StudentService.class, version="1.0.0", timeout=15000)
```

**使用在controller层上的对服务层的注入中**

**dubbo:reference interface="" version="" check=false  代替dubbo的配置**

```java
@Reference(interfaceClass = StudentService.class, version="1.0.0", check=false)
private StudentService studentService;
```

**在服务提供者的工程中配置：**

```properties
# 设置dubbo的配置 和dubbo的application配置相同
spring.application.name=springboot-006-dubbo-provider
# 当前工程是一个服务提供者
spring.dubbo.server=true
# 设置注册中心
spring.dubbo.registry=zookeeper://192.168.174.1:2181
```

**在服务消费者的工程中配置：**

```properties
# 设置dubbo的配置
spring.application.name=springboot-007-dubbo-consumer
# 指定注册中心
spring.dubbo.registry=zookeeper://192.168.174.1:2181
```

`@Configuration`：声明当前类是一个配置类

# 7、 dubbo常用配置

在java web项目中的配置

## 服务提供者

web.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--加载配置文件位置-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:dubbo-zk-userservice-provider.xml</param-value>
    </context-param>

    <!--监听器-->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

</web-app>
```

resources里面的配置文件中

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!--声明dubbo服务提供者的名称：保证唯一性-->
    <dubbo:application name="007-zk-userservice-provider"/>

    <!--声明dubbo使用的协议名称和端口号-->
    <dubbo:protocol name="dubbo" port="20880"/>

    <!--现在要使用zookeeper注册中心-->
    <!--指定注册中心地址和端口号-->
    <dubbo:registry address="zookeeper://localhost:2181"/>

    <!--暴露服务接口-->
    <dubbo:service
            interface="indi.yuluo.dubbo.service.UserService"
            ref="userServiceImpl"
            registry=""/>

    <!--加载接口的实现类-->
    <bean id="userServiceImpl" class="indi.yuluo.dubbo.service.impl.UserServiceImpl"/>

</beans>
```

## 消费者中

web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:applicationContext.xml, classpath:dubbo-zk-consumer.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

resources

applicationContext.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--组件扫描器-->
    <context:component-scan base-package="indi.yuluo.dubbo.controller"/>
    
    <!--注解驱动器-->
    <mvc:annotation-driven/>
    
    <!--模板解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/templates/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    
</beans>
```

dubbo-consumer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:dubbo="http://dubbo.apache.org/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://dubbo.apache.org/schema/dubbo http://dubbo.apache.org/schema/dubbo/dubbo.xsd">

    <!--声明dubbo服务消费者名称：保证唯一性-->
    <dubbo:application name="008-zk-consumer"/>

    <!--指定注册中心-->
    <dubbo:registry address="zookeeper://localhost:2181"/>

    <!--引用远程接口-->
    <dubbo:reference id="userService" interface="indi.yuluo.dubbo.service.UserService"/>

</beans>
```

