# Spring Cloud Gateway

## 网关概述：

网关：网络关口，微服务最边缘的服务，直接暴露给用户，用来做用户和微服务的桥梁。

出口的时候要通过海关，进口的时候要通过海关。**海关的作用就相当于网关的作用**

gateway在微服务中的作用：在微服务群和前端应用之间提供**路由转发**和**安全控制**、**负载均衡**的作用，前端应用只需要访问gateway的端口即可

![image-20220715101716763](..\images\image-20220715101716763.png)

> 1.没有网关：客户端直接访问我们的微服务，会需要在客户端配置很多的 ip：port，如果 user-service 并发比较大，则无法完成负载均衡 
>
> 2.有网关：客户端访问网关，网关来访问微服务，（网关可以和注册中心整合，通过服务名 称找到目标的 ip：prot）这样只需要使用服务名称即可访问微服务，可以实现负载均衡，可以实现 token 拦截，权限验证，限流等操作

## Gateway简介：

在之前的项目开发中，使用的网关组件时zuul（netfix）

Gateway时spring cloud官方推出的用于替代zuul的新一代网关组件，是基于webFlux框架实现的，webFlux框架使用了高性能的Reactor模式通信框架的netty。

> zuul: （1.0、2.0）
>
> ​		1.0：使用的时BIO，tomcat7以前用的都是BIO，性能一般
>
> ​		2.0：性能好 NIO，tomcat7之后用的是NIO
>
> 一组过滤器，根据自定义的过滤器顺序来执行，本质就是web组件：监听器，过滤器【拦截所有请求】，servlet，拦截器（spring mvc）【主要拦截进入controller的请求】

网关的作用：

> 让路由更加简单，灵活，提供了一些强大的过滤功能，例如：熔断，限流，重试，自定义过哦长期，token验证，ip检验等

原理：网关实质上是一个过滤器，按照先后顺序来执行。

![image-20220715102800626](..\images\image-20220715102800626.png)

> 客户端向 springcloud Gateway 发出请求，然后在 Gateway Handler Mapping 中找到与请求相匹配的路由，将其发送到 Gateway Web Handler。
> Handler 再通过指定的过滤器来将请求发送到我们实际的服务的业务逻辑，然后返回。 过滤器之间用虚线分开是因为过滤器可能会在发送爱丽请求之前【pre】或之后【post】执行业务 逻辑，对其进行加强或处理。 Filter 在 【pre】 类型的过滤器可以做参数校验、权限校验、流量监控、日志输出、协议转 换等 在【post】 类型的过滤器中可以做响应内容、响应头的修改、日志的输出，流量监控等有着 非常重要的作用。
> **总结：Gateway 的核心逻辑也就是 路由转发 + 执行过滤器链**

## spring cloud gateway 三大核心概念

### 路由

路由信息的组成： 由一个 ID、一个目的URL、一组断言工厂、一组 Filter 组成。
如果路由断言为真，说明请求URL 和配置路由匹配。

### Predicate（断言）一个返回布尔类型的表达式

Spring Cloud Gateway 中 的 断 言 函 数 输 入 类 型 是 Spring 5.0 框 架 中 的 ServerWebExchange。Spring Cloud Gateway 的断言函数允许开发者去定义匹配来自于Http Request 中的任何信息比如请求头和参数。

### Filter（过滤）

spring cloud gateway中的过滤器分为两种，分别是GateWay Filter 和 Global Filter

GateWay:

> 针对某一个路由的filter，对某一个接口做限流

Global Filter

> 针对全局的filter，token、ip黑名单

## Nginx和Gateway的区别

Gateway是项目级别的转发

Nginx是服务器级别的转发

![image-20220715105540177](..\images\image-20220715105540177.png)

要做Gateway集群的时候，要通过nginx做转发

![image-20220715105655315](..\images\image-20220715105655315.png)

性能问题：

> gateway：使用的是netty，每秒钟转发量在1万左右
>
> nginx：每秒钟转发量在3-5w

## GateWay快速入门

### gateway项目搭建

在创建Gateway项目的时候不要选择spring web组件，因为web的服务器是tomcat，gateway的服务器是netty

