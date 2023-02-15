# spring cloud Nacos作为配置中心的用法

## 配置文件中心的作用

![image-20220718090439549](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220718090439549.png)

## 编码

### 在nacos新建一个项目配置：

![image-20220718093640532](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220718093640532.png)

之后的配置文件会在数据库的config-info的content列展示出来，

配置文件：

```yml
# 应用服务 WEB 访问端口
server:
  port: 8080


# 应用名称
spring:
  application:
    name: nacos-config-a
  # 配置项目对用的配置文件的地址
  cloud:
    nacos:
      config:
        server-addr: localhost:8848
        username: nacos
        password: nacos
        prefix: nacos-config-a  # 指定读那个配置文件
        file-extension: yml  # 文件类型
```

controller：

```java
package indi.yuluo.nacosconfiga.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-18  09:25
 * @Description: TODO
 */

@RestController
public class TestController {

    @Value("${hero.name}")
    private String name;

    @Value("${hero.age}")
    private String age;

    @Value("${hero.address}")
    private String address;

    @GetMapping("/test")
    public String test() {

        String s = name + " " + age + " " + address;

        return s;
    }

}
```

### 动态刷新配置

在controller中加一个@Refershscope的刷新作用域，即可动态获取yml的文件配置

```java
package indi.yuluo.nacosconfiga.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-18  09:25
 * @Description: TODO
 */

@RestController
@RefreshScope  // 动态刷新的作用域
public class TestController {

    @Value("${hero.name}")
    private String name;

    @Value("${hero.age}")
    private String age;

    @Value("${hero.address}")
    private String address;

    @GetMapping("/test")
    public String test() {

        String s = name + " " + age + " " + address;

        return s;
    }

}
```

一般在开发中都会加一个配置类来存放这些配置文件的属性

```java
package indi.yuluo.nacosconfiga.config;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.context.config.annotation.RefreshScope;
import org.springframework.stereotype.Component;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-18  09:40
 * @Description: TODO
 */

@Component
@RefreshScope  // 动态刷新的作用域
public class Hero {

    @Value("${hero.name}")
    private String name;

    @Value("${hero.age}")
    private String age;

    @Value("${hero.address}")
    private String address;

    public Hero() {
    }

    public Hero(String name, String age, String address) {
        this.name = name;
        this.age = age;
        this.address = address;
    }


    @Override
    public String toString() {
        return "Hero{" +
                "name='" + name + '\'' +
                ", age='" + age + '\'' +
                ", address='" + address + '\'' +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getAge() {
        return age;
    }

    public void setAge(String age) {
        this.age = age;
    }

    public String getAddress() {
        return address;
    }

    public void setAddress(String address) {
        this.address = address;
    }
}
```

在controller中注入Hero类，使用get方法获取值

## nacos的配置回滚操作

根据配置id和组名搜索配置，选中版本，进行回滚

## 配置文件读取规则

nacos 配置中心通过 namespace、dataId 和 group 来唯一确定一条配置。 

 Namespace：即命名空间。默认的命名空间为 public，我们可以在 Nacos 控制台中新建 命名空间；
dataId：即配置文件名称 

Group ： 即 配 置 分 组 ， 默 认 为 DEFAULT_GROUP ， 可 以 通 过 spring.cloud.nacos.config.group 配置。
其中：dataId 是最关键的配置字段：格式如下：

```md
${prefix} - ${spring.profiles.active} . ${file-extension}
```

说明： 

- prefix 默 认 为 spring.application.name 的 值 ， 也 可 以 通 过 配 置 项 spring.cloud.nacos.config.prefix 来配置；
- spring.profiles.active 即 为 当 前 环 境 对 应 的 profile 。 注 意 ， 当 spring.profiles.active 为空时，对应的连接符-也将不存在，dataId 的拼接格式变 成${prefix}.${file-extension}；
- file-extension 为 配 置 内 容 的 数 据 格 式 ， 可 以 通 过 配 置 项 spring.cloud.nacos.config.file-extension 来配置。
  这就是上面我们为什么能获得到配置的原因了。 

**注意:在写 dataId 的时候一定要添加文件类型后缀**

```xml
Eg: nacos-config-dev.yml
```

## 配置文件中心总结

![image-20220718100239078](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220718100239078.png)

### bootstrap.yml 写什么配置

- 应用名称
- nacos的注册和拉取配置文件

### nacos的配置中心写什么配置

- 端口
- 数据源
- redis
- mq
- 能放远程的全部放