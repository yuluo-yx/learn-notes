# eureka搭建集群

## 集群的方案

> 中心化集群：nginx配置tomcat
>
> 主从模式集群：mysql主从分离集群
>
> 去中心化集群：（eureka）没有主从概念。将数据进行广播和扩散，两两互相注册

## 创建eureka-server

新建三个springboot版本集成eureka

分别更改端口

### 服务集群1 配置

```yml
server:
  port: 8761  # \u5BA2\u6237\u7AEF\u7684\u7AEF\u53E3\u6CA1\u6709\u8981\u6C42

spring:
  application:
    name: eureka-server

eureka:
  # 客户端的相关配置
  client:
    	# 指定注册的地址
    	service-url:
      		# 在其他两台eureka中注册
      		defaultZone: http://localhost:8762/eureka,http://localhost:8763/eureka
    instance:
    	instance-id: ${eureka.instance.hostname}:${spring.application.name}:${server.port}
    	hostname: localhost
    	prefer-ip-address: true
    	lease-renewal-interval-in-seconds: 5
```

在b，c机器中进行同样的配置，注意端口的配置

## 集群的深入理解

### 如果是主从模式，主机怎么选择？主从模式，数据怎么同步？

主机挂掉之后怎么选举产生新的主机？

#### 哨兵模式

投票来选择新的主机，了解一下**分布式数据一致性协议**

***eureka没有分布式数据一致性协议，所有节点都是相同的***

> Paxos : （zookeeper）
>
> raft：（nacos）【可理解的分布式共识】解决数据协议和主机选举的问题 http://thesecretlivesofdata.com/raft/

