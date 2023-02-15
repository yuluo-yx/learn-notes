# Rabbit MQ

## MQ的相关概念

### MQ简介：

MQ(message queue)：从字面意思上看，本质是个队列，FIFO先入先出，只不过队列中存放的内容是 message 而已，还是一种跨进程的通信机制，用于上下游传递消息。在互联网架构中，MQ是一种非常常 见的上下游“逻辑解耦+物理解耦”的消息通信服务。使用了MQ之后，消息发送上游只需要依赖MQ，不用依赖其他服务。

### MQ流量削峰

通过MQ达到消减用户流量的目的。例如：平时的电商平台，下单一秒后就会下单成功。双十一 电商平台可能有上亿并发，服务器不能及时处理。只能限制用户超过额定的数量之后不能下单。使用mq缓存消息，将用户订单分散到一段时间段内进行处理，保护服务器，不至于宕机。这时用户下单十几秒才能收到消息，但是比不能下单的体验要好。

### MQ应用解耦

以电商应用为例，应用中有订单系统、库存系统、物流系统、支付系统。用户创建订单后，如果耦合 调用库存系统、物流系统、支付系统，任何一个子系统出了故障，都会造成下单操作异常。当转变成基于 消息队列的方式后，系统间调用的问题会减少很多，比如物流系统因为发生故障，需要几分钟来修复。在 这几分钟的时间里，物流系统要处理的内存被缓存在消息队列中，用户的下单操作可以正常完成。当物流
系统恢复后，继续处理订单信息即可，下单用户感受不到物流系统的故障，提升系统的可用性。

### MQ异步处理

有些服务间调用是异步的，例如 A 调用 B，B需要花费很长时间执行，但是 A 需要知道 B 什么时候可 以执行完，以前一般有两种方式，A过一段时间去调用 B 的查询 api 查询。或者 A 提供一个 callback api， B 执行完之后调用 api 通知 A 服务。这两种方式都不是很优雅，使用消息总线，可以很方便解决这个问题， A 调用 B 服务后，只需要监听 B 处理完成的消息，当 B 处理完成后，会发送一条消息给MQ，MQ会将此 消息转发给 A 服务。这样 A 服务既不用循环调用 B 的查询 api，也不用提供 callback api。同样 B 服务也不
用做这些操作。A服务还能及时的得到异步处理成功的消息。

### MQ的分类

1. ActiveMQ

   优点：单机吞吐量万级，时效性 ms级，可用性高，基于主从架构实现高可用性，消息可靠性较 低的概率丢失数据 

   缺点:官方社区现在对 ActiveMQ 5.x 维护越来越少，高吞吐量场景较少使用。

2. kafka

   大数据的杀手锏，谈到大数据领域内的消息传输，则绕不开 Kafka，这款为大数据而生的消息中间件，以其百万级 TPS的吞吐量名声大噪，迅速成为大数据领域的宠儿，在数据采集、传输、存储的过程中发挥 着举足轻重的作用。目前已经被 LinkedIn，Uber, Twitter, Netflix 等大公司所采纳。

   

   优点: 性能卓越，单机写入 TPS 约在百万条/秒，最大的优点，就是吞吐量高。时效性 ms 级可用性非
   常高，kafka 是分布式的，一个数据多个副本，少数机器宕机，不会丢失数据，不会导致不可用,消费者采 用 Pull 方式获取消息, 消息有序, 通过控制能够保证所有消息被消费且仅被消费一次;有优秀的第三方 Kafka Web 管理界面 Kafka-Manager；在日志领域比较成熟，被多家公司和多个开源项目使用；功能支持： 功能较为简单，主要支持简单的MQ功能，在大数据领域的实时计算以及日志采集被大规模使用

   

   缺点：Kafka 单机超过 64 个队列/分区，Load 会发生明显的（CPU）飙高现象，队列越多，load 越高，发送消息响应时间变长，使用短轮询方式，实时性取决于轮询间隔时间，消费失败不支持重试；支持消息顺序，但是一台代理宕机后，就会产生消息乱序，社区更新较慢；

