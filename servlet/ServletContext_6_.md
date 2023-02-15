# ServletContext

## ServletContext是什么

- 是servlet规范中的一员
- 由Tomcat服务器（web容器）实现的，
- 在tomcat服务器启动时实例化对象
- 对于一个webapp来说，Servletcontext对象只有一个，即一个webapp只有一个servletContext
- 在服务器关闭的时候销毁对象
- 所以ServletContext为应用级对象

## 怎么理解

> 是Servlet对象的上下文对象，环境对象的意思
>
> ServletContext对应的是整个web.xml配置

- ***注意：一个Servlet对象对应一个ServletConfig，100个Servlet对象对应100个ServletConfig对象，但是只要在同一个webapp当中，所有的servlet对象都只对应一个ServletContext对象***

## SErvletContext里面常用的方法

```java
public String getInitParamerter(String name);   // 通过初始化参数的name获取value

public Enumeration<String> getInitParameterNames(); //获取所有初始参数的name

// 以上的方法获取的是全局的配置信息
    <!--上下文的配置信息  以下的这些信息可以通过ServletContext对象获取-->
    <context-param>
        <param-name>pageSize</param-name>
        <param-value>10</param-value>
    </context-param>
    <context-param>
        <param-name>startIndex</param-name>
        <param-value>0</param-value>
    </context-param>
```

```java
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <!--上下文的配置信息  以下的这些信息可以通过ServletContext对象获取-->
    <context-param>
        <param-name>pageSize</param-name>
        <param-value>10</param-value>
    </context-param>
    <context-param>
        <param-name>startIndex</param-name>
        <param-value>0</param-value>
    </context-param>

    <servlet>
        <servlet-name>aservletcontext</servlet-name>
        <servlet-class>indi.yuluo.javaweb.servlet.Aservlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>aservletcontext</servlet-name>
        <url-pattern>/aservletcontext</url-pattern>
    </servlet-mapping>

    <servlet>
        <servlet-name>bservletcontext</servlet-name>
        <servlet-class>indi.yuluo.javaweb.servlet.Bservlet</servlet-class>
    </servlet>
    <servlet-mapping>
        <servlet-name>bservletcontext</servlet-name>
        <url-pattern>/bservletcontext</url-pattern>
    </servlet-mapping>

</web-app>
```

在<servlet>外面配置<context-param>和在里面配置<init-param>的时机：

- 如果配置的范围是当前的servlet对象，只需要在<ervlet>里面配置，使用ServletConfig对象获取
- 如果想要此配置全局有效。就在<servlet>外部配置，为应用级的配置

## 获取应用的根路径

获取应用的根路径非常重要，在java中代码的一些地方会用到这个项目的根路径，这个方法可以动态的获取到这个应用的根路径，不要再java代码中将应用的根路径写死，不清楚应用在最终部署的时候，会起一个什么名字

```java
public String getContextPath();

// String contextPath = application.getContextPath();
```

## 获取文件的绝对路径

```java
// 获取某个文件的绝对路径（根路径）
String realPath = application.getRealPath("/index.html");
out.print(realPath + "</br>");
```

## 日志

```java
// 日志记录
// 会记录到catalina_home里的logs的目录下
// 利用IDEA，会创建到IDEA工具里面的tomcat副本下面的logs的localhost-2021-01-10.log里面
// C:\Users\14815\AppData\Local\JetBrains\IntelliJIdea2021.3\tomcat\d83a9e49-0b17-4aa0-8471-fb0ef1873a26\logs
application.log("正在学习java web……");
```

Tomcat服务器的日志：

- catalina.2022-01-10.log (服务器)  服务器端的java程序运行的控制台的信息
- localhost.2022-01-10.log   ServletContext对象的log方法记录的日志信息在这个文件里
- localhost_access_log.2022-01-10.log  访问日志，当你的项目开始部署到阿里服务器上面这个东西就很重要

## 应用域

- ServletContext对象还有另外一个名字，应用域（后面还有其他域，请求域，会话域）
- 如果所有的用户共享一份数据，并且这个数据很少被修改，数据量很小，则可以将这些数据放到ServletContext这个应用域中，
- 为什么是所有用户共享的数据？不是共享的数据没有意义，因为ServletContext这个对象只有一个，只有共享的数据放进去才有意义
- 为什么数据量要小？因为数据量比较大的话，太占用堆内存，并且这个对象的声明周期比较长，服务器关闭的时候，这个对象才会被销毁，大数据量会影响这个服务器的性能
- 为什么说这些共享数据量很少被修改，或者几乎不被修改？所有用户共享的数据，如果修改，必然存在线程并发所带来的安全问题，所以一般在应用域中的数据一般都是只读的
- 数据量小，所有用户共享，又不修改，这样的数据放到ServletContext对象中，会大大提升效率，因为应用域相当于一个缓存，放到缓存中的数据，下次在用的时候，不需要从数据库中去获取，大大提升运行效率

> 1. 存```public void setAttribute(String name, Object value)```   //map.put(k,  v)
> 2. 取```public Object getAttribute(String name, Object value)```  //Object v = map.get(k)
> 3. 删```public void removeAttribute(String name, Object value)```  // map.remove(k)

```java
		Aservlet:
		// 准备数据
        User yuluo = new User("yuluo", "12");
        // 向ServletContext应用域中存数据
        application.setAttribute("userObj", yuluo);
        // 取数据
        Object userObj = application.getAttribute("userObj");
        // 输出到浏览器
        out.print(userObj + "</br>");

		Bservlet:
        // 取出在Aservlet中写入的数据
        Object userObj = application.getAttribute("userObj");
        out.print(userObj + "</br>");

        // 先获取Attribute的名字，在输出value
        Enumeration<String> attributeNames = application.getAttributeNames();
        while (attributeNames.hasMoreElements()) {
            String name = attributeNames.nextElement();
            Object value = application.getAttribute(name);
            out.print(name + " = " + value + "</br>");
        }
```

