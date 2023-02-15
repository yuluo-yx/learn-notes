# spring cloud Alibaba Nacos

## 简介

![image-20220717111610548](\spring cloud Alibaba Nacos.assets\image-20220717111610548.png)

### 概述：

Nacos 致力于发现、配置和管理微服务。Nacos 提供了一组简单易用的特性集，帮助您快速 实现动态服务发现、服务配置、服务元数据及流量管理。 Nacos 帮助您更敏捷和容易地构建、交付和管理微服务平台。 Nacos 是构建以“服务”为中心
的现代应用架构 (例如微服务范式、云原生范式) 的服务基础设施。

### 关于Nacos版本说明：

语义化版本：

> 初始化版本：1.0.0
>
> 修复一个小bug或者更新一个小功能：1.0.1
>
> 迭代或者新增一个小功能：1.1.0
>
> 重大更新：2.0.0

## 使用

### 安装

eureka的服务端是自己搭建了的项目。

nacos不需要搭建服务，只需要安装nacos即可，别人已经给我们搭建好了。要对应版本

spring cloud Alibaba使用2.2.7.RELEASE 版本，nacos使用2.0.3版本

下载地址：https://github.com/alibaba/nacos/releases/tag/2.0.3

![nacos](\spring cloud Alibaba Nacos.assets\nacos.png)

### 配置修改

从配置文件看出，nacos需要mysql数据库

新建一个nacos数据库，执行sql文件

![sql](\spring cloud Alibaba Nacos.assets\sql.png)

修改配置文件：application.properties

![image-20220717114904078](\spring cloud Alibaba Nacos.assets\image-20220717114904078.png)

### 数据库配置：

![image-20220717115237903](\spring cloud Alibaba Nacos.assets\image-20220717115237903.png)

### 启动

**修改完成之后，启动服务** 进入bin目录双击startup.cmd

> 注意：
>
> ​	 需要配置JAVA_HOME，不然启动startup.cmd会闪退，也可以打开cmd批处理文件设置java环境变量
>
> ​	默认是以集群的形式启动的，单独启动需要在cmd中输入 startup.cmd -m standalone
>
> ​	-m  model 模型
>
> ​	standalone 单机版
>
> ![conf](\spring cloud Alibaba Nacos.assets\conf.png)
>
> 直接修改cmd里面Moudle的值为standalone
>
> ![image-20220717121319220](\spring cloud Alibaba Nacos.assets\image-20220717121319220.png)

启动成功  浏览器访问：http://localhost:8848/nacos 登录的用户名和密码在数据库中 密码是secrity加密框架加密的

![image-20220717121516104](\spring cloud Alibaba Nacos.assets\image-20220717121516104.png)

## Nacos当作注册中心

### nacos和eureka的区别

> 1、eureka没有登录功能，nacos默认有登录功能
>
> 如果有两个项目，都有一个login服务，同时注册的时候，eureka没办法区分，只能通过加上项目名来区分
>
> ![image-20220717151756433](\spring cloud Alibaba Nacos.assets\image-20220717151756433.png)
>
> 2、nacos有命名空间

### 编码

**创建项目时使用：https://start.aliyun.com**来创建项目，在spring官方的boot项目启动中没有nacos的起步依赖

pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>indi.yuluo</groupId>
    <artifactId>nacos-client-b</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>02-nacos-client-b</name>
    <description>02-nacos-client-b</description>

    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <spring-boot.version>2.3.12.RELEASE</spring-boot.version>
        <spring-cloud-alibaba.version>2.2.7.RELEASE</spring-cloud-alibaba.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring-cloud-alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

</project>
```

#### client-a：

配置文件

```yml
server:
  port: 8080

spring:
  application:
    name: nacos-client-a
  cloud:
    nacos:
      server-addr: localhost:8848
      username: nacos
      password: nacos
      discovery:
        namespace: 77d57d98-250a-40e9-9064-6465493ccc68
        group: B_group