```yml
server:
  # 网关一般是80
  port: 80

spring:
  application:
    name: gateway-server
  cloud:
    gateway:
      # 只要加了依赖，默认开启
      enabled: true
      routes:
        - id: login-service-route   # 这个是路由的id，保持唯一即可
          uri: http://localhost:10010  # uri统一资源定位符  url：统一资源表示符
          predicates:
            - Path=/doLogin  # 匹配规则，只要path匹配上了就去uri转发。并且带上路径
```

浏览器访问 http://localhost:80/doLogin 

gateway就可以自动完成路由转发

## Gateway的两种路由配置方式 （重要）

### 配置文件路由

```yml
server:
  port: 80
spring:
  application:
    name: gateway-server
  cloud:
  # 路由配置
    gateway:
      enabled: true
      routes:
        - id: login-service-route   
          uri: http://localhost:10010  
          predicates:
            - Path=/doLogin  
```

### Java Bean路由

```java
package indi.yuluo.gatewayserver.config;

import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-15  11:34
 * @Description: TODO
 */

@Configuration
public class GatewayRouteConfig {
    @Bean
    public RouteLocator routeLocator(RouteLocatorBuilder routeLocatorBuilder) {

        RouteLocatorBuilder.Builder routes = routeLocatorBuilder.routes();
        
        /**
         * 如果访问 http://localhost:80/guonei
         * 浏览器会访问 http://news.baidu.com/guonei
         */
        routes.route("path_rote_guonei", r -> r.path("/guonei").uri("http://news.baidu.com/guonei"))
                .route("path_rote_guoji", r -> r.path("/guoji").uri("http://news.baidu.com/guoji"))
                .route("path_rote_tech", r -> r.path("/tech").uri("http://news.baidu.com/tech"))
                .route("path_rote_lady", r -> r.path("/lady").uri("http://news.baidu.com/lady"))
                .build();

        return routes.build();
    }
}
```

## Gateway动态路由

需要结合eureka（注册发现中心）实现

> 从之前的配置里面我们可以看到我们的URL 都是写死的，这不符合我们微服务的要求，我们微服务是只要知道服务的名字，根据名字去找，而直接写死就没有负载均衡的效果了。默认情况下Gateway 会根据注册中心的服务列表，以注册中心上微服务名为路径创建动态路由进行转发，从而实现动态路由的功能

```yml
server:
  # 网关一般是80
  port: 80

spring:
  application:
    name: gateway-server
  cloud:
    gateway:
      # 只要加了依赖，默认开启
#      enabled: true
#      routes:
#        - id: login-service-route   # 这个是路由的id，保持唯一即可
#          uri: http://localhost:10010  # uri统一资源定位符  url：统一资源表示符
#          predicates:
#            - Path=/doLogin  # 匹配规则，只要path匹配上了就去uri转发。并且带上路径
      discovery:
        locator:
          enabled: true   # 开启动态路由
          lower-case-service-id: true   # eureka中的服务名全部是大写，这个是开启服务名称小写

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
    registry-fetch-interval-seconds: 3   # 网关拉取服务列表的时间缩短
  instance:
    hostname: localhost
    prefer-ip-address: true
    instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}
```

浏览器访问：需要加上服务名

http://localohost/login-service/doLogin

上线一个服务之后，不需要重启，自己会去做负载均衡，做服务发现

也可以通过把uri写成lb://login-service来做动态路由

```yml
server:
  port: 80
spring:
  application:
    name: gateway-server
  cloud:
  # 路由配置
    gateway:
      enabled: true
      routes:
        - id: login-service-route   
          uri: lb://login-service
          predicates:
            - Path=/doLogin  
```

## 断言工厂（了解）

**Predicate 就是为了实现一组匹配规则，让请求过来找到对应的 Route 进行处理**

> 断言就是路由添加一些条件(丰富路由功能的) 通俗的说，断言就是一些布尔表达式，满足条件的返回 true，不满足的返回 false。 
>
> Spring Cloud Gateway 将路由作为 Spring WebFlux (HandlerMapping) 基础架构的一部分 进行匹配。
>
> Spring Cloud Gateway 包括许多内置的路由断言工厂。所有这些断言都与HTTP 请求的不同属性匹配。您可以将多个路由断言可以组合使用.
>
> Spring Cloud Gateway 创建对象时，使用 RoutePredicateFactory 创建 Predicate 对象，Predicate 对象可以赋值给 Route。

