# spring cloud Alibaba

## 项目简介

Spring Cloud Alibaba 致力于提供微服务开发的**一站式解决方案**。此项目包含开发分布式 应用微服务的必需组件，方便开发者通过 Spring Cloud 编程模型轻松使用这些组件来开发 分布式应用服务。 依托 Spring Cloud Alibaba，您只需要添加一些注解和少量配置，就可以将 Spring Cloud
应用接入阿里微服务解决方案，通过阿里中间件来迅速搭建分布式应用系统。

## 组件

> zookeeper：也是用来当作注册中心的
>
> sentinel：熔断器，有限流功能
>
> Dubbo：Feign在分布式中用的很多
>
> Seata：分布式事务
>
> RocketMQ：消息队列，有延时。
>
> 商业化组件：（付费）
>
> OSS：七牛云类似的功能，存储文档和资料
>
> SMS：发送短信，一条短信0.035，三分五厘
>
> SchedulerX：定时任务调度
>
> 项目地址：ttps://github.com/alibaba/spring-cloud-alibaba

![image-20220717104140421](.\images\image-20220717104140421.png)

**[Sentinel](https://github.com/alibaba/Sentinel)**：把流量作为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。代替Hystrix

**[Nacos](https://github.com/alibaba/Nacos)**：一个更易于构建云原生应用的动态服务发现、配置管理和服务管理平台。

**[RocketMQ](https://rocketmq.apache.org/)**：一款开源的分布式消息系统，基于高可用分布式集群技术，提供低延时的、高可靠的消息发布与订阅服务。

**[Seata](https://github.com/seata/seata)**：阿里巴巴开源产品，一个易于使用的高性能微服务分布式事务解决方案。

**[Alibaba Cloud OSS](https://www.aliyun.com/product/oss)**: 阿里云对象存储服务（Object Storage Service，简称 OSS），是阿里云提供的海量、安全、低成本、高可靠的云存储服务。您可以在任何应用、任何时间、任何地点存储和访问任意类型的数据。

**[Alibaba Cloud SchedulerX](https://cn.aliyun.com/aliware/schedulerx)**: 阿里中间件团队开发的一款分布式任务调度产品，提供秒级、精准、高可靠、高可用的定时（基于 Cron 表达式）任务调度服务。

**[Alibaba Cloud SMS](https://www.aliyun.com/product/sms)**: 覆盖全球的短信服务，友好、高效、智能的互联化通讯能力，帮助企业迅速搭建客户触达通道。

## 组件版本关系

依赖官方文档配置，自行搭配项目可能不可用。

### 组件版本关系

![image-20220717105256579](.\images\image-20220717105256579.png)

### 毕业框架版本关系

一般使用的是次新本，不用最新版

spring boot 2.3.12

spring cloud Hoxton.SR12

spring cloud alibaba 2.2.7

![image-20220717105439777](.\images\image-20220717105439777.png)