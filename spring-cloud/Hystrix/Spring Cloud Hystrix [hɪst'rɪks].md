# Spring Cloud Hystrix [hɪst'rɪks] 

## 服务雪崩

> 1、用户去请求A服务，A的tomcat会分配一个线程去支持用户的访问，A发现需要完成用户的操作，需要去调用B，
>
> 2、A去请求B，B的tomcat也会分配一个线程去支持A的访问。B发现需要完成A的操作，需要去调用C
>
> 3、B去调用C，但是C宕机了，B并不知道，一如既往的去访问c
>
> 导致A和B的线程都没有回收，此时有大量请求进入A服务或者B服务，AB会报错 503 sevrvice unavaliable
>
> **在分布式的链路中，只要有一个服务宕机，那么就可能导致一整个业务线都瘫痪**

***本质：*线程没有及时回收**

## 服务雪崩的解决方法

熔断：（保险丝）直接return，业务不能完成，但是可以缓解服务器压力

解决办法：

> 1、调整等待时间，这样可以缓解压力  （很有局限性。不是很灵活，有的服务需要很长时间去执行）
>
> 2、（拦截器）在上游服务提前知道前一个服务的状态，如果正常则访问，宕机则return，这样就可以缓解服务雪崩

**我们可以容忍服务挂掉，但是需要做对应的策略可以设置一个备选方案**

用户希望业务可以正常完成，

作为开发人员来讲，要保证其他的服务可以用

## spring cloud Hystrix

熔断器，也叫断路器！（正常情况下 断路器是关的 只有出了问题才打开）用来保护微服务不雪崩的方法。思想和我们上面画的拦截器一样。 Hystrix 是Netflix 公司开源的一个项目，它提供了熔断器功能，能够阻止分布式系统中出现联动故障。

Hystrix 是通过隔离服务的访问点阻止联动故障的，并提供了故障的解决方案，从
而提高了整个分布式系统的弹性。微博 弹性云扩容 Docker K8s

**当有服务调用的时候，才会出现服务雪崩，所以 Hystrix 常和 OpenFeign，Ribbon 一起出现**

在服务消费者（客户端加入Hystrix的依赖）

## 代码实现

### 服务消费者（客户端）

application.java

```java
package indi.yuluo.customerservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableEurekaClient
@EnableFeignClients
public class CustomerServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(CustomerServiceApplication.class, args);
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
    <artifactId>customer-service</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>01-customer-service</name>
    <description>01-customer-service</description>
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
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
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

application.yml

```yml
server:
  port: 9091
spring:
  application:
    name: customer-servie
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
  instance:
    hostname: localhost
    prefer-ip-address: true
    instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}
# 开启Hystrix的功能
# 在spring的F版本以前，默认是开启的，F版本之后，关闭（因为有了其他的熔断组件）
feign:
  hystrix:
    enabled: true
```

controller

```java
package indi.yuluo.customerservice.controller;

import indi.yuluo.customerservice.feign.CustomerRentCarFeign;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-13  11:58
 * @Description: TODO
 */

@RestController
public class CustomerController {

    // 注入feign的接口
    // 注意：在这的时候需要在启动类上加一个@EnableFeignClients的注解来开启feign接口的功能
    @Autowired
    private CustomerRentCarFeign customerRentCarFeign;

    @GetMapping("/customerRent")
    public String customerRent() {

        System.out.println("客户来租车了！");

        // 使用feign调用远程接口
        String rent = customerRentCarFeign.rendCar();

        return rent;
    }


}
```

feign

```java
package indi.yuluo.customerservice.feign;

import indi.yuluo.customerservice.feign.hystrix.CustomerRentCArFeignHystrix;
import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-13  11:59
 * @Description: TODO
 */

/*
 value是服务提供者的名字
 另外需要在@FeignClient的注解中指定备选方案
*/
@FeignClient(value = "rent-car-service", fallback = CustomerRentCArFeignHystrix.class)
public interface CustomerRentCarFeign {

    // 写的是需要rpc调用的远程方法签名
    @GetMapping("/rent")
    String rendCar();

}
```

feign里的Hystrix子包

```java
package indi.yuluo.customerservice.feign.hystrix;

import indi.yuluo.customerservice.feign.CustomerRentCarFeign;
import org.springframework.stereotype.Component;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-13  12:10
 * @Description:
 *  1、加入spring容器进行管理
 *  2、重写feign接口里的方法，重写的方法就是备选方案
 *
 */

@Component
public class CustomerRentCArFeignHystrix implements CustomerRentCarFeign {

    /**
     * 这个方法就是备选方案
     * @return
     */
    @Override
    public String rendCar() {
        return "我是备选方案";
    }
}
```

**feign里面的Hystrix实现类的作用是提供备选方案，就是在服务提供者宕机之后的选择方案**

> 1、在feign中建立hystrix
>
> 2、在hystrix中建立feign接口的实现类
>
> 3、加入@Component，将类交给spring容器进行管理，否则找不到bean
>
> 3、重写feign接口里的方法，提供备选方案

### 服务提供者（服务端）

application.java

```java
package indi.yuluo.rentcarservice;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableEurekaClient
public class RentCarServiceApplication {

    public static void main(String[] args) {
        SpringApplication.run(RentCarServiceApplication.class, args);
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
    <artifactId>rent-car-service</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>02-rent-car-service</name>
    <description>02-rent-car-service</description>
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

controller

```java
package indi.yuluo.rentcarservice.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-13  11:55
 * @Description: TODO
 */

@RestController
public class RentCarController {

    @GetMapping("/rent")
    public String rendCar() {
        return "租车成功！";
    }

}
```

application.yml

```yml
server:
  port: 9090
spring:
  application:
    name: rent-car-service
eureka:
  instance:
    hostname: localhost
    prefer-ip-address: true
    instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
```

### 测试结果

启动服务提供者和服务消费者。浏览器访问http://localhost:9091/customerRent

浏览器显示租车成功

停止服务端，重新刷新浏览器，Hystrix的备选方案生效，重新启动服务端，再次刷新浏览器。服务恢复！

## Hystrix的常用配置

```yml
hystrix: #hystrix 的全局控制
  command:
    default:    #default 是全局控制，也可以换成单个方法控制，把 default 换成方法名即可
      circuitBreaker:
        enabled: true #开启断路器
        requestVolumeThreshold: 3 #失败次数（阀值）
        sleepWindowInMilliseconds: 20000 #窗口时间
        errorThresholdPercentage: 60 #失败率
      fallback:
        isolation:
          semaphore:
            maxConcurrentRequests: 1000 #信号量隔离级别最大并发数
      execution:
        isolation:
          Strategy: thread #隔离方式thread线程隔离集合和SEMAPHORE信号量隔离级别
          thread: 
            timeoutInMilliseconds: 3000 #调用超时时长
```
