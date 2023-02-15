## spring工厂中多种创建对象方式的选择

### 第一种 @Component及其衍生注解 @Autowired

```java
@Component
public void User() {
    
}
```

> 主要应用于程序员自己开发的类型上
>
> UserService UserDAO UserController

###  第二种 @Bean



```java
@Bean
public void user() {
    return new User();
}
```

> 主要用于框架提供的类型和别的程序员开的类型 （没有源码） 无法在源码上添加@Component ，@Autowired注解

### applicationContext.xml配置文件

```xml
<bean id="user" class="indi.exer.yuluo.bean.User"/>
```

> 纯注解的开发方式，基本不用
>
> 与遗留系统的整合需要用到

###  第四种 @Import注解

> 1. 主要用在spring框架的底层
>
> 2. 多配置bean的整合上

## spring创建对象方式的优先级

```mark
@Component及其衍生注解 < @Bean < 配置文件中的bean标签

优先级高的配置可以覆盖优先级低的配置

@Component
public void User() {

}

@Bean
public void user() {
	return new User();
}

<bean id="user" class="indi.exer.yuluo,bean.User"

配置覆盖的过程中：id值 必须保持一致

```

> 使用@Bean注解的同时可以使用@ImportResource("applicationContext.xml")来引入spring配置文件，使其达到互通、
>
> **可以使用配置文件优先级的顺序来解决@Bean的耦合问题，在需要更改实现类时，在配置文件中书写id相同的bean标签来覆盖之前的@Bean标签**

