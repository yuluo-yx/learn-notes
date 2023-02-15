# 适配器模式改造Servlet

********

## 编写一个Servlet类直接实现Servlet接口有什么缺点？

​	在编写Servlet的时候，使用最多的就是service方法和init，destory方法，ServletConfig和getServletInfo方法大部分情况不使用.

在开发过程中实现了Servlet接口，就要重写这5个方法，因为使用不到其他的两个方法，所以只能给一个空返回，造成了代码的不美观和代码量大

```java
package indi.yuluo.javaweb.Servlet;

import jakarta.servlet.*;

import java.io.IOException;

/**
 * @author: yuluo
 * @createTime: 2022/1/9 11:19
 * @File : Bservlet.java
 * @Software : IntelliJ IDEA
 * @Description:
 */

public class Bservlet implements Servlet {


    // 利用无参的构造方法判断Tomat是否新建了Servlet对象
    public Bservlet() {
        System.out.println("Bservlet无参构造方法执行了");
    }

    @Override
    public void init(ServletConfig servletConfig) throws ServletException {

    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {

    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}
```

## 解决方法，适配器设计模式

> 手机不能直接插到220V的电源上，电池容易烧毁，就需要一个充电器来进行转换。充电器便是适配器设计

```java
package indi.yuluo.javaweb.adapter;

public interface MyInterface {

    void m1();

    void m2();

    void custmoerService();

    void m3();

    void m4();

    void userService();

}

package indi.yuluo.javaweb.adapter;

/**
 * @author: yuluo
 * @createTime: 2022/1/9 17:05
 * @File : UserAdapter.java
 * @Software : IntelliJ IDEA
 * @Description:
 */

public abstract class UserAdapter implements MyInterface{

    @Override
    public void m1() {

    }

    @Override
    public void m2() {

    }

    @Override
    public void custmoerService() {

    }

    @Override
    public void m3() {

    }

    @Override
    public void m4() {

    }

    public abstract void userService();
}

package indi.yuluo.javaweb.adapter;

/**
 * @author: yuluo
 * @createTime: 2022/1/9 17:05
 * @File : UserService.java
 * @Software : IntelliJ IDEA
 * @Description:
 */

public class UserService extends UserAdapter{

    /*
    对于UserService来说，不需要实现MyInterface中的所有方法，只需要实现UserService方法即可，
    如果实现了所有的放方法，对于当前的UserService来说，代码不够优雅，不够美观
    所以编写了userAdapter，user的适配器来解决这个问题，只需要继承这个适配器即可,
     */

    @Override
    public void userService() {

    }

}
```

## GenericServlet

Grneric是一个抽象类，有一个抽象方法service

以后写servlet代码只需要继承GenericServlet，重写里面的service方法即可

```java
package indi.yuluo.javaweb.Servlet;

import jakarta.servlet.*;

import java.io.IOException;

/**
 * @author: yuluo
 * @createTime: 2022/1/9 17:15
 * @File : GenericServlet.java
 * @Software : IntelliJ IDEA
 * @Description:  通用的Servlet的适配器
 */

public abstract class GenericServlet implements Servlet {

    @Override
    public void init(ServletConfig servletConfig) throws ServletException {

    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    /**
     * 这个方法在Servlet中经常使用，所以要求子类必须实现service方法
     * @param servletRequest
     * @param servletResponse
     * @throws ServletException
     * @throws IOException
     */
    public abstract void service(ServletRequest servletRequest, ServletResponse servletResponse)
            throws ServletException, IOException;

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}


package indi.yuluo.javaweb.Servlet;

import jakarta.servlet.ServletException;
import jakarta.servlet.ServletRequest;
import jakarta.servlet.ServletResponse;

import java.io.IOException;

/**
 * @author: yuluo
 * @createTime: 2022/1/9 17:19
 * @File : LoginServlet.java
 * @Software : IntelliJ IDEA
 * @Description:
 */

public class LoginServlet extends GenericServlet{
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("正在处理用户请求，请稍后。。。。。。");
    }
}

```

## 改造GenericServlet

- 第一个问题：提供了GenericServle方法之后，init方法还会继续执行吗？

  -   	会，会执行GenericServlet里面的inti方法，

- init方法和init方法中的ServletConfig对象，是谁创建的，谁传过来的

  - Tomcat干的，
  - Tomcat服务器先创建了Servletconfig对象，然后将ServletConfig对象传给了inti方法

- 输出ServletConfig对象如下：

  - > ```java
    > ServletConfig对象org.apache.catalina.core.StandardWrapperFacade@7d5a4458
    > ```

- tomcat已经为我们封装了GenericServlet