3. RocketMQ

   RocketMQ 出自阿里巴巴的开源产品，用 Java 语言实现，在设计时参考了 Kafka，并做出了自己的一 些改进。被阿里巴巴广泛应用在订单，交易，充值，流计算，消息推送，日志流式处理，binglog 分发等场 景。
   优点:单机吞吐量十万级,可用性非常高，分布式架构,消息可以做到 0丢失,MQ功能较为完善，还是分布式的，扩展性好,支持10亿级别的消息堆积，不会因为堆积导致性能下降,源码是 java 我们可以自己阅 读源码，定制自己公司的MQ
   缺点：支持的客户端语言不多，目前是 java 及 c++，其中 c++不成熟；社区活跃度一般,没有在MQ核心中去实现 JMS等接口,有些系统要迁移需要修改大量代码

4. Rabbit MQ

   2007 年发布，是一个在AMQP(高级消息队列协议)基础上完成的，可复用的企业消息系统，是当前最主流的消息中间件之一。

    优点:由于 erlang 语言的高并发特性，性能较好；吞吐量到万级，MQ功能比较完备,健壮、稳定、易
   用、跨平台、支持多种语言 如：Python、Ruby、.NET、Java、JMS、C、PHP、ActionScript、XMPP、STOMP 等，支持 AJAX 文档齐全；开源提供的管理界面非常棒，用起来很好用,社区活跃度高；更新频率相当高 https://www.rabbitmq.com/news.html

   

   缺点：商业版需要收费,学习成本较高

### MQ的选择

1.Kafka Kafka 主要特点是基于 Pull 的模式来处理消息消费，追求高吞吐量，一开始的目的就是用于日志收集和传输，适合产生大量数据的互联网服务的数据收集业务。大型公司建议可以选用，如果有日志采集功能， 肯定是首选 kafka 了。

2.RocketMQ 天生为金融互联网领域而生，对于可靠性要求很高的场景，尤其是电商里面的订单扣款，以及业务削峰，在大量交易涌入时，后端可能无法及时处理的情况。RoketMQ在稳定性上可能更值得信赖，这些业务 场景在阿里双 11 已经经历了多次考验，如果你的业务有上述并发场景，建议可以选择 RocketMQ。 

3.RabbitMQ 结合 erlang 语言本身的并发优势，性能好时效性微秒级，社区活跃度也比较高，管理界面用起来十分方便，如果你的数据量没有那么大，中小型公司优先选择功能比较完备的 RabbitMQ。

## RabbitMQ

### 简介：

RabbitMQ是一个消息中间件：它接受并转发消息。你可以把它当做一个快递站点，当你要发送一个包 裹时，你把你的包裹放到快递站，快递员最终会把你的快递送到收件人那里，按照这种逻辑 RabbitMQ是 一个快递站，一个快递员帮你传递快件。RabbitMQ与快递站的主要区别在于，它不处理快件而是**接收，存储和转发消息数据。**

### 四大核心概念

生产者：

>  产生数据发送消息的程序是生产者

交互机：

> 交换机是 RabbitMQ非常重要的一个部件，一方面它接收来自生产者的消息，另一方面它将消息 推送到队列中。交换机必须确切知道如何处理它接收到的消息，是将这些消息推送到特定队列还是推
> 送到多个队列，亦或者是把消息丢弃，这个得有交换机类型决定

队列：

> 队列是 RabbitMQ内部使用的一种数据结构，尽管消息流经 RabbitMQ和应用程序，但它们只能存 储在队列中。队列仅受主机的内存和磁盘限制的约束，本质上是一个大的消息缓冲区。许多生产者可以将消息发送到一个队列，许多消费者可以尝试从一个队列接收数据。这就是我们使用队列的方式

消费者：

> 消费与接收具有相似的含义。消费者大多时候是一个等待接收消息的程序。请注意生产者，消费者和消息中间件很多时候并不在同一机器上。同一个应用程序既可以是生产者又是可以是消费者。

### RabbitMQ 模式

1. 简单模式：Hello World
2. 工作模式：Work queues
3. Publish/Subscrible：发布订阅模式
4. Routing：路由模式
5. Topis：主题模式
6. Publisher Confirms：发布确认模式

### Rabbit MQ原理及名词解释

![image-20220719094308373](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220719094308373.png)

- Broker：接收和分发消息的应用，RabbitMQ Server 就是 Message Broker

- Virtual host：出于多租户和安全因素设计的，把 AMQP 的基本组件划分到一个虚拟的分组中，类似于网络中的 namespace 概念。当多个不同的用户使用同一个 RabbitMQ server 提供的服务时，可以划分出 多个 vhost，每个用户在自己的 vhost 创建 exchange／queue 等

