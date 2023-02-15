# Spring 面向切面编程 AOP



## 介绍

**AOP：面向切面编程 （Aspect Oriented Programming）**

概念：

```mark
AOP (Aspect Oriented Programing)   面向切面编程 = spring动态代理开发
	以切面为基本单位的程序开发，通过切面间的彼此协同，相互调用来完成程序的构建
	切面 = 切入点 + 额外功能
	
OOP (Object Oriented Programing)   面向对象编程  java
	以对象为基本单位的程序开发，通过对象间的彼此协同，相互调用，完成程序的构建
	
POP (Producer Oriented Programing) 面向过程（方法，函数）编程 C
	以过程为基本单位的程序开发，通过过程间的彼此协同，相互调用， 来完成程序的构建
	
```



- 百度版

> 在软件业，AOP为Aspect Oriented Programming的缩写，意为：[面向切面编程](https://baike.baidu.com/item/面向切面编程/6016335)，通过[预编译](https://baike.baidu.com/item/预编译/3191547)方式和运行期间动态代理实现程序功能的统一维护的一种技术。AOP是[OOP](https://baike.baidu.com/item/OOP)的延续，是软件开发中的一个热点，也是[Spring](https://baike.baidu.com/item/Spring)框架中的一个重要内容，是[函数式编程](https://baike.baidu.com/item/函数式编程/4035031)的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的[耦合度](https://baike.baidu.com/item/耦合度/2603938)降低，提高程序的可重用性，同时提高了开发的效率。

## 为什么需要AOP编程？

- 在javaEE的分层开发中service是最重要的部分之一

- 在service层中包含了那些代码？

  - 核心业务：**代码量大，**
  - 附加功能：**代码量小（对比于核心业务代码），不属于核心业务，可有可无的******

  ```java
  public class XXXService {
      public void XXXServiceMethod() {
          附加功能
              事务
              日志
              性能
              
          核心业务
              业务 运算 + DAO操作
      }
  }
  
  ```

- 额外功能书写在service层中到底好不好？

  - 在service层的调用者的角度（controller层）：**事务**，决定了代码执行是否成功
  - 在软件设计者的角度：Service层不需要这些功能，造成代码不好维护！

- 如何解决这一个矛盾？

  - **引入代理类（中介），利用中介的省份添加额外功能，调用service的核心功能**

## 代理设计模式

### 概念

- 通过代理类，为原始类添加额外功能
- 好处：利于原始类的维护

### 代理开发的核心要素

代理类 -> 目标类（原始类）+ 额外功能 +  原始类（目标类）实现相同的接口

```mark
接口
public interface UserService{
	m1,
	m2
}

实现类
public class UserServiceImpl implements UserService{
	m1,  ---> 只负责实现业务运算 DAO调用
	m2
}

代理类
public class UserServiceProxy implements UserService{

}
```

### 编码

**静态代理：** **每一个原始类都会手工编写一个代理类**

```java
package indi.exer.yuluo.proxy;

/**
 * @author: yuluo
 * @createTime: 2021/11/18 19:34
 * @Description:
 *
 * proxy 代理类 ： 代理类 = 目标类(原始类) + 额外功能 + 原始类(目标类)实现相同的接口
 *
 */


// 实现相同的接口 implements UserService
public class UserServiceProxy implements UserService {

    // 原始对象
    private UserServiceImpl userService = new UserServiceImpl();

    // 重写一样的方法 实现原始的接口
    @Override
    public void register(User user) {
        // 额外功能的添加
        System.out.println("-------log-------");
        System.out.println(user);
    }

    @Override
    public boolean login(String name, String password) {

        System.out.println("-----log------");

        return userService.login(name, password);
    }
}
```

### 测试类

```java
/**
 * 用于测试：静态代理的代码
 */
@Test
public void text1() {
    UserService userService= new UserServiceProxy();
    userService.login("yuluo", "123456");

    System.out.println("------------");

    OrderService orderService = new OrderServiceProxy();
    orderService.showOrder();
}
```

### 静态代理的缺点：

- 静态文件数量过多，不利于项目管理
- 额外功能的维护性差

### Spring动态代理概念

- 概念：通过代理类为原始类增加额外功能
- 利用原始类（目标类）的维护

## AOP编程的开发步骤：

> 1. 原始对象
> 2. 额外功能 (MethodInterceptor)
> 3. 切入点
> 4. 组装切面 (额外功能+切入点)