还有一个访问权重的设置，意思是说： 80%的请求，由 https://weighthigh.org 这个 url 去处理
20%的请求由 https://weightlow.org 去处理

```yml
spring
	cloud: 
		gateway: 
			routes: 
				- id: weight_high 
				  uri: https://weighthigh.org 
				  predicates: 
					- Weight=group1, 8
				- id: weight_low 
				  uri: https://weightlow.org 
				  predicates:
					- Weight=group1, 2
```

断言属性

```yml
spring
	cloud: 
		gateway: 
			routes: 
				- id: weight_high 
				  uri: https://weighthigh.org 
				  predicates: 
					- Weight=group1, 8
				- id: weight_low 
				  uri: https://weightlow.org 
				  predicates:  # 断言匹配
					 - Path=/doLogin  # 正则匹配
					- After=2020-01-20T17:42:47.789-07:00[Asia/Shanghai] #此断言匹配发生在指定 日期时间之后的请求，ZonedDateTime dateTime=ZonedDateTime.now()获得 
					- Before=2020-06-18T21:26:26.711+08:00[Asia/Shanghai] #此断言匹配发生在指定日期时间之前的请求 
					- Between=2020-06-18T21:26:26.711+08:00[Asia/Shanghai],2020-06-18T21:32:26.711+08:00[Asia/Shanghai] #此断言匹配发生在指定日期时间之间的请求 
					- Cookie=name,xiaobai #Cookie 路由断言工厂接受两个参数，Cookie 名称和 regexp(一个 Java 正则表达式)。此断言匹配具有给定名称且其值与正则表达式匹配的 cookie 
					- Header=token,123456 #头路由断言工厂接受两个参数，头名称和 regexp(一个 Java 正则表达式)。此断言与具有给定名称的头匹配，该头的值与正则表达式匹配。
                 - Host=**.bai*.com:* #主机路由断言工厂接受一个参数:主机名模式列表。该模式是一个 ant 样式的模式。作为分隔符。此断言匹配与模式匹配的主机头 
                 - Method=GET,POST #方法路由断言工厂接受一个方法参数，该参数是一个或多个参数: 要匹配的 HTTP 方法 
                 - Query=username,cxs #查询路由断言工厂接受两个参数:一个必需的 param 和一个可选的 regexp(一个 Java 正则表达式)。 
                 - RemoteAddr=192.168.1.1/24 #RemoteAddr 路由断言工厂接受一个源列表(最小大小 1)，这些源是 cidr 符号(IPv4 或 IPv6)字符串，比如 192.168.1.1/24(其中 192.168.1.1 是 IP 地址，24 是子网掩码)。
```

## Filter过滤器 （重点）

### 概述

Gateway里面的过滤器和Servlet里面的过滤器差不多，路由过滤器可以用于修改进入Http请求和Http响应

### 分类

#### 按生命周期

pre：在业务逻辑之前

post：在业务逻辑之后

#### 按种类

Gateway Filter：需要配置某个路由，才能过滤。

> 记录接口的访问次数
>
> 接口限流

Global Filter：全局过滤器，不需要配置路由，系统初始化到所有路由上

> 黑名单校验
>
> token验证
>
> 参数校验，避免sql注入攻击，

Gateway过滤器有31个

Global过滤器有10个

### 自定义网关过滤器（重点）

#### 自定义全局过滤器

全局过滤器的优点是初始化时默认挂到所有的路由上，可以使用它来完成限流和IP过滤等操作