- Connection：publisher／consumer 和 broker 之间的 TCP 连接 

- Channel：如果每一次访问 RabbitMQ 都建立一个 Connection，在消息量大的时候建立 TCP Connection 的开销将是巨大的，效率也较低。Channel 是在 connection 内部建立的逻辑连接，如果应用程 序支持多线程，通常每个 thread 创建单独的 channel 进行通讯，AMQP method 包含了 channel id 帮助客 户端和message broker 识别 channel，所以 channel 之间是完全隔离的。Channel 作为轻量级的 Connection 极大减少了操作系统建立 TCP connection 的开销

- Exchange：message 到达 broker 的第一站，根据分发规则，匹配查询表中的 routing key，分发
  消息到 queue 中去。常用的类型有：direct (point-to-point), topic (publish-subscribe) and fanout
  (multicast)

- Queue：消息最终被送到这里等待 consumer 取走 

- Binding：exchange 和 queue 之间的虚拟连接，binding 中可以包含 routing key，Binding 信息被保存到 exchange 中的查询表中，用于message 的分发依据

### 安装

Rabbit MQ需要依赖Erlang环境作为底层依赖

Erlang：http://www.erlang.org/download/otp_win64_17.3.exe

Rabbit MQ：https://www.rabbitmq.com/

> 开启 web 管理插件 
>
> ​	rabbitmq-plugins enable rabbitmq_management
>
> 用户名和密码都是guest，更多参考官方文档

### 常用命令

```shell
net start RabbitMQ  启动
net stop RabbitMQ  停止
rabbitmqctl status  查看状态

健康检查： rabbitmqctl status

启动监控管理器：rabbitmq-plugins enable rabbitmq_management

关闭监控：rabbitmq-plugins disable rabbitmq_management

停止服务：rabbitmq-service stop

启动服务：rabbitmq-service start

重启命令：net stop RabbitMQ && net start

帮助命令：rabbitmqctl help

rabbitmqctl list_queues查看所有队列

rabbitmqctl reset清除所有队列

rabbitmqctl list_exchanges查看所有交换器

rabbitmqctl add_user username password添加用户

rabbitmqctl set_user_tags username administrator分配角色

rabbitmqctl list_bindings 查看交换器和队列的绑定关系

修改密码
rabbitmqctl change_password Username Newpassword

删除用户
rabbitmqctl delete_user Username
```

### 添加用户和授予权限

添加用户

```cmd
rabbitmqctl.bat add_user yuluo root
```

设置用户角色

```cmd
rabbitmqctl.bat set_user_tags yuluo administrator
```

列出用户列表

```cmd
rabbitmqctl.bat list_users
```

授予权限

```cmd
rabbitmqctl.bat set_permissions -p / yuluo  '.*'  '.*'  '.*'
```

至此，rabbit mq安装完成！

******

跳过

## hello示例

### pom

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>indi.yuluo</groupId>
    <artifactId>rabbitmq-hello</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>

    <dependencies>
        <!--rabbitmq依赖客户端-->
        <dependency>
            <groupId>com.rabbitmq</groupId>
            <artifactId>amqp-client</artifactId>
            <version>5.8.0</version>
        </dependency>

        <!--操作文件流的一个依赖-->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.6</version>
        </dependency>
    </dependencies>

    <!--指定jdk编译版本-->
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>8</source>
                    <target>8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>

</project>
```

### 生产者

```java
package indi.yuluo.rabbitmq.one;

import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

import java.io.IOException;
import java.nio.charset.StandardCharsets;
import java.util.concurrent.TimeoutException;

/**
 * @Author: yuluo
 * @CreateTime: 2022-11-16  21:22
 * @Description: 生产者
 */

public class Producer {

    // 队列名称
    public static final String QUEUE_NAME = "hello";

    // 发消息
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        // 设置工厂ip 用来连接rabbitmq队列
        factory.setHost("localhost");
        // 用户名
        factory.setUsername("yuluo");
        // 密码
        factory.setPassword("@@@@@@");