```

启动类

```java
package indi.yuluo.nacosclienta;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class NacosClientAApplication {

    public static void main(String[] args) {
        SpringApplication.run(NacosClientAApplication.class, args);
    }

}
```

#### client-b

配置文件：

```yml
server:
  port: 8081

spring:
  application:
    name: nacos-client-b
  cloud:
    nacos:
      server-addr: localhost:8848
      username: nacos
      password: nacos
      discovery: # 这是和服务注册相关的配置 这里要写的是命名空间的id
        namespace: 77d57d98-250a-40e9-9064-6465493ccc68
        group: A_group # 往A组中注册
        # 在nacos中默认的服务注册时的名字为应用名称，可以修改
        service: client-b
```

## 服务发现

> **注意：nacos的服务发现隔离很彻底，必须要在同一个组中才能发现**

在client-a中编写一个controller

```java
package indi.yuluo.nacosclienta.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.client.ServiceInstance;
import org.springframework.cloud.client.discovery.DiscoveryClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-17  16:04
 * @Description: TODO
 */

@RestController
public class TestController {

    @Autowired
    private DiscoveryClient discoveryClient;

    @GetMapping("/test")
    public String test() {
        List<ServiceInstance> instances = discoveryClient.getInstances("client-b");
        System.out.println(instances);

        return "ok";
    }

}
```

## nacos集成feign

### client-b：接口

```java
package indi.yuluo.nacosclientb.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-17  16:15
 * @Description: TODO
 */

@RestController
public class BController {

    @GetMapping("/testFeignAndGateway")
    public String testFeignAndGateway() {

        return "testFeignAndGateway 这是client-b中的服务，在client-a中调用这个服务！";
    }

}
```

### client-a：服务远程调用

接入feign的依赖：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>indi.yuluo</groupId>
    <artifactId>nacos-client-a</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>01-nacos-client-a</name>
    <description>01-nacos-client-a</description>

    <properties>
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <spring-boot.version>2.3.7.RELEASE</spring-boot.version>
        <spring-cloud-alibaba.version>2.2.2.RELEASE</spring-cloud-alibaba.version>
        <spring-cloud.version>Hoxton.SR12</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>com.alibaba.cloud</groupId>
            <artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
        </dependency>

        openFeign的依赖
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-dependencies</artifactId>
                <version>${spring-boot.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            <dependency>
                <groupId>com.alibaba.cloud</groupId>
                <artifactId>spring-cloud-alibaba-dependencies</artifactId>
                <version>${spring-cloud-alibaba.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            
            加入spring cloud的官方依赖管理
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

</project>
```

## Nacos集成Feign和Hystrix

> 1、加入Hystrix的依赖
>
> ```xml
>         <dependency>
>             <groupId>org.springframework.cloud</groupId>
>             <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
>         </dependency>
> ```
>
> 2、在配置文件中开启hystrix的功能，
>
> ```yml
> feign:
> 	hystrix:
> 		enabled: true
> ```
>
> 3、编写Hystrix子包继承feign接口里面，并且重写里面的方法提供备选方案
>
> 4、在继承接口的类上加入@Component注解，将类交给spring容器进行管理
>
> 5、重启服务，测试接口

## Nacos集成Gateway

网关生效的方式：

> 网关注册到注册中心，从注册中心拉取服务列表，之后进行动态路由的组装，
>
> 就可以使用  http://localhost/服务名/testFeign  进行访问

配置文件：

```yml
server:
  port: 80

spring:
  application:
    name: gateway
  cloud:
    nacos:
      server-addr: localhost:8848
      username: nacos
      password: nacos
      discovery:
        group: A_group
        # service: gateway  一般都不写
        namespace: 77d57d98-250a-40e9-9064-6465493ccc68
    gateway:
      discovery:
        locator:
          enabled: true # 开启动态路由
          lower-case-service-id: true
```

启动类上添加@EnabledDiscoveryClient启动服务即可

## 配置项

![image-20220717174527502](D:\information\Typora笔记\Java\springcloud\spring cloud alibaba Nacos\spring cloud Alibaba Nacos.assets\image-20220717174527502.png)