```java
package indi.yuluo.gatewayserver.filter;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.core.io.buffer.DataBuffer;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.server.reactive.ServerHttpRequest;
import org.springframework.http.server.reactive.ServerHttpResponse;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

import java.util.HashMap;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-15  16:24
 * @Description: 自定义网关过滤器
 */

@Component
public class MyGlobalFilter implements GlobalFilter, Ordered {

    /**
     * 过滤方法
     *
     * 责任链设计模式
     * 网关里面有使用，mybatis的二级缓存有使用
     *
     * @param exchange
     * @param chain
     * @return
     */
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {

        // 针对请求的过滤 拿到请求，header
        ServerHttpRequest request = exchange.getRequest();

        /*
        HttpServletRequest 是web中的
        ServeHttpRequest 是webFlux 响应式里面的
         */

        String path = request.getURI().getPath();
        System.out.println(path);

        HttpHeaders headers = request.getHeaders();
        System.out.println(headers);

        String methodName = request.getMethod().name();
        System.out.println(methodName);

        // 针对响应相关的
        ServerHttpResponse response = exchange.getResponse();

        // 用了微服务，肯定是前后端分离的一般前后端通过json传输数据
        // {"code": 200, "msg": "ok"}
        // 设置编码。响应头里面设置
        response.getHeaders().set("content-type", "application/json;charset=utf-8");
        // 组装业务返回值
        HashMap<Object, Object> map = new HashMap<>(4);
        map.put("code", HttpStatus.UNAUTHORIZED.value());
        map.put("msg", "你未授权被系统的使用权限！");

        ObjectMapper objectMapper = new ObjectMapper();
        // 把map转换成字符
        byte[] bytes = new byte[0];
        try {
            bytes = objectMapper.writeValueAsBytes(map);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }
        // 通过buffer工厂将字节数组包装成 一个数据包
        DataBuffer wrap = response.bufferFactory().wrap(bytes);

        return response.writeWith(Mono.just(wrap));

        // 放行 到下一个过滤器
        // Mono<Void> filter = chain.filter(exchange);
        // return filter;
    }

    /**
     * 指定过滤器顺序的方法
     *
     * order越小，越先执行
     *
     * @return
     */
    @Override
    public int getOrder() {
        return -1;
    }
}

```

#### ip校验

```java
package indi.yuluo.gatewayserver.filter;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.core.io.buffer.DataBuffer;
import org.springframework.http.HttpStatus;
import org.springframework.http.server.reactive.ServerHttpRequest;
import org.springframework.http.server.reactive.ServerHttpResponse;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

import java.util.Arrays;
import java.util.HashMap;
import java.util.List;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-15  16:55
 * @Description:
 *
 * 网关里面，过滤器
 * ip拦截
 * 请求都有一个源头，
 * 比如 电话:181 183 122 190……
 * 请求执行的顺序 请求 ---> gateway ---> service
 * 黑名单：不放行的请求  black_list
 * 白名单：放行的请求  white_list
 * 根据请求数量判断是黑名单还是白名单
 * 像具体的业务订单，一般是黑名单
 * 一般像数据库，服务器等用白名单
 *
 */

@Component
public class IpCheckFilter implements GlobalFilter, Ordered {

    /**
     * 网关的并发量比较高，不要在网关里面直接操作mysql
     * 后台系统可以查数据库 在用户量和并发量不大的情况下
     * 如果并发量大，去查redis 在内存中写好
     */
    private static final List<String> BLACK_LIST = Arrays.asList(
            "127.0.0.1",
            "144.128.232.147"
    );

    /**
     * 拿到ip，
     * 检验ip是否符合规范
     * 放行/拦截
     *
     * @param exchange
     * @param chain
     * @return
     */
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {

        ServerHttpRequest request = exchange.getRequest();
        // 获取ip地址
        String ip = request.getHeaders().getHost().getHostString();
        // 查询数据库，看这个ip是否是在黑名单里面，mysql数据库的并发
        // 只要能存放数据的地方都是数据库 redis mysql
        if (!BLACK_LIST.contains(ip)) {
            // 如果包含就拦截，不包含就放行
            return chain.filter(exchange);
        }
        // 拦截 设置响应
        ServerHttpResponse response = exchange.getResponse();
        response.getHeaders().set("content-type", "application/json;charset=utf-8");
        HashMap<Object, Object> map = new HashMap<>();
        map.put("code", HttpStatus.UNAUTHORIZED.value());
        map.put("mag", "你未授权被系统的使用权限！是黑名单");

        ObjectMapper objectMapper = new ObjectMapper();
        byte[] bytes = new byte[0];
        try {
            bytes = objectMapper.writeValueAsBytes(map);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }

        DataBuffer wrap = response.bufferFactory().wrap(bytes);
        Mono<Void> mono = response.writeWith(Mono.just(wrap));

        return mono;


    }

    @Override
    public int getOrder() {
        return -5;
    }
}
```

#### token校验

