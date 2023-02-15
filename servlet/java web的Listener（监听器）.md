# java web的Listener（监听器）



## 介绍

- 监听器是Servlet规范中的一员，就像Filiter一样，Filter也是Servlet规范中的一员，
- 在servlet中，所有的监听器接口都是以“Listener”结尾

## 作用

- 监听器实际上是Servlet规范留给我们javaweb程序源的特殊时机
- 特殊的时刻如果想执行这段代码，需要想到使用对应的监听器

## Servlet中提供的监听器

> jakarta.servlet包下
>
> - ServletContextListener
>
> - ServletContextAttributeListener
> - ServletRequestListener
> - ServletRequestAttributeListner
>
> jakarta.servlet.http包下
>
> - HttpSessionListener
> - HttpSessionAttributeListener
>   - 该监听器需要使用@WebListener注解标注
>   - 监听的是session域中数据的变化，只要数据发生变化，就会执行相应的方法，主要监测点为session对象
> - HttpSessionBindingListener
>   - 不需要@WebListener标注
>   - 假设User类实现了该监听器，那么User对象被放入sesson域的时候出发bind事件，User对象从session域中删除的时候，出发unbind事件
>   - 如果实体类没有实现该监听器的时候，将对象放入session域的时候不会触发bind事件
> - HttpSessionIdListener
>   - session对象的id发生改变的时候，监听器中的唯一一个方法就会被调用
> - HttpSessionActivationListener
>   - 监听session对象钝化和活化的过程的
>     - 钝化：session对象从内存存储到磁盘
>     - 活化：从硬盘文件恢复到内存中的过程

## 实现一个监听器的步骤，以ServletContextListener为例，

1. 编写一个类实现ServletContextListener接口

   ```java
   void contextInitialized(ServletContextEvent event)
   void contextDestroyed(ServletContextEvent event)    
   ```

2. 在web.xml里进行配置

   ```xml
   <listener>
       <listener-class>com.bjpowernode.javaweb.listener.MyServletContextListener</listener-class>
   </listener>
   ```

   也可以使用注解

   ```java
   @WebListener
   public class MyServletContextListener implements ServletContextListener {
   ```

3. 注意：

   所有监听器中的方法都不需要java程序员调用，都是服务器自己调用的，

   只在这个特殊时刻编写自己的代码即可

   ```java
   package indi.yuluo.javaweb.listener;
   
   import jakarta.servlet.ServletContextEvent;
   import jakarta.servlet.ServletContextListener;
   import jakarta.servlet.annotation.WebListener;
   
   // ServletContextListener监听器主要监听的是：ServletContext对象的状态。
   @WebListener
   public class MyServletContextListener implements ServletContextListener {
   
       /**
        * 监听器中的方法不需要程序员手动调用。是发生某个特殊事件之后被服务器调用。
        * @param sce
        */
       @Override
       public void contextInitialized(ServletContextEvent sce) { // 服务器启动时间点，想在这个时候执行一段代码，写就行了。
           // 现在这个特殊的时刻写代码，你写就是了。它会被服务器自动调用。
           // 这个方法是在ServletContext对象被创建的时候调用。
           System.out.println("ServletContext对象创建了。");
       }
   
       @Override
       public void contextDestroyed(ServletContextEvent sce) { // 服务器关闭时间点。
           // 现在这个特殊的时刻写代码，你写就是了。它会被服务器自动调用。
           // 这个方法是在ServletContext对象被销毁的时候调用。
           System.out.println("ServletContext对象被销毁了。");
       }
   }
   ```

   ```java
   package indi.yuluo.javaweb.listener;
   
   import jakarta.servlet.ServletRequestEvent;
   import jakarta.servlet.ServletRequestListener;
   import jakarta.servlet.annotation.WebListener;
   
   @WebListener
   public class MyServletRequestListener implements ServletRequestListener {
       // request对象销毁时间点
       @Override
       public void requestDestroyed(ServletRequestEvent sre) {
           System.out.println("request对象销毁了");
       }
   
       // request对象创建时间点
       @Override
       public void requestInitialized(ServletRequestEvent sre) {
           
           System.out.println("request对象初始化了");
       }
   }
   ```

## 业务场景：

**统计在线人数**

代表用户登录

- session.setAttribute("user", userObj); User类型的对象只要往session中存储过，表示有新用户登录

代表用户退出

- session.removeAttribute("user"); User类型的对象从session中移除了
- 或者有可能是session对象销毁了（超时了）
