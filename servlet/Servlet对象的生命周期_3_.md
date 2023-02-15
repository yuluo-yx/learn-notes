# Servlet对象的生命周期

******

## servlet对象的生命周期是什么

- Servlet对象什么时候被创建
- Servlet对象什么时候被销毁
- Servlet对象创建了几个
- Servlet对象的生命周期表示一个Servlet对象从创建到销毁的过程

## Servle对象是由谁来维护的

- 是由Tomcat来维护的，对象上的方法的调用，最终的销毁，java web的开发者无权干涉

- Tomcat服务器又被称为web容器（web container）

- web容器来管理servlet的生命周期

- > 自己创建的servlet对象不受web容器的管理
  >
  > web容器创建的Servlet对象都会被放到一个HashMap里面，只有这个HashMap集合中的Servlet
  >
  > 对象只能被web容器管理

- web容器底层使用HashMap这样的一个集合来管理servlet对象（value）和请求路径（key）之间的关系的

经过测试之后得出结论：

> 服务器在启动的时候并不会执行servlet的无参构造方法，说明tomcat服务器在启动的时候没有新建Servlet对象，并不会实例化Servlet对象
>
> **只有在访问此连接的时候才会读取web.xml的配置，创建servlet对象**
>
> 为了内存考虑，如果servlet对象一直没有访问，则此servlet对象没有用处，创建对象会浪费资源

**可以在tomcat服务器启动的时候就创建servlet对象，需要在web.xml配置文件中加一个子标签**,在启动的时候加载这个servlet对象

```xml
<load-on-startup>0</load-on-startup>
```

参数代表优先级，数字越小，则创建servlet对象的顺序越前

缺省值就是不实例化servlet对象

## Servlet对象的生命周期：

tomcat服务器在启动的时候，后台的响应如下：

```java
Aservlet无参构造方法执行了
Aservlet`s init mothod execute!
aservlet`s service method execute!
```

分析以上我们得知：

- 用户在一次发送请求的时候，tomcat服务器会实例化servlet对象，（Tomcat服务器通过反射的方法，调用AServlet的构造方法执行，并且执行的是无参的构造方法）

- ASeevlet对象被创建出来之后，Tomcat服务器会调用init方法，（init方法在执行的时候，Aservlet对象已经被创建出来了）

- 用户发送了请求之后，init方法执行之后，Tomcat服务器马上调用AServlet的service方法

- 用户第二次发送请求，tomcat服务器继续执行service方法

  > aservlet`s service method execute!

- 用户在发送第3，4次请求的时候，服务器并没有在继续实例化servlet对象，而是执行service方法，说明

  > 1. Servlet对象是单例的，（单实例的。但是Servlet类并不符合单例设计模式，称为假单例，之所以单例是因为servlet对象的创建时tomcat服务器管的，程序员管不着，Tomcat只创建了一个，所以导致了单例，但是属于假单例，真单例模式：构造方法是私有的）
  > 2. init方法只被tomcat方法执行一次，在用户第一次发送请求时执行

关闭服务器的时候，控制台输出了一下内容，

> aservlet`s destory method execute!

可见，destory方法也只执行一次，在tomcat服务器关闭的时候执行，

- 在服务器福安比的时候要销毁AServlet对象的内存

- 服务器在销毁对象之前会调用AServlet的destory方法

  > 因为destory不是静态方法，所以在destory方法执行的时候Servlet对象还存在，destory方法执行之后，AServlet对象的内存才会被Tomcat服务器释放掉

## Servlet构造方法

当程序员在Servlet类中编写了一个有参数的构造方法，如果没有手动编写无参数的构造方法会出现什么问题

>  报500错误（服务器内部错误）
>
> 一般是因为服务端的java代码出现异常

***所以：不建议java web程序员自己去手写构造方法，如果定义不当，很容易让无参构造方法消息，可能会导致无法实例化servlet对象*，所以init方法的存在是很有必要的！**

Servlet的构造方法是在对对象进行第一次创建的时候执行，并且只执行一次，init方法也是在对象第一次创建的时候执行，并且只执行一次

## 使用Servlet中个方法的时机

在Servlet中使用最多的是service方法

> service方法是处理用户请求的核心方法

init方法很少使用：

> init方法当中做初始化操作，并且这个操作只需要做一次，例如：初始化数据连接池，初始化线程池

destory方法也很少用：

> 通常在destory方法中进行资源的关闭，意味这个对象马上就要销毁了，还有什么是没关闭的，抓紧时间关闭资源，进行保存