        // 建立连接
        Connection connection = factory.newConnection();
        // 获取连接中的信道
        Channel channel = connection.createChannel();
        /**
         * 通过信道 声明一个队列
         * 参数1：队列名称
         * 参数2：队列中的信息是否持久化（磁盘）默认情况下消息存储在内存中
         * 参数3：该队列是否只提供一个消费者进行消费 是否进行消息共享 true可以多个消费者消费 false只能一个消费者消费
         * 参数4：是否自动删除，最后一个消费者端开连接之后，该队列是否自动删除，true 自动删除，false不自动删除
         * 参数5：暂时不用
         */
        channel.queueDeclare(QUEUE_NAME, false, false, false, null);

        // 发 消息
        String msg = "hello world!";

        /**
         * 发送消息
         * 参数1：表示发送到那个交换机，本次例子未考虑交换机，所以给空字符串
         * 参数2：路由的key，本次是队列名称
         * 参数3：其他参数信息
         * 参数4：发送消息的消息体
         */
        channel.basicPublish("", QUEUE_NAME, null, msg.getBytes(StandardCharsets.UTF_8));
        System.out.println("消息发送完毕！");
    }

}
```

### 消费者

```java
package indi.yuluo.rabbitmq.one;

import com.rabbitmq.client.*;

import java.io.IOException;
import java.util.concurrent.TimeoutException;

/**
 * @Author: yuluo
 * @CreateTime: 2022-11-20  21:28
 * @Description: TODO
 */

public class Consumer {

    public static final String QUEUE_NAME = "hello";

    // 发消息
    public static void main(String[] args) throws IOException, TimeoutException {
        // 创建连接工厂
        ConnectionFactory factory = new ConnectionFactory();
        // 设置工厂ip 用来连接rabbitmq队列
        factory.setHost("192.168.3.19");
        // 用户名
        factory.setUsername("yuluo");
        // 密码
        factory.setPassword("@@@@@@");;
        Connection connection = factory.newConnection();

        Channel channel = connection.createChannel();

        // 声明 接受消息
        DeliverCallback deliverCallback = (consumerTag, message) -> {
            System.out.println(new String(message.getBody()));
        };

        // 取消消息接受时的回调
        CancelCallback cancelCallback = consumerTag -> {
            System.out.println("消息消费被中断！");
        };

        /**
         * 消费者消费消息
         * 1 消费那个队列
         * 2 消费成功者之后是否要自动应答 true 代表自动应答 false 手动
         * 3 消费者未成功消费的回调
         * 4 消费者取消消费的回调
         */
        channel.basicConsume(QUEUE_NAME, true, deliverCallback, cancelCallback);
    }

}
```

## Work Queues

跳过

******

## spring boot整合RabbitMQ

pom.xml

```xml
<!--spring boot整合rabbit mq 依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```

application.yml

```yml
spring:
  application:
    name: rabbit-springboot
  rabbitmq:
    host: 127.0.0.1
    port: 5672
    username: yuluo
    password: test
    virtual-host: test
```

### hello world模型

生产者

```java
package indi.yuluo.springbootrabbitmq;

import org.junit.jupiter.api.Test;
import org.springframework.amqp.rabbit.core.RabbitTemplate;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

/**
 * @Author: yuluo
 * @CreateTime: 2022-12-26  19:17
 * @Description: TODO
 */

@SpringBootTest
public class TestRabbitMQ {

    // 注入RaabbitTemplate
    @Autowired
    private RabbitTemplate rabbitTemplate;

    /**
     * 生产者
     */
    @Test
    public void test() {
        rabbitTemplate.convertAndSend("hello", "hello world");
    }

}
```

消费者

```java
package indi.yuluo.springbootrabbitmq.test;

import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.RabbitHandler;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

/**
 * @Author: yuluo
 * @CreateTime: 2022-12-26  19:58
 * @Description: 消费者
 */

@Component
// 如果没有队列就自己创建一个队列，后续相关参数在次设置
@RabbitListener(queuesToDeclare =  @Queue("hello"))
public class Customer {

    @RabbitHandler
    public void receivel(String message) {
        System.out.println("message = " + message);
    }

}
```

> 在队列中相关的设置都可以在`@RabbitListener中进行设置`

### 工作模型

```java
/**
 * 《work模型》
 */
@Test
public void testWork() {
    for (int i = 0; i < 10; i++) {
        rabbitTemplate.convertAndSend("work", "work模型");
    }
}
```

```java
package indi.yuluo.springbootrabbitmq.work;

import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

/**
 * @Author: yuluo
 * @CreateTime: 2022-12-26  20:08
 * @Description: TODO
 */

@Component
public class WorkCustomer {

