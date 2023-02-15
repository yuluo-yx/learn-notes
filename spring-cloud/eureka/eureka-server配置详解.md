# eureka-server配置详解

配置分为三大类

> server
>
> client
>
> 实例配置

![image-20220615184229459](..\images\image-20220615184229459.png)

## 服务端配置

```yml
# eureka服务端配置
eureka:
  server:
    # 服务端间隔10秒做定期删除的操作 剔除不用的服务
    eviction-interval-timer-in-ms: 100000
    # 续约百分比 在一段时间之后，超过85%的服务和注册中心没有建立心跳，则不会剔除任何一个服务 认为自身网络问题
    renewal-percent-threshold: 0.85
```

## 实例配置

```yml
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

![image-20220615183952884](..\images\image-20220615183952884.png)

## 客户端配置

```yml
eureka:
  # 客户端的相关配置
  client:
    # 指定注册的地址
    service-url:
      defaultZone: http://localhost:8761/eureka
      # 可以不向eureka-server中注册
    register-with-eureka: true
    # 配置应用是否拉取注册中心的服务列表到本地
    fetch-registry: true
    # 为了缓解服务列表的脏读问题 每隔30s拉取一次 性能消耗大
    registry-fetch-interval-seconds: 10
```