***注意：url中不要出现大写字符***

原理：

![image-20220716101107863](..\images\image-20220716101107863.png)

TokenCheckFilter: 

```java
package indi.yuluo.gatewayservers.filter;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.core.io.buffer.DataBuffer;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.server.reactive.ServerHttpRequest;
import org.springframework.http.server.reactive.ServerHttpResponse;
import org.springframework.stereotype.Component;
import org.springframework.util.CollectionUtils;
import org.springframework.util.StringUtils;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

import java.util.Arrays;
import java.util.HashMap;
import java.util.List;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-16  15:50
 * @Description: token校验
 */

@Component
public class TokenCheckFilter implements GlobalFilter, Ordered {

    /*注入redis*/
    @Autowired
    private StringRedisTemplate redisTemplate;

    /* 指定好放行的路径 */
    public static final List<String> ALLOW_URL = Arrays.asList(
            // "/teacher-service/teach",
            "/teacher-login-service/doLogin"
    );

    /**
     *
     * 一般token放在请求头中，一般 key是Authorization（授权） value是bearer token  行业约定带bearer
     * 1、拿到url
     * 2、判断放行
     * 3、拿到请求头
     * 4、拿到token
     * 5、校验
     * 6、放行或拦截
     *
     * @param exchange
     * @param chain
     * @return
     */
    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {

        // 获取url
        ServerHttpRequest request = exchange.getRequest();
        String path = request.getURI().getPath();
        // 校验uri
        if (ALLOW_URL.contains(path)) {
            // 直接放行
            return chain.filter(exchange);
        }

        // 检查
        HttpHeaders headers = request.getHeaders();
        List<String> authorization = headers.get("Authorization");
        if (!CollectionUtils.isEmpty(authorization)) {
            String token = authorization.get(0);
            if (StringUtils.hasText(token)) {
                // 约定好有前缀，bearer token 替换bearer为”
                String realToken = token.replaceFirst("bearer ", "");
                System.out.println(redisTemplate.hasKey(realToken));
                if (StringUtils.hasText(token) && redisTemplate.hasKey(realToken)) {
                    return chain.filter(exchange);
                }
            }
        }

        // 拦截
        ServerHttpResponse response = exchange.getResponse();
        response.getHeaders().set("content-type", "application/json;charset=utf-8");
        HashMap<Object, Object> map = new HashMap<>();
        // 返回401
        map.put("code", HttpStatus.UNAUTHORIZED.value());
        map.put("msg", "您没有该网站的使用权限！未授权！");

        // 序列化
        ObjectMapper objectMapper = new ObjectMapper();
        byte[] bytes = new byte[0];
        try {
            bytes = objectMapper.writeValueAsBytes(map);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }

        // 通过响应流的形式响应给response
        DataBuffer wrap = response.bufferFactory().wrap(bytes);

        // 响应到前端
        return response.writeWith(Mono.just(wrap));
    }

    @Override
    public int getOrder() {

        return -1;
    }
}
```

application.yml

```yml
server:
  port: 80

spring:
  application:
    name: gateway-servers

  # redis配置
  redis:
    host: 127.0.0.1
    port: 6379
    password: "082916"
    database: 0

  cloud:
    gateway:

#      非动态路由配置
#      enabled: true
#      routes:
#        - id: login-service-route
#          uri: http://localhost:8080
#          predicates:
#            - Path=/doLogin

#      动态路由配置
      discovery:
        locator:
          enabled: true   # 开启动态路由
          lower-case-service-id: true

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
    registry-fetch-interval-seconds: 3
  instance:
    hostname: localhost
    prefer-ip-address: true
    instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}
```

## 限流（面试会问）

### 什么是限流

> 通俗的说，限流就是限制一段时间内，用户访问资源的次数，减轻服务器压力，
>
> 限流大致分为 两种： 
>
> 1. IP限流（5s 内同一个 ip访问超过 3 次，则限制不让访问，过一段时间才可继续访问）
> 2. 请求量限流（只要在一段时间内(窗口期)，请求次数达到阀值，就直接拒绝后面来的访问了，过一段时间才可以继续访问）（粒度可以细化到一个 api（url），一个服务）

### 限流模型

限流模型: 

漏斗算法 ，令牌桶算法，窗口滑动算法 计数器算法

