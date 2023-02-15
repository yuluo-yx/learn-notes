# spring cloud OpenFeign

## 简介

https://docs.spring.io/spring-cloud-openfeign/docs/2.2.4.RELEASE/reference/html/#spring-cloud-feign

Feign 是声明性(注解)Web 服务客户端。它使编写 Web 服务客户端更加容易。要使用 Feign， 请创建一个接口并对其进行注解。它具有可插入注解支持，包括 Feign 注解和 JAX-RS 注解。 Feign 还支持可插拔编码器和解码器。Spring Cloud 添加了对 Spring MVC 注解的支持，并 支持使用 HttpMessageConverters，Spring Web 中默认使用的注解。Spring Cloud 集成 了 Ribbon 和 Eureka 以及 Spring Cloud LoadBalancer，以在使用 Feign 时提供负载平衡
的 http 客户端。

> Feign是一个远程调用的组件（接口和注解的方式）http调用的

> Feign集成了ribbon，ribbon集成了eureka

## 代码实现

### 注册中心

eureka-server：启动之前。先开启一个eureka的服务中心

application.java

```java
package indi.yuluo.eurekaserver;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
@EnableEurekaServer  // 开启eureka的注册中心的功能
public class EurekaServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }

}
```

配置文件

```
server:
  port: 8761 # eureka\u7684\u9ED8\u8BA4\u7AEF\u53E3
  
spring:
  application: 
    name: eureka-server  # \u5E94\u7528\u540D\u79F0\uFF0C\u4E0D\u8981\u4F7F\u7528\u7279\u6B8A\u5B57\u7B26

# eureka服务端配置
eureka:
  server:
    # 服务端间隔10秒做定期删除的操作 剔除不用的服务
    eviction-interval-timer-in-ms: 100000
    # 续约百分比 在一段时间之后，超过85%的服务和注册中心没有建立心跳，则不会剔除任何一个服务 认为自身网络问题
    renewal-percent-threshold: 0.85
  # 实例的配置
  instance:
    # 实例的名称由电脑名称：应用名称：端口号组成  el表达式取值
    instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}
    # 配置主机名称
    hostname: localhost
    # 以ip得形式显示具体得服务信息
    prefer-ip-address: true
    # 服务实例续约的时间间隔
    lease-renewal-interval-in-seconds: 5
```

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.12.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>

    <groupId>indi.yuluo</groupId>
    <artifactId>eureka-server</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>01-eureka-server</name>
    <description>01-eureka-server</description>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Hoxton.SR12</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <!--maven依赖管理-->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>
```

### 服务提供者

#### order-service-provider

配置文件

```yml
server:
  port: 8080
spring:
  application:
    name: order-service-provider
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
  instance:
    hostname: localhost
    prefer-ip-address: true
    instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}
```

controller

```java
package indi.yuluo.orderservice.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-12  08:45
 * @Description: TODO
 */

@RestController
public class OrderController {

    @GetMapping("/doOrder")
    public String doOrder() {
        return "下单成功 油条豆浆！";
    }

}
```

application.java

```java
package indi.yuluo.orderservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class OrderServiceProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(OrderServiceProviderApplication.class, args);
    }

}
```

pom.xml

```java
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.12.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>indi.yuluo</groupId>
    <artifactId>order-service</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>01-order-service-provider</name>
    <description>01-order-service-provider</description>
    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Hoxton.SR12</spring-cloud.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

### 服务消费者

#### user-service-consumer

配置文件application.yml

```yml
server:
  port: 8081
spring:
  application:
    name: user-service-provider
eureka:
  instance:
    hostname: localhost
    prefer-ip-address: true
    instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka

# 针对服务超时的处理方法，feign的默认等待时间是1秒，
# feign的配置文件中没有超时时间的配置，因为feign只是封装了远程调用的功能，集成了ribbon，所以要去修改ribbon的超时时间配置
ribbon:
  ReadTimeout: 3000  # 调用超时 设置为3秒
  ConnectTimeout: 3000 # 链接服务的超时时间
```

