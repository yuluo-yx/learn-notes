# Spring cloud Ribbon

## 概述

spring cloud ribbon（[ˈrɪbən]）是一个基于HTTP和TCP的客户端负载均衡工具，基于Netfix Ribbon实现，通过spring cloud的封装，可以轻松让我们完成将面向服务的REST模板请求自动转换成客户端负载均衡的服务调用，

负载均衡的算法：轮询，hash，权重……

**Ribbon 就是 netfix 公司的一个开源项目，主要功能是提供客户端负载均衡算法和 服务调用。Ribbon 客户端组件提供了一套完善的配置项，比如连接超时，重试等。 在 Spring Cloud 构建的微服务系统中， Ribbon 作为服务消费者的负载均衡器，有两种使 用方式，一种是和 RestTemplate 相结合，另一种是和 OpenFeign 相结合。OpenFeign 已经 默认集成了 Ribbon,关于 OpenFeign 的内容将会在下一章进行详细讲解。Ribbon 有很多子**
**模块，但很多模块没有用于生产环境!**

## 负载均衡

负载均衡，英文名称为 Load Balance（LB）http://（http网络协议） lb://（负载均衡协议），其含义 就是指将负载（工作任务）进行平衡、分摊到多个操作单元上进行运行，例如 Web 服务器、 企业核心应用服务器和其它主要任务服务器等，从而协同完成工作任务。 负载均衡构建在原有网络结构之上，它提供了一种透明且廉价有效的方法扩展服务器和网络设备的带宽、加强网络数据处理能力、增加吞吐量、提高网络的可用性和灵活性。

## 代码实现

### 服务提供者 provider-a

配置文件：

```yml
server:
  port: 9090

spring:
  application:
    name: provider
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
  instance:
    hostname: localhost
    prefer-ip-address: true
    instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}
```

Application.java

```java
package indi.yuluo.providera;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class ProviderAApplication {

    public static void main(String[] args) {

        SpringApplication.run(ProviderAApplication.class, args);
    }

}
```

controller

```java
package indi.yuluo.providera.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-11  16:17
 * @Description: TODO
 */

@RestController
public class ProviderController {

    @GetMapping("/hello")
    public String hello() {
        return "我是服务提供者aaa的接口";
    }

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
    <artifactId>provider-a</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>01-provider-a</name>
    <description>01-provider-a</description>
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
            </plugin>
        </plugins>
    </build>

</project>
```

### 服务提供者 provider-b

配置文件：

```yml
server:
  port: 9091

spring:
  application:
    name: provider

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
package indi.yuluo.providerb.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-11  16:23
 * @Description: TODO
 */

@RestController
public class ProviderController {

    @GetMapping("/hello")
    public String hello() {
        return "我是服务提供者bbb的接口";
    }

}
```

application.java

```java
package indi.yuluo.providerb;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class ProviderBApplication {

    public static void main(String[] args) {
        SpringApplication.run(ProviderBApplication.class, args);
    }

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
        <version>2.7.1</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>indi.yuluo</groupId>
    <artifactId>provider-b</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>01-provider-b</name>
    <description>01-provider-b</description>
    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>2021.0.3</spring-cloud.version>
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
            </plugin>
        </plugins>
    </build>

</project>
```

### 服务消费者 consumer

配置文件：

```yml
server:
  port: 9092
spring:
  application:
    name: consumer
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
  instance:
    hostname: localhost
    prefer-ip-address: true
    instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}
    
ribbon:
  eager-load:
    # ribbon它只有自己的话，自己本身不能做服务发现，要借助eureka，
    # ribbon需要去eureka里拉取服务列表  如果这个值为false，则懒加载，在请求的时候才拉取服务列表
    enabled: true
  eureka:
    enabled: true
  # 我们使用的ribbon 用的是RestTemplate发请求，很方便，但是不支持连接池
  http:
    client:
      # 发请求的工具很多，httpclient支持连接池，效率更好。如果想改请求工具，记得加依赖
      enabled: true
  # 也是请求工具，一般在移动端用的比较多，比较轻量级
  okhttp:
    enabled: true    
```

controller

```java
package indi.yuluo.consumer.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-11  16:35
 * @Description: TODO
 */

@RestController
public class ConsumerController {

    @Autowired
    private RestTemplate restTemplate;

    /**
     * 实际的请求地址是：http://localhost:9092/test?serviceName=provider
     *
     * 思考：ribbon是如何将http://provider/hello路径请求成功的
     * http://localhost:9090/hello
     * 1、拦截请求
     * 2、截取主机名称
     * 3、借助eureka来做服务发现
     * 4、通过负载均衡算法来拿到一个服务ip port
     * 5、reconstructURL  （重构url）
     * 6、发起请求
     *
     * @param serviceName
     * @return
     */
    @GetMapping("/test")
    public String testRibbon(String serviceName) {

        // 正常来说 需要端口和ip以及路径才能使用

        String result = restTemplate.getForObject("http://" + serviceName + "/hello", String.class);

        return result;
    }

}
```

Application.java

```java
package indi.yuluo.consumer;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@SpringBootApplication
@EnableEurekaClient
public class ConsumerApplication {

   public static void main(String[] args) {
      SpringApplication.run(ConsumerApplication.class, args);
   }

   /**
    * 这个RestTemplate加了@LoadBalanced注解就交给ribbon来操作了，不再是它本身
    * @return
    */
   @Bean
   @LoadBalanced
   public RestTemplate restTemplate() {
      return new RestTemplate();
   }

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
   <artifactId>consumer</artifactId>
   <version>0.0.1-SNAPSHOT</version>
   <name>02-consumer</name>
   <description>02-consumer</description>
   <properties>
      <java.version>1.8</java.version>
      <spring-cloud.version>Hoxton.SR12</spring-cloud.version>
   </properties>

   <dependencies>

      <!--加入ribbon依赖-->
      <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
      </dependency>


      <dependency>
         <groupId>org.springframework.boot</groupId>
         <artifactId>spring-boot-starter-web</artifactId>
      </dependency>
      <dependency>
         <groupId>org.springframework.cloud</groupId>
         <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
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
         </plugin>
      </plugins>
   </build>

</project>
```

## 测试

浏览器 http://localhost:9092/test

查看结果。使用的是负载均衡算法是轮询

## 轮询算法的实现：

两台机器 A B

```java
List<机器>;
int index = 1 % size, list.get(index);
1 % 2 = 1;
2 % 2 = 0;
3 % 2 = 1;
4 % 2 = 0;
// 取模的好处是得到一个周期函数，让的到的结果总是小于除数的

// 具体实现
int i = 0;
i ++;  (线程不安全)
i % size;

// 怎么做一个线程安全的轮询算法，加锁的效率极低，
// 使用CAS（compare and swap） 自旋锁，没有线程的等待和唤醒的开销
// CAS的优点：性能好，java层面无锁，但是在jvm层面有锁的cmpxy
// CAS缺点：会导致短时间内cpu飙升，还有ABA问题
```

## ribbon总结

Ribbon是客户端实现负载均衡的远程调用组件，用法简单

Ribbon源码的核心：

> ILoadBalancer接口：起到承上启下的作用
>
> 1、承上：从eureka拉取服务列表
>
> 2、启下：使用IRule算法实现客户端调用的负载均衡
