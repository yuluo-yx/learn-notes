# Spring  boot笔记 (demo建立的顺序)

## 创建springboot的web项目

## 使用springboot框架继承springmvc

## 使用springboot框架的核心配置文件 application.properties

## 使用springboot框架的核心配置文件 application.properties或者application.yml / yaml

## springboot框架的核心配置文件同时存在时有优先级区别，properties最高，yml/yaml次之

## 多环境下核心配置文件的使用，工作中的开发环境

> 核心配置文件 **application.properties**
> 		激活使用的配置文件 (application-后面的单词)
> 		`spring.profiles.active=dev`

> 开发环境  **application-dev.properties**
> `server.port=8080`
> `server.servlet.context-path=/dev`

> 测试环境  **application-test.properties**
> `server.port=8081`
> `server.servlet.context-path=/test`

> 准生产环境 **application-ready.properties**
> `server.port=8082`
> `server.servlet.context-path=/ready`

> 生产环境 ** application-product.properties**
> `server.port=8083`
> `server.servlet.context-path=/product`

1. 使用yml文件也是这样配置

   ```yml
   server:
     port: 8080
     servlet:
         context-path: dev
   ```

   

2. 在主配置文件中使用

     ```yml
     spring: 
         profiles: 
             active: dev
     ```

## springboot自定义配置一个值通过@Value注解获取

### 在application.properties中自定义属性值

```java
yuluo=123456
    
 需要使用@Value注解的方式取出来
    
@Value("${yuluo}")
    
会自己赋值给
private String yuluo;
```

### springboot在核心配置文件将自定义配置映射到一个对象
```java
使用@ConfiguarationProperties注解来获取必须要设置prefix前缀
@Component  // 将此类交给spring容器进行管理
@ConfigurationProperties(prefix = "abc")  // 读取properties配置文件
public class School {

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getWebsite() {
        return website;
    }

    public void setWebsite(String website) {
        this.website = website;
    }

    private String name;

    private String website;


}

application.properties: 
	abc.name=yuluo
	abc.website=http://www.yuluo.com

// 在实体中设置get和set方法使用
```

## springboot集成jsp

1. jsp文件的放置位置依然保持不变，和mvc想同
2. 在main下面创建webapp文件夹，将其设置为web资源文件夹
3. 剩余操作方式和在springmvc中编写jsp代码一致，可以使用ModelAndView或者响应一个视图（拆分Modle和view）

## springboot集成mybatis以及mybatis-plus

1. 使用逆向工程生成实体bean，dao接口和映射文件
2. 集成mybatis最主要的两个注解
   1. @Mapper 需要在每一个Mapper接口类上添加，作用是扫描dao接口
   2. @MapperScan 是在springBoot启动入口类上添加的，是扫描所有的mapper文件
3. 关于mapper映射文件存放的位置写法有两种
   1. 将mapper接口和mapper映射文件存放到src/main/java同一目录下，还需要在pom文件中
   手动指定资源文件夹路径resources
   2. 将mapper接口和Mapper映射文件分开存放
      1. mapper接口存放到src/main/java目录下
      2. mapper映射文件存放到resources（类路径下）
      3. 需要在springboot核心配置文件中指定mapper映射文件存放的位置
   3. springboot与事务相关的代码
      在业务层中加入@Transactional发生错误之后就会回滚
      可以现在在启动类中加入@EnableTransactionManagement （开启事务）**可选项**

## springboot使用RESTful
使用boot+mybatis-plus写了一个增删改查，很nice
使用apipost测试通过了

## 关闭springboot工程的启动logo

### 关闭logo

1. 在springboot的启动类写
2. 获取入口springboot类
SpringApplication springApplication = new SpringApplication(Application.class)
3. 设置它的属性
4. springApplication.setBannerMode(Banner.Mode.OFF);
5. 传递参数启动springboot工程
springApplication.run(args);

### 更改springboot的启动logo

1. 在resource里面设置banner.txt，springboot启动的时候会自己读取
https://www.bootschool.net.ascii

## springboot集成dubo分布式框架

1. 接口工程：存放实体bean和业务接口
2. 服务提供者：要我去接口的实现类并将服务暴露且注册到注册中心，调用数据持久层
   1. 添加依赖 （dubbo，接口工程，注册中心）
   2. 配置服务提供者核心配置文件
3. 服务消费者：处理浏览器客户端发送的请求，从注册中心调用服务提供者所提供的服务
   1. 添加依赖（dubbo，注册中心，接口工程）
   2. 配置服务消费者核心配置文件

## springboot创建非wb工程

1. 非web工程创建不选择web模块

## 使用拦截器

> 定义一个拦截器，实现HandlerInterceptor接口
> 创建一个配置类（即在springmvc配置文件中使用mvc:interceptors标签） 

## 使用servlet

1. 创建一个servlet继承HttpServlet 不需要在web.xml文件中配置 利用注解@WebServlet
2. 配置bean 相当于（利用web.xml配置路径和controller之间的映射关系）
19.使用filter(过滤器)
1.注解方式
2.配置bean  添加过滤路径的时候，\*\*不能用 用一个*

## 设置字符编码

###  (第一种方式)配置类里面写一个过滤器

  ```java
  // 创建字符编码过滤器
  CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter();
  // 设置强制使用指定字符编码
  characterEncodingFilter.setForceEncoding(true);
  // 设置指定字符编码
  characterEncodingFilter.setEncoding("utf-8");
  // 新建过滤器对象
  FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
  // 设置字符编码过滤器
  filterRegistrationBean.setFilter(characterEncodingFilter);
  // 设置字符编码过滤器路径
  filterRegistrationBean.addUrlPatterns("/*");
  // 返回
  return filterRegistrationBean;
  
  ****:
  关闭spring自己对于编码的支持
  spring.http.encoding.enabled=false;
  可以统一设置浏览器的编码格式
  response.setContentType("text/html;character=utf-8");
  ```

 ###  （第二种方式）
```properties
    在配置文件中设置响应文件编码
    spring.http.encoding.enabled=true   
    spring.http.encoding.force=true
    spring.http.encoding.charset=utf-8
```

## 打war包

在pom文件中指定打包方式

```xml
<packing>war</packing>
```

要在启动类中继承SpringbootServletInitializer 重写里面的configure方法

```java
@Override
protected SpringApplicationBuilder configure(SpringApplicationBuilder builder)
// 参数为当前的springboot的启动类
// 构造新资源
return builder.source(Application.class)
```

**springboot打war包部署到tomcat中，之前在application.properties设置的上下文路径和端口号就失效了，以本地的tomcat服务器为准**

##  打jar包并部署

不需要写<packing>jar</packing>，因为默认的方式就是打jar包
需要设置maven插件的版本 `<version>1.4.2.RELEASE</version>`

要加入classpath资源文件夹的路径，类似于（mapper）的情况
可以在build中通过`<fileName>Springboot</fileName>`来指定打包之后的jar包名字
可以放到任何位置，因为springboot内嵌tomcat，直接通过 `java -jar name.jar`启动即可
**创建 一个文件
写入java -jar name.jar 后缀名为xxx.bat**

## 总结

> 使用springboot dubbo mybatisplus zookeeper thymeleaf 