controller

```java
package indi.yuluo.userserviceprovider.controller;

import indi.yuluo.userserviceprovider.feign.UserOrderFeign;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-12  08:53
 * @Description: TODO
 */

@RestController
public class UserController {

    // 注入feign  （UserOrderFeign）
    @Autowired
    private UserOrderFeign userOrderFeign;

    @GetMapping("/userDoOrder")
    public String UserDoOrder() {
        System.out.println("有用户下单了");

        /*
        这里需要一个远程调用
        根据官网，需要接口和注解去使用feign
        在application启动类中加入@EnableFeignClients注解，并且创建一个feign包，写入相应内容

        总结：调用流程
        浏览器（前端）----> user-service-provider(/userDoOrder) -----> RPC ----> oder-service-provider(/doOrder)
         */
        String s = userOrderFeign.doOrder();

        return s;
    }

}
```

feign

```java
package indi.yuluo.userserviceprovider.feign;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-12  09:04
 * @Description: TODO
 */

/**
 * @FeignClient(value = "")
 * value为提供者的应用名称
 */
@FeignClient(value = "order-service-provider")
public interface UserOrderFeign {

    /*
    需要调用那个controller接口，将那个方法的签名放在这里
    方法签名（包含了一个方法所有的属性）
     */
    @GetMapping("/doOrder")
    String doOrder();

}
```

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.3.12.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>indi.yuluo</groupId>
    <artifactId>user-service-provider</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>02-user-service-provider</name>
    <description>02-user-service-provider</description>
    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Hoxton.SR12</spring-cloud.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

application.java

```java
package indi.yuluo.userserviceprovider;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients   // 开启feign的客户端功能，才能发起调用
public class UserServiceProviderApplication {

    public static void main(String[] args) {
        SpringApplication.run(UserServiceProviderApplication.class, args);
    }

}
```

## 手写feign的实现过程

原理：

> 在UserOrderFeign的调用过程中，通过接口写了userService和orderService的调用关系
>
> 但是接口是不能做事情的
>
> 如果想做事，必须要有对象
>
> 那么这个接口对象肯定是要被创建出来代理对象的
>
> 动态代理有两种：jdk（java interface 接口 $proxy  aspectj）   cglib(subcalss 子类)
>
> jdk动态代理：只要是代理对象必须走java.lang.reflect.InvocationHandler#invoke方法，重写其中的invoke方法
>
> ​	Object proxy：代理对象
>
> ​	Method method：方法
>
> ​	object[] args：参数

**核心步骤：**

```java
/**
     * 手写feign接口的代理对象  jdk invoke
     * @param args
     */
    public static void main(String[] args) {
        UserOrderFeign userOrderFeign1 = (UserOrderFeign) Proxy.newProxyInstance(UserController.class.getClassLoader(), new Class[]{UserOrderFeign.class}, new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                System.out.println("进入代理对象 invoke方法里面");

                // 如果能在这拿到对方的ip和port  并且拿到这个方法上面的注解里面的值，feign接口的功能就完成了

                // 获取feign接口里的方法
                String name = method.getName();
                // 获取方法上的路径注解
                GetMapping annotation = method.getAnnotation(GetMapping.class);
                // 取出值
                String[] paths = annotation.value();
                String path = paths[0];
                // 路径拼接
                // String url = "http://" + "/" + path;

                // 调用ribbon去eureka的服务列表找到相应的服务（在feign接口上的@FeignClient(value = "")）

                // 获取类
                Class<?> declaringClass = method.getDeclaringClass();
                String name1 = declaringClass.getName();
                FeignClient annotation1 = declaringClass.getAnnotation(FeignClient.class);
                String valueApplicationName = annotation1.value();
                String url = "http://" + valueApplicationName + "/" + path;

                // 调用RestTemplate去发送请求，完成远程调用
                RestTemplate restTemplate = new RestTemplate();
                String forObject = restTemplate.getForObject(url, String.class);

                return forObject;
            }
        });

        // 通过代理创建出来的对象调用doOrder方法  会走invoke方法
        String s = userOrderFeign1.doOrder();
        System.out.println(s);
    }

```

