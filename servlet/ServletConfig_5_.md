# ServletConfig

*****

## ServletConfig是什么

```jakarta.Servlet.ServletConfig```

是Servlet规范中的一员，是一个接口

## 谁实现了这个接口

```java
ServletcConfig对象org.apache.catalina.core.StandardWrapperFacade@1d86fb92

这个类实现了ServletConfig接口
Tomcat服务器实现了这个接口
public class org.apache.catalina.core.StandardWrapperFacade implements ServletConfig() {}
```

## servlet和ServletConfig对象之间的创建关系

一一对应

ServletConfig由Tomcat服务器创建，一个Servlet对应一个ServletConfig对象

在创建Servlet的同时创建ServletConfig对象

### 作用：

ServletConfig为Servlet的配置信息对象

即在web.xml里面的配置信息，包装的信息是：(servlet标签里面的信息：)

```xml
    <servlet>
        <servlet-name>servletconfig</servlet-name>
        <servlet-class>indi.yuluo.javaweb.servlet.ConfigTestServlet</servlet-class>
    </servlet>
```



## ServlerConfig里的方法

public String getInitParameter(String name);

public Enumeration<String> getInitParameterNames();

public ServletContext getServletContext();

public String getServletName();

***以上的方法在自己编写的Servllet里面也可以用this调用，因为他继承了GenericServlet***

```xml
<servlet>
        <servlet-name>servletconfig</servlet-name>
        <servlet-class>indi.yuluo.javaweb.servlet.ConfigTestServlet</servlet-class>
        <!--这里是一个Servlet对象的初始化参数配置信息。会自动被tomcat解析，放到ServletConfig对象中-->
        <init-param>
            <param-name>driver</param-name>
            <param-value>com.mysql.cj.jdbc.Driver</param-value>
        </init-param>
        <init-param>
            <param-name>url</param-name>
            <param-value>jdbc:localhost:8080/jdbc</param-value>
        </init-param>
        <init-param>
            <param-name>user</param-name>
            <param-value>root</param-value>
        </init-param>
        <init-param>
            <param-name>password</param-name>
            <param-value>w082916x</param-value>
        </init-param>
    </servlet>
```



```java
// 获取web.xml中的初始化参数信息
        Enumeration<String> initParameterNames = servletConfig.getInitParameterNames();
        // 迭代器输出集合
        Iterator it= initParameterNames.asIterator();
        while(it.hasNext()) {
            // 获取下一个元素
            String paramName = initParameterNames.nextElement();
            out.print("paramName = " + paramName + "</br>");
        }
        // 获取value
        // 通过初始化参数的name获取value
        out.print(servletConfig.getInitParameter("driver"));
        out.print(servletConfig.getInitParameter("url"));
        out.print(servletConfig.getInitParameter("user"));
        out.print(servletConfig.getInitParameter("password"));

        /*
        获取Servlet对象的初始化参数，可以不用获取ServletConfig对象
        因为ServletConfig实现了GenericServlet接口，继承了GenericServlet里面的方法，所以
        用this调用getParameterNames是一样的。

        Enumeration<String> initParameterNames1 = this.getInitParameterNames();
        while(initParameterNames1.hasMoreElements()) {
            String name = initParameterNames1.nextElement();
            String value = this.getInitParameter("initParameterNames1");
            System.out.println(name + " = " + value);
        }*/


        System.out.print(this.getInitParameter("driver"));
        System.out.print(this.getInitParameter("url"));
        System.out.print(this.getInitParameter("user"));
        System.out.print(this.getInitParameter("password"));
```
