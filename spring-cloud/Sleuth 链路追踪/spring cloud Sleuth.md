# spring cloud Sleuth

## 什么是链路追踪

官网：https://spring.io/projects/spring-cloud-sleuth 

链路追踪就是：追踪微服务的调用路径

## 链路追踪的由来

在微服务框架中，一个由客户端发起的请求在后端系统中会经过多个不同的服务节点调用来协 同产生最后的请求结果，每一个请求都会开成一条复杂的分布式服务调用链路，链路中的任何 一环出现高延时或错误都会引导起整个请求最后的失败。（不建议微服务中链路调用超过 3次）

**建议：远程调用的次数不要超过 3 层，2 层最好**

## 分布式链路调用的监控

sleuth+zipkin（zipkin 就是一个可视化的监控控制台）

 Zipkin 是 Twitter 的一个开源项目，允许开发者收集 Twitter 各个服务上的监控数据，并提 供查询接口。 该系统让开发者可通过一个 Web 前端轻松的收集和分析数据，例如用户每次请求服务的处理时间等，可方便的监测系统中存在的瓶颈。

## zipkin使用

### 下载

SpringCloud 从 F 版以后已不需要自己构建 Zipkin server 了，只需要调用 jar 包即可 

https://dl.bintray.com/openzipkin/maven/io/zipkin/java/zipkin-server/
https://dl.bintray.com/openzipkin/maven/io/zipkin/java/zipkin-server/2.12.9/

### 运行

java -jar zipkin-server-2.12.9-exec.jar

### 访问

http://localhost:9411

### 结果

使用的是懒加载模式

![image-20220714101917973](..\images\\image-20220714101917973.png)



![image-20220714102104432](..\images\\image-20220714102104432.png)

## 使用方法

在每个子模块中加入依赖

```xml
<dependency> 
    <groupId>org.springframework.cloud</groupId> 
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

修改配置文件

order-service：

```yml
spring:
  application:
    name: order-service
  zipkin:
    base-url: http://localhost:9411
  sleuth:
    sampler:
      # 配置采样率，默认的采样比例为0.1， 即10%所设置的值介于0和1之间，1 表示全部采样
      probability: 1
      # 为了使用速率限制采样器，选择每秒间隔接受的trace量，最小值为1，最大值2,147,483,647(最大int值)
      rate: 10
```

user-service

```xml
spring:
  application:
    name: user-service
  zipkin:
    base-url: http://localhost:9411
  sleuth:
    sampler:
      # 配置采样率，默认的采样比例为0.1， 即10%所设置的值介于0和1之间，1 表示全部采样
      probability: 1
      # 为了使用速率限制采样器，选择每秒间隔接受的trace量，最小值为1，最大值2,147,483,647(最大int值)
      rate: 10
```



### 结果

![image-20220714103756459](..\images\\image-20220714103756459.png)

## 名词解释



## spring-boot-admin

监控所有服务的状态

### 使用方式

如果一个服务想要自己被监控，只有自身将信息暴漏出去，别人才能监控，

```xml
<!--
	 暴漏服务自身信息
    使用 spring-boot-starter-actuator 可以用于检测系统的健康情况、当前的Beans、系统的缓存等
 	 浏览器访问http://localhost:8080/actuator/health 
-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

在配置文件中加入,

```yml
# 暴漏服务自身信息
management:
  endpoints:
    web:
      exposure:
        # 暴露所有的监控断点
        include: '*'
```

引入依赖

```xml
<properties>
    <maven.compiler.source>8</maven.compiler.source>
    <maven.compiler.target>8</maven.compiler.target>
    <spring-boot-admin.version>2.3.0</spring-boot-admin.version>
</properties>
<!--admin服务监控依赖-->
<dependency>
    <groupId>de.codecentric</groupId>
    <artifactId>spring-boot-admin-dependencies</artifactId>
    <version>${spring-boot-admin.version}</version>
    <type>pom</type>
    <scope>import</scope>
</dependency>
```

> 建立一个spring boot项目，选择admin-server和web依赖
>
> 将服务注册到eureka
>
> @EnableEurekaClient
>
> 在启动类上添加
>
> @EnableAdminServer
>
> 启动项目，浏览器访问

配置文件

```yml
server:
  # 端口号范围 0-65535
  port: 9090
spring:
  application:
    name: admin-servers
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
  instance:
    hostname: localhost
    prefer-ip-address: true
    instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}

# admin暴漏信息配置
management:
  endpoints:
    web:
      exposure:
        # 暴露所有的监控断点
        include: '*'
```