## openFeign的参数处理：

Feign 传参确保消费者和提供者的参数列表一致 包括返回值 方法签名要一致 

1. 通过 URL 传参数，GET 请求，参数列表使用@PathVariable（“”）
2. 如果是 GET 请求，每个基本参数必须加@RequestParam（“”）

3. 如果是 POST 请求，而且是对象集合等参数，必须加@Requestbody 或者@RequestParam

### controller

```java
package indi.yuluo.orderservice.controller;

import indi.yuluo.orderservice.domain.Order;
import org.springframework.web.bind.annotation.*;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-12  21:15
 * @Description: TODO
 */

@RestController
public class ParamController {

    /**
     * 传参的情况：
     *      url传参：/doOrder/包子
     *      get传参：
     *          传递一个参数
     *          传递多个参数
     *      post传参：
     *          传递一个对象
     *          传递一个对象+一个基本参数
     */

    @GetMapping("/testUrlParam/{name}/and/{age}")
    public String testUrlParam(@PathVariable("name") String name, @PathVariable("age") Integer age) {
        System.out.println(name + " " + age);
        return "ok";
    }

    /**
     * 如果不给@RequestParam设置值，默认是需要传递值的，不然报错
     *     @AliasFor("value")
     *     String name() default "";
     *
     *     boolean required() default true;
     * @param name
     * @return
     */
    @GetMapping("/testOneParam")
    public String oneParam(@RequestParam(required = false) String name) {
        System.out.println(name);
        return "ok";
    }

    @GetMapping("/testTwoParam")
    public String twoParam(@RequestParam(required = false) String name, @RequestParam(required = false) Integer age) {
        System.out.println(name);
        return "ok";
    }

    @PostMapping("/oneObj")
    public String oneObj(@RequestParam Order order) {
        System.out.println(order);
        return "ok";
    }

    @PostMapping("/oneObjOneParam")
    public String oneObjOneParam(@RequestParam Order order, @RequestParam("name") String name) {
        System.out.println(order);
        System.out.println(name);
        return "ok";
    }

}
```

### UserOrderFeign

```java
package indi.yuluo.userserviceprovider.feign;

import indi.yuluo.userserviceprovider.domain.Order;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-12  09:04
 * @Description: TODO
 */

/**
 * @FeignClient(value = "")
 * value为提供者的应用名称
 */
@FeignClient(value = "order-service-provider")
public interface UserOrderFeign {

    /*
    需要调用那个controller接口，将那个方法的签名放在这里
    方法签名（包含了一个方法所有的属性）
     */
    @GetMapping("/doOrder")
    String doOrder();

    @GetMapping("/testUrlParam/{name}/and/{age}")
    String testUrlParam(@PathVariable("name") String name, @PathVariable("age") Integer age);

    @GetMapping("/testOneParam")
    String oneParam(@RequestParam(required = false) String name);

    @GetMapping("/testTwoParam")
    String twoParam(@RequestParam(required = false) String name, @RequestParam(required = false) Integer age);

    @PostMapping("/oneObj")
    String oneObj(@RequestParam Order order);

    @PostMapping("/oneObjOneParam")
    String oneObjOneParam(@RequestParam Order order, @RequestParam("name") String name);

}
```

## Feign日志分析

feign提供了日志打印的功能，我们可以通过调整日志级别来解开feign中http请求的所有细节

- Level for logger在feign包中

  ```java
  public static enum Level {
      NONE,  无日志打印
      BASIC,	打印请求方法和url以及请求状态码
      HEADERS, 打印请求头信息
      FULL;   所有信息都打印
  
      private Level() {
      }
  }
  ```

### 用法

在applciation.java中配置

```java
/**
* 打印所有的日志信息
*/
@Bean
public Logger.Level level() {
    return Logger.Level.FULL;
}
```