    /**
     * 消费者1
     * @param msg
     */
    @RabbitListener(queuesToDeclare = @Queue("work"))
    public void receive1(String msg) {
        System.out.println("work msg1 = " + msg);
    }

    /**
     * 消费者1
     * @param msg
     */
    @RabbitListener(queuesToDeclare = @Queue("work"))
    public void receive2(String msg) {
        System.out.println("work msg2 = " + msg);
    }

}
```

### 发布订阅模型

```java
/**
 * 发布订阅模型
 */
@Test
public void testFanout() {
    rabbitTemplate.convertAndSend("logs", "", "Fanout模型发送的消息");
}
```

```java
package indi.yuluo.springbootrabbitmq.fanout;

import org.springframework.amqp.rabbit.annotation.Exchange;
import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.QueueBinding;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

/**
 * @Author: yuluo
 * @CreateTime: 2022-12-26  20:39
 * @Description: TODO
 */

@Component
public class FanoutCustomer {

    @RabbitListener(bindings = {
            @QueueBinding(
                    value = @Queue, // 创建临时队列
                    exchange = @Exchange(value = "logs", type="fanout") // 绑定的交换机
            )
    })
    public void receive1(String msg) {
        System.out.println("msg1 = " + msg);
    }

    @RabbitListener(bindings = {
            @QueueBinding(
                    value = @Queue, // 创建临时队列
                    exchange = @Exchange(value = "logs", type="fanout") // 绑定的交换机
            )
    })
    public void receive2(String msg) {
        System.out.println("msg2 = " + msg);
    }

}
```

### 路由模型

```java
/**
 * 路由模型
 */
@Test
public void testRouting() {
    rabbitTemplate.convertAndSend("directs", "info", "发送info的key的路由信息");
}
```

```java
package indi.yuluo.springbootrabbitmq.route;

import org.springframework.amqp.rabbit.annotation.Exchange;
import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.QueueBinding;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

/**
 * @Author: yuluo
 * @CreateTime: 2022-12-26  20:46
 * @Description: TODO
 */

@Component
public class RouteCustomer {

    @RabbitListener(bindings = {
            @QueueBinding(
                    value = @Queue, // 创建临时队列
                    exchange = @Exchange(value = "directs", type="direct"), // 自定义交换机名称和类型
                    key = {"info", "error", "warn"}  // 只接受三种类型的路由信息
            )
    })
    public void receive1(String msg) {
        System.out.println("msg1 = " + msg);
    }

    @RabbitListener(bindings = {
            @QueueBinding(
                    value = @Queue, // 创建临时队列
                    exchange = @Exchange(value = "directs", type="direct"), // 自定义交换机名称和类型
                    key = {"error"}  // 接受不到info类型的路由信息
            )
    })
    public void receive2(String msg) {
        System.out.println("msg2 = " + msg);
    }

}
```

### 动态路由 订阅模式

```java
/**
 * 动态路由 订阅模式
 */
@Test
public void testTopic1() {
    rabbitTemplate.convertAndSend("topics", "user.save", "user.save 路由消息");
}
@Test
public void testTopic2() {
    rabbitTemplate.convertAndSend("topics", "order.add", "order.add 路由消息");
}
```

```java
package indi.yuluo.springbootrabbitmq.topic;

import org.springframework.amqp.rabbit.annotation.Exchange;
import org.springframework.amqp.rabbit.annotation.Queue;
import org.springframework.amqp.rabbit.annotation.QueueBinding;
import org.springframework.amqp.rabbit.annotation.RabbitListener;
import org.springframework.stereotype.Component;

/**
 * @Author: yuluo
 * @CreateTime: 2022-12-26  21:00
 * @Description: TODO
 */

@Component
public class TopicCustomer {

    @RabbitListener(bindings = {
            @QueueBinding(
                    value = @Queue,
                    exchange = @Exchange(type = "topic", name = "topics"),
                    key = {"user.save", "user.*"}
            )
    })
    public void recevie1(String msg) {
        System.out.println("msg1 = " + msg);
    }

    @RabbitListener(bindings = {
            @QueueBinding(
                    value = @Queue,
                    exchange = @Exchange(type = "topic", name = "topics"),
                    key = {"order.#", "prod.#", "user.*"}
            )
    })
    public void recevie2(String msg) {
        System.out.println("msg2 = " + msg);
    }

}
```



