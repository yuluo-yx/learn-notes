# servlet的注解式开发

- 第一个注解

  - jakarta.servlet.annotation.webServlet
    标注此类是一个servelt

  - > 默认属性：
    >
    > 1. name：用来指定servlet的名字，等同于web.xml中的<servlet-name>
    > 2. urlPatterns：是一个数组，用来指定servlet的映射路径，相当于web.xml中的<url-pattern>
    > 3. loadOnStartup：用来指定在服务器启动阶段是否加载该servlet，等同于<load-on-sratup>
    > 4. initParams： 用来指定servlet的初始化参数
    > 5. 当注解的属性名是value的时候可以省略，

    ```java
    @WebServlet(name = "hello",
            urlPatterns = {"/hello1", "/hello2", "/hello3"},
            //loadOnStartup = 1,
            initParams = {@WebInitParam(name = "username", value = "root"), @WebInitParam(name = "password", value = "123")})
    public class HelloServlet extends HttpServlet {    
    ```

    

  - ```java
    package com.bjpowernode.javaweb.servlet;
    
    import jakarta.servlet.ServletException;
    import jakarta.servlet.annotation.WebServlet;
    import jakarta.servlet.http.HttpServlet;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    
    import java.io.IOException;
    import java.io.PrintWriter;
    
    //@WebServlet(urlPatterns = {"/welcome1", "/welcome2"})
    // 注意：当注解的属性是一个数组，并且数组中只有一个元素，大括号可以省略。
    //@WebServlet(urlPatterns = "/welcome")
    // 这个value属性和urlPatterns属性一致，都是用来指定Servlet的映射路径的。
    //@WebServlet(value = {"/welcome1", "/welcome2"})
    // 如果注解的属性名是value的话，属性名也是可以省略的。
    //@WebServlet(value = "/welcome1")
    //@WebServlet({"/wel", "/abc", "/def"})
    @WebServlet("/wel")
    public class WelcomeServlet extends HttpServlet {
    
        @Override
        protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            response.setContentType("text/html;charset=UTF-8");
            PrintWriter out = response.getWriter();
            out.print("欢迎学习Servlet。");
        }
    }
    ```

- 当配置文件经常要改动的时候推荐使用web.xml的配置文件，如果配置文件不经常改动使用注解的方式