> 入不敷出 
>
> 1）、所有的请求在处理之前都需要拿到一个可用的令牌才会被处理；
> 2）、根据限流大小，设置按照一定的速率往桶里添加令牌；
>
> 3）、桶设置最大的放置令牌限制，当桶满时、新添加的令牌就被丢弃或者拒绝； 
>
> 4）、请求达到后首先要获取令牌桶中的令牌，拿着令牌才可以进行其他的业务逻辑，处理完 业务逻辑之后，将令牌直接删除；
>
> 5）、令牌桶有最低限额，当桶中的令牌达到最低限额的时候，请求处理完之后将不会删除令牌，以此保证足够的限流；

### 限流需要引入Redis的一个reactive依赖

```xml
<!--限流要引入 Redis--> 
<dependency> 
    <groupId>org.springframework.boot</groupId> 
    <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
</dependency>
```

### 编码

配置类：

```java
package indi.yuluo.gatewayserver.config;

import org.springframework.cloud.gateway.filter.ratelimit.KeyResolver;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import reactor.core.publisher.Mono;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-16  18:15
 * @Description: TODO
 */

@Configuration
public class RequestLimitConfig {

    // 针对某一个接口 ip来限流 每一个ip只能访问3次
    // Bean的名字可以设置为方法名 可以任意改
    @Bean("ipKeyResolver")
    @Primary   // 主候选的配置类
    public KeyResolver ipKeyResolver() {
        return exchage -> Mono.just(exchage.getRequest().getHeaders().getHost().getHostName());
    }

    // 针对这个路径来限制 这个路径每秒只能访问3次
    // api 就是 接口外面一般把gateway 当作网关
    @Bean
    public KeyResolver apiKeyResolver() {
        return exchange -> Mono.just(exchange.getRequest().getPath().value());
    }

}
```

application.yml配置文件

```yml
server:
  # 网关一般是80
  port: 80

spring:
  application:
    name: gateway-server
  cloud:
    gateway:
      # 只要加了依赖，默认开启
      enabled: true
      routes:
        - id: login-service-route   # 这个是路由的id，保持唯一即可
          uri: http://localhost:10010  # uri统一资源定位符  url：统一资源表示符
          predicates:
            - Path=/doLogin  # 匹配规则，只要path匹配上了就去uri转发。并且带上路径
          filters:
            - name: RequestRateLimiter  # 这个是过滤器的名称
              args:  # 过滤器参数
                key-resolver: '#{@ipKeyResolver}'  # 通过spel表达式获取ioc容器中过滤器配置bean的值
                redis-rate-limiter.replenishRate: 1  # 生成令牌的速度
                redis-rate-limiter.burstCapacity: 3  # 桶容量

#      discovery:
#        locator:
#          enabled: true   # 开启动态路由
#          lower-case-service-id: true   # eureka中的服务名全部是大写，这个是开启服务名称小写

eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka
    registry-fetch-interval-seconds: 3   # 网关拉取服务列表的时间缩短
  instance:
    hostname: localhost
    prefer-ip-address: true
    instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}
```

## 跨域配置

### 什么是跨域

 ajax  8080 8081 

> 同源策略（Same Orgin Policy）是一种约定，它是浏览器核心也最基本的安全功能，它会阻止一个域的js脚本和另外一个域的内容进行交互，如果缺少了同源策略，浏览器很容易受到XSS、CSFR等攻击。
>
> 同源：（即在同一个域）就是两个页面具有相同的协议（protocol）、主机（host）和端口号（port）。

> **因为网关是微服务的边缘 所有的请求都要走网关 跨域的配置只需要写在网关即可**

跨域的本质就是一个过滤器

### 跨域问题的解决

#### @CrossOrigin注解

#### 配置类

```java
package indi.yuluo.loginservice.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.reactive.CorsWebFilter;
import org.springframework.web.cors.reactive.UrlBasedCorsConfigurationSource;
import org.springframework.web.util.pattern.PathPatternParser;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-16  18:36
 * @Description: TODO
 */

@Configuration
public class CrossConfig {

    @Bean
    public CorsWebFilter corsFilter() {
        
        CorsConfiguration config = new CorsConfiguration();
        config.addAllowedMethod("*");
        config.addAllowedOrigin("*");
        config.addAllowedHeader("*");
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource(new PathPatternParser());
        source.registerCorsConfiguration("/**", config);
        return new CorsWebFilter(source);
    }
}
```

