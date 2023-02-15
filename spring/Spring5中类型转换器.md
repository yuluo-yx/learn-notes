# Spring5中类型转换器

>  在spring的xml文件配置中，在对java bean进行转换时，spring会自己对`<property></property>`中的标签进行类型转换。

## 示例

java bean

```java
package indi.exer.yuluo.converter;

import java.util.Date;

/**
 * @author: yuluo
 * @createTime: 2021/11/14 19:55
 * @Description:
 */

public class People {

    private String name;
    private Date birthday;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Date getBirthday() {
        return birthday;
    }

    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }
}
```

xml配置

```xml
<bean id="person" class="indi.exer.yuluo.converter.People" >
    <property name="name" value="yuluo"/>
    <property name="birthday" value="2021-11-14"/>
</bean>
```

结果

这样的配置势必会出现问题，因为spring没有对日期格式的转换设置类型转换器

报错：

```mark
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'person' defined in class path resource [applicationContext2.xml]: Initialization of bean failed; nested exception is org.springframework.beans.ConversionNotSupportedException: Failed to convert property value of type 'java.lang.String' to required type 'java.util.Date' for property 'birthday'; nested exception is java.lang.IllegalStateException: Cannot convert value of type 'java.lang.String' to required type 'java.util.Date' for property 'birthday': no matching editors or conversion strategy found

	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:628)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.createBean(AbstractAutowireCapableBeanFactory.java:542)
	at org.springframework.beans.factory.support.AbstractBeanFactory.lambda$doGetBean$0(AbstractBeanFactory.java:335)
	at org.springframework.beans.factory.support.DefaultSingletonBeanRegistry.getSingleton(DefaultSingletonBeanRegistry.java:234)
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:333)
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:208)
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.preInstantiateSingletons(DefaultListableBeanFactory.java:944)
	at org.springframework.context.support.AbstractApplicationContext.finishBeanFactoryInitialization(AbstractApplicationContext.java:918)
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:583)
	at org.springframework.context.support.ClassPathXmlApplicationContext.<init>(ClassPathXmlApplicationContext.java:144)
	at org.springframework.context.support.ClassPathXmlApplicationContext.<init>(ClassPathXmlApplicationContext.java:85)
	at indi.exer.yuluo.TestSpring.test19(TestSpring.java:441)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:59)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:56)
	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.BlockJUnit4ClassRunner$1.evaluate(BlockJUnit4ClassRunner.java:100)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:366)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:103)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:63)
	at org.junit.runners.ParentRunner$4.run(ParentRunner.java:331)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:79)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:329)
	at org.junit.runners.ParentRunner.access$100(ParentRunner.java:66)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:293)
	at org.junit.runners.ParentRunner$3.evaluate(ParentRunner.java:306)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:413)
	at org.junit.runner.JUnitCore.run(JUnitCore.java:137)
	at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:69)
	at com.intellij.rt.junit.IdeaTestRunner$Repeater$1.execute(IdeaTestRunner.java:38)
	at com.intellij.rt.execution.junit.TestsRepeater.repeat(TestsRepeater.java:11)
	at com.intellij.rt.junit.IdeaTestRunner$Repeater.startRunnerWithArgs(IdeaTestRunner.java:35)
	at com.intellij.rt.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:235)
	at com.intellij.rt.junit.JUnitStarter.main(JUnitStarter.java:54)
Caused by: org.springframework.beans.ConversionNotSupportedException: Failed to convert property value of type 'java.lang.String' to required type 'java.util.Date' for property 'birthday'; nested exception is java.lang.IllegalStateException: Cannot convert value of type 'java.lang.String' to required type 'java.util.Date' for property 'birthday': no matching editors or conversion strategy found
	at org.springframework.beans.AbstractNestablePropertyAccessor.convertIfNecessary(AbstractNestablePropertyAccessor.java:595)
	at org.springframework.beans.AbstractNestablePropertyAccessor.convertForProperty(AbstractNestablePropertyAccessor.java:609)
	at org.springframework.beans.BeanWrapperImpl.convertForProperty(BeanWrapperImpl.java:219)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.convertForProperty(AbstractAutowireCapableBeanFactory.java:1756)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.applyPropertyValues(AbstractAutowireCapableBeanFactory.java:1712)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.populateBean(AbstractAutowireCapableBeanFactory.java:1452)
	at org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory.doCreateBean(AbstractAutowireCapableBeanFactory.java:619)
	... 38 more
Caused by: java.lang.IllegalStateException: Cannot convert value of type 'java.lang.String' to required type 'java.util.Date' for property 'birthday': no matching editors or conversion strategy found
	at org.springframework.beans.TypeConverterDelegate.convertIfNecessary(TypeConverterDelegate.java:262)
	at org.springframework.beans.AbstractNestablePropertyAccessor.convertIfNecessary(AbstractNestablePropertyAccessor.java:590)
	... 44 more
```

