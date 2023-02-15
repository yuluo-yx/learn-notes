# Spring5 AOP

## 前置知识 BeanPostProcessor

### 介绍

后置处理Bean：对Spring工厂所创建的对象，进行在加工

**是AOP的底层时间**

### 运行原理分析

程序员要实现的方法：

```java
Object postProcessBeforeInitiallization(Object bean, String beanName)
在Spring创建完对象，并进行注入之后，可以运行Before方法进行加工
获得Spring创建的对象，通过方法参数
最终通过返回值交给spring框架
    
Object postProcessAfterInitiallization(Object bean, String beanName)    
在Spring执行完对象的初始化操作之后，可以运行After方法进行加工
获得Spring创建的对象，通过方法参数
最终通过返回值交给spring框架    
```

> 在实际编码中，很少进行Spring的初始化操作，没有必要区分Before After，只需要实现其中的After方法即可
>
> 
>
> 注意：在只实现After方法时，需要对Before方法进行操作，必须return bean对象
>
> ```java
> Object postProcessBeforeInitiallization(Object bean, String beanName) {
>     return bean 对象;
> }
> ```

### 开发步骤

- 实现BeanPostProcess接口
- 在spring的配置文件中进行配置

```java
package indi.exer.yuluo.beanpost;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

/**
 * @author: yuluo
 * @createTime: 2021/11/18 15:50
 * @Description:
 */

public class MyBeanPostProcessor implements BeanPostProcessor {

    @Override
    /**
     *在初始化之前对对象进行加工处理
     */
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }

    @Override
    /**
     * 在初始化之后对对象进行加工处理
     */
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        Categroy categroy = null;

        if (bean instanceof Categroy) {
            // 获得Category对象
            categroy = (Categroy) bean;
            // 加工 修改categroy中name的值
            categroy.setName("wife");
        }
        // 作为返回值返回
        return bean;

        /**
         * 配置注入
         * <bean id="myBeanPostprocessor" class="……"/>
         */

    }

}
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <bean id="c" class="indi.exer.yuluo.beanpost.Categroy">
        <property name="name" value="yuluo"/>
        <property name="id" value="10"/>
    </bean>

    <bean id="MyBeanPostProcessor" class="indi.exer.yuluo.beanpost.MyBeanPostProcessor"/>

</beans>
```

测试类

```java
    /**
     *
     * BeanPostProcess 的开发步骤
     * 需要一个类实现BeanPostprocess接口
     * spring中的配置文件进行配置
     *
     * 用于测试：BeanPostProcessor
     */
    @Test
    public void test20() {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext3.xml");

        Categroy c = (Categroy) ctx.getBean("c");

        // System.out.println("c = " + c);
        System.out.println("name = " + c.getName());
    }
}
```

## AOP简介