#### 配置文件

```yml
spring:
  application:
    name: login-service
  cloud:
    gateway:
      globalcors:
        corsConfigurations:
          '[/**]': # 针对哪些路径
            allowCredentials: true # 这个是可以携带 cookie
            allowedHeaders: '*'
            allowedMethods: '*'
            allowedOrigins: '*'
```

## 总结

1.你们网关用的什么 ? 

> Gateway zuul 

2.你们网关里面写什么代码？

> 跨域，路由（动态路由，负载均衡）ip 黑名单拦截，Token 的校验，对请求进行过滤（请求 参数校验） 对响应做处理（状态码，响应头） 熔断 限流 微服务的网关，可以很好地将具体的服务和浏览器隔离开，只暴露网关的地址给到浏览器 在微服务网关中，可以很好的实现校验认证，负载均衡（lb），黑名单拦截，限流等。 

Gateway 和 zuul 的区别

> ZuulFilter Zuul 也是web 网关，本质上就是一组过滤器，按照定义的顺序，来执行过滤操作
>
> 二者的区别： 
>
> 1. 两者均是web 网关，处理的是 http 请求 
> 1.  Gateway 是 springcloud官方的组件， zuul 则是 netflix 的产品springcloud，netflix ，alibaba（nacos，sentinel，dubbo zk，seata，rocketmq）
> 3. gateway 在 spring 的支持下，内部实现了限流、负载均衡等，扩展性也更强，但同时也 限制了仅适合于Spring Cloud套件。而 zuul 则可以扩展至其他微服务框架中，其内部没有实现限流、负载均衡等。
> 4. Gateway（Netty NIO）很好的支持异步(spring5.x ,webFlux 响应式编程默认是异步的)， 而 zuul1.0 仅支持同步 BIO zuul2.0 以后也支持异步了

Nginx 在微服务中的地位

> 最后简单聊一下nginx，在过去几年微服务架构还没有流行的日子里，nginx 已经得到了广大 开发者的认可，其性能高、扩展性强、可以灵活利用 lua 脚本构建插件的特点让人没有抵抗力。 （nginx 的请求转发 最大并发是多个次，每秒 5w-10w 左右） 3w 左右 有一个能满足我所有需求还很方便我扩展的东西，还免费，凭啥不用？？ 但是，如今很多微服务架构的项目中不会选择 nginx，我认为原因有以下几点： 微服务框架一般来说是配套的，集成起来更容易 如今微服务架构中，仅有很少的公司会面对无法解决的性能瓶颈，而他们也不会因此使用 nginx，而是选择开发一套适合自己的微服务框架（很多公司会对现有框架进行修改） spring boot 对于一些模板引擎如 FreeMarker、themleaf 的支持是非常好的，很多应用还没 有达到动、静态文件分离的地步，对 nginx 的需求程度并不大。
> **动静分离： css js 可以放在 nginx**
> 单体项目需要部署 对 nginx 的使用的需求还是比较大的
>
> 斗鱼直播面试题 
>
> ​		问题：不是使用后端技术 如何实现大规模缓存 
>
> ​		使用 Nginx 做大规模的静态资源缓存

关于限流，面试不会直接问，而是间接来问 问 不卖超

> 比如：如果在抢购过程中，用户量请求非常大，怎么确保商品不会卖超 
>
> Redis 单线程 （IO 为什么快，因为我们现在的处理器是多核心数的，redis 底层使用的是 IO 的多路复用） 一般人只会在意商品卖超，而忘记了限流的重要性
> Mq（限流 削峰，异步，解耦合）

健康状态检查等

> 健康检查的依赖 
>
> ```xml
> <!--健康检查的依赖-->
> <dependency> 
> 	<groupId>org.springframework.boot</groupId>
>     <artifactId>spring-boot-starter-actuator</artifactId>
> </dependency>
> ```
>
> 添加配置文件
>
> ```yml
> management: 
> 	endpoints: 
> 		web:
> 			exposure:
> 				include: '*' #暴露检查的端点
> ```
>
> 
