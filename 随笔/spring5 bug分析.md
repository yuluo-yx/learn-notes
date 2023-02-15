# spring5 bug分析

## 问题复述

在一次使用`ssm`开发的时候，发现了此bug

```java
public class TestGetBeanProductTypeServiceImpl {

    /**
     *  用于测试：
     */
    @Test
    public void test() {
        ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("spring/application-*.xml");

        String[] beanDefinitionNames = classPathXmlApplicationContext.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            System.out.println("beanDefinitionName = " + beanDefinitionName);
        }

        ProductTypeService productTypeServiceImpl = (ProductTypeService) classPathXmlApplicationContext.getBean("ProductTypeServiceImpl");

        List<ProductType> all = productTypeServiceImpl.getAll();
        all.forEach(type -> System.out.println(type.toString()));
    }

}
```

代码如上所示：

我的配置文件：

![image-20220608174000883](E:\Typora笔记\java framework\mybatisplus\images\image-20220608174000883.png)

在获取`productTypeServiceImpl`的时候，第一次出现了异常

```xml
org.springframework.beans.factory.NoSuchBeanDefinitionException: No bean named 'ProductTypeServiceImpl' available

	at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBeanDefinition(DefaultListableBeanFactory.java:808)
	at org.springframework.beans.factory.support.AbstractBeanFactory.getMergedLocalBeanDefinition(AbstractBeanFactory.java:1279)
	at org.springframework.beans.factory.support.AbstractBeanFactory.doGetBean(AbstractBeanFactory.java:297)
	at org.springframework.beans.factory.support.AbstractBeanFactory.getBean(AbstractBeanFactory.java:202)
	at org.springframework.context.support.AbstractApplicationContext.getBean(AbstractApplicationContext.java:1108)
	at indi.yuluo.test.TestGetBeanProductTypeServiceImpl.test(TestGetBeanProductTypeServiceImpl.java:31)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.junit.runners.model.FrameworkMethod$1.runReflectiveCall(FrameworkMethod.java:50)
	at org.junit.internal.runners.model.ReflectiveCallable.run(ReflectiveCallable.java:12)
	at org.junit.runners.model.FrameworkMethod.invokeExplosively(FrameworkMethod.java:47)
	at org.junit.internal.runners.statements.InvokeMethod.evaluate(InvokeMethod.java:17)
	at org.junit.runners.ParentRunner.runLeaf(ParentRunner.java:325)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:78)
	at org.junit.runners.BlockJUnit4ClassRunner.runChild(BlockJUnit4ClassRunner.java:57)
	at org.junit.runners.ParentRunner$3.run(ParentRunner.java:290)
	at org.junit.runners.ParentRunner$1.schedule(ParentRunner.java:71)
	at org.junit.runners.ParentRunner.runChildren(ParentRunner.java:288)
	at org.junit.runners.ParentRunner.access$000(ParentRunner.java:58)
	at org.junit.runners.ParentRunner$2.evaluate(ParentRunner.java:268)
	at org.junit.runners.ParentRunner.run(ParentRunner.java:363)
	at org.junit.runner.JUnitCore.run(JUnitCore.java:137)
	at com.intellij.junit4.JUnit4IdeaTestRunner.startRunnerWithArgs(JUnit4IdeaTestRunner.java:69)
	at com.intellij.rt.junit.IdeaTestRunner$Repeater$1.execute(IdeaTestRunner.java:38)
	at com.intellij.rt.execution.junit.TestsRepeater.repeat(TestsRepeater.java:11)
	at com.intellij.rt.junit.IdeaTestRunner$Repeater.startRunnerWithArgs(IdeaTestRunner.java:35)
	at com.intellij.rt.junit.JUnitStarter.prepareStreamsAndStart(JUnitStarter.java:235)
	at com.intellij.rt.junit.JUnitStarter.main(JUnitStarter.java:54)
```

**第一次出现之后，我认为是bean id的问题，定义了bean id之后，同样是此问题，后来对比了配置文件之后，发现问题所在**

正确代码：

```java
ClassPathXmlApplicationContext classPathXmlApplicationContext = new ClassPathXmlApplicationContext("spring/applicationContex-*.xml");
```

## 问题分析

debug展开对问题的分析：

配置文件错误应该报出的是FileNotFoundException异常，为什么会No bean name异常？

经过分析，在深入了spring的源码之后

![image-20220608174701899](E:\Typora笔记\java framework\mybatisplus\images\image-20220608174701899.png)

在上下文环境配置的源码中`org.springframework.context.support.AbstractApplicationContext`

发现问题

此代码缺少对parent为空的处理，我认为此处应该报出一个`FileNotFoundException`异常， 而不是直接执行下一步。

## 反馈spring官方

此问题已经反馈spring官方

问题链接：https://github.com/spring-projects/spring-framework/issues/28584

