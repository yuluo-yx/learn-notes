# java servlet的过滤器 Filter

- 在tomcat10之前，需要处理乱码问题就需要使用到过滤器，过滤乱码
- 在处理自己的业务时，都需要判断用户有没有登录，登录了才可以继续操作！这段判断用户是否登录的代码时固定的，每一个servlet类都需要编写，并没有达到复用。
- **可以使用servlet规范中的Filter过滤器来解决问题**

## 原理

![过滤器实现原理](E:\Typora笔记\java framework\mybatisplus\images\过滤器实现原理.png)

## 作用

Filter可以在Servlet这个目标程序执行之前添加代码，也可以在目标servlet代码之后添加代码，都可以添加过滤规则

一般情况下都是在过滤器中编写公共代码

## 使用

1. 编写一个java类实现一个接口：`jakarta.servlet.Filter` ,并且实现这个接口中的所有方法

   ```java
   package indi.yuluo.javaweb.servlet;
   
   import jakarta.servlet.*;
   import jakarta.servlet.annotation.WebFilter;
   
   import java.io.IOException;
   
   @WebFilter("*.do")
   public class Filter2 implements Filter {
       @Override
       public void init(FilterConfig filterConfig) throws ServletException {
       }
   
       @Override
       public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
               throws IOException, ServletException {
           System.out.println("Filter2 doFilter begin");
           // 执行下一个过滤器，如果不是过滤器执行下一个servlet
           chain.doFilter(request, response);
           System.out.println("Filter2 doFilter end");
       }
   
       @Override
       public void destroy() {
       }
   }
   ```

   1. init方法：在Filter对象第一次被创建之后调用，并且只调用一次
   2. doFilter方法：只要用户发送一次请求，则执行一次，发送N次请求，则执行N次，在这个方法中编写过滤规则
   3. destory方法：在Filter对象被销毁/释放前调用，并且只调用一次

2. 在web.xml中对Filter进行配置，类似于servlet的配置

   1. ```xml
      <filter>
          <filter-name>filter2</filter-name>
          <filter-class>com.bjpowernode.javaweb.servlet.Filter2</filter-class>
      </filter>
      <filter-mapping>
          <filter-name>filter2</filter-name>
          <url-pattern>*.do</url-pattern>
      </filter-mapping>
      ```

   

- 注意：
  - servlet对象默认情况下，在服务器启动的时候是不是新建对象的
  - Filter对象默认情况下，在服务器启动的时候会新建对象
  - Servlet是单例的，Filter也是单例的
  - **Filter的优先级高于servlet**
- 目标servlet是否执行取决于来个条件：
  1. 在过滤器中是否编写了`chain.doFilter(request, reqponse);`代码
     - 代码的作用：执行下一个过滤器，如果没有过滤器了，执行目标serlvet
  2. 用户发送的请求路径是否和servlet的请求路径一致
- 过滤器的调用顺序，遵循栈数据结构
- 使用`@WebFilter`注解的时候，执行顺序是比较Filter类的类名，根据字典顺序排列
- Filter的生命周期：
  - 和Servlet对象的生命周期一致
  - 唯一的区别在于：Filter默认情况下，在服务器启动的阶段就实例化，而servlet不会

- Filter过滤器的最大优点
  - 在程序的编译阶段不会确定调用顺序，因为Filter的调用顺序是配置到web.xml文件中的，只要修改wen.xml中的配置filter-mapping的顺序就可以更改Filter的执行顺序，显然Filter的执行顺序是在程序运行阶段动态组合的，这种设计模式被称为**责任链设计模式**