可以清楚的看到

> Cannot convert value of type 'java.lang.String' to required type 'java.util.Date' for property 'birthday': no matching editors or conversion strategy found

spring不能自己将string类型的日期格式转换为Date格式

**作为spring来讲，各个地方的人的日期表现格式都是不一样的，没有办法统一定义，所以需要程序员自己去定义类型转换器**

## 自定义类型转换器

上面已经说明了自定义类型转换器的原因和意义，下面介绍如何去自定义类型转换器！

实现步骤：

- 实现convert接口
- 在spring的配置文件中对自定义的类型转换器进行注册

类型转换器

```java
package indi.exer.yuluo.converter;

import org.springframework.core.convert.converter.Converter;

import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Date;

/**
 * @author: yuluo
 * @createTime: 2021/11/14 20:10
 * @Description: 自定义日期类型转换器
 */

public class MyDateConverter implements Converter<String, Date> {

    // 对字符串进行依赖注入
    private String pattern;

    public String getPattern() {
        return pattern;
    }

    public void setPattern(String pattern) {
        this.pattern = pattern;
    }

    /**
     * converter方法作用：String  --> Date
     * SimpleDateFormat sdf = new SimpleDateFormat();
     * sdf.parset(String) --> Date
     * param: source代表的是 配置文件中的 日期字符串 <value> 2021-11-14 </value>
     * return: 转换好的Date作为方法的返回值，Spring会自动的为birthday属性注入
     * 在spring中创建出来这个对象
     * 转换器注册
     */

    @Override
    public Date convert(String source) {
        Date parse = null;
        SimpleDateFormat simpleDateFormat = (SimpleDateFormat) new SimpleDateFormat(pattern);
        try {
            parse = simpleDateFormat.parse(source);
        } catch (ParseException e) {
            e.printStackTrace();
        }
        return parse;
    }
}
```

xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context" xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--创建myDateConverter转换器对象-->
    <bean id="myDateConverter" class="indi.exer.yuluo.converter.MyDateConverter">
        <property name="pattern" value="yyyy-MM-dd"/>
    </bean>
    
    <!--用于注册类型转换器-->
    <!--注册的id命名是限定的converterService-->
    <!--spring内置的日期类型转换器为 ”2021/11/14“ 当日期格式为这种时，会自己转换！-->
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
        <property name="converters">
            <set>
                <ref bean="myDateConverter"/>
            </set>
        </property>
    </bean>

    <bean id="person" class="indi.exer.yuluo.converter.People" >
        <property name="name" value="yuluo"/>
        <property name="birthday" value="2021-11-14"/>
    </bean>


</beans>
```

效果展示

```mark
2022-10-16 15:48:41 DEBUG ClassPathXmlApplicationContext:629 - Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@4c203ea1
2022-10-16 15:48:41 DEBUG XmlBeanDefinitionReader:393 - Loaded 3 bean definitions from class path resource [applicationContext2.xml]
2022-10-16 15:48:41 DEBUG DefaultListableBeanFactory:225 - Creating shared instance of singleton bean 'conversionService'
2022-10-16 15:48:41 DEBUG DefaultListableBeanFactory:225 - Creating shared instance of singleton bean 'myDateConverter'
2022-10-16 15:48:41 DEBUG DefaultListableBeanFactory:225 - Creating shared instance of singleton bean 'person'
people = indi.exer.yuluo.converter.People@7c417213
people.getBirthday() = Sun Nov 14 00:00:00 CST 2021
```

