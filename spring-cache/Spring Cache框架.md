# Spring Cache框架

## 介绍

` Spring Cache框架` 是`Spring3.1`版本发布出来的，他是对使用缓存进行封装和抽象，通过在方法上使用注解就能拿到缓存结果。因为用了annotation，所以它解决了业务代码和缓存代码的耦合度问题，即再不侵入业务代码的基础上让现有代码即刻支持缓存，它让开发人员无感知的使用了缓存。

spring cache底层提供了一层抽象，可以切换不同的cache实现，具体实现是通过CacheManeger接口统一不同的缓存技术

**CacheManager是spring cache提供的各种缓存技术抽象接口**

针对不同的缓存技术需要实现不同的CacheManager

| CacheManager      | 描述                               |
| ----------------- | ---------------------------------- |
| EnCacheManager    | 使用EhCache作为缓存技术            |
| GuavaCacheManager | 使用google的GuavaCache作为缓存技术 |
| RedisCacheManager | 使用Redis作为缓存技术              |

**特别注意：（注意：对于redis的缓存，springcache只支持string）**

## 常用注解

| 注解           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| @EnableCaching | 开启缓存注解功能（在启动类上面添加）                         |
| @Cacheable     | 在方法执行前spring先查看缓存中是否有数据，如果有数据，则直接返回缓存数据，若没有数据，调用方法并将方法返回值放到缓存中 |
| @CachePut      | 将方法的返回值放到cache中  （参数：value=""， 缓存的名称，每个缓存下面有多个key  key="" 缓存的key） |
| @CacheEvict    | 将一条或多条数据从缓存中删除                                 |

## 配置

如果项目中导入了redis，sping cache会默认集成redis作为缓存

spring cache的包已经传递到了spring-context的包里面

如果我们需要使用redis作为缓存的话，需要额外导入一个maven依赖

```xml
<!--spring cache-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```

## 使用方式

在springboot中导入sping cache的操作步骤（使用redis缓存）

- 导入maven坐标

  ```xml
  <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-cache</artifactId>
  </dependency>
  ```

- 配置application.yml

  ```yml
  spring:
  	cache:
  		redis:
  			time-to-live:1800000 # 设置缓存有效期
  ```

  



## spring cache的大坑

对于redis的缓存，springcache只支持String，其他的Hash、List、set、ZSet都不支持，所以对于Hash、List、set、ZSet只能用RedisTemplate
对于多表查询的数据缓存，springcache是用支持的，只支持单表的简单缓存。对于多表的整体缓存，只能用RedisTemplate。

