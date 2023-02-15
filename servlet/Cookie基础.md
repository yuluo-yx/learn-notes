# Cookie基础

## cookie介绍

### session的实现原理中，每一个session对象都会关联一个sessionid。例如

1. JSESSIONID=53484JDFFJ9549N
2. 以上这个键值对数据其实就是cookie对象
3. 对于session关联的cookie来说，这个cookie是被保存到“浏览器的运行内存当中”
4. 只要浏览器不关闭，用户再次发送请求时，会自动将运行内存中的cookie发送给服务器
5. 服务器就是根据53484JDFFJ9549N来找到对应的session对象的

### cookie怎么生成，保存到什么地方，有什么用，浏览器什么时候发送cookie？

1. cookie最终保存到浏览器客户端上的，
   - 可以保存到浏览器的运行内中（浏览器只要关闭cookie就消失了）
   - 也可以保存到硬盘文件中 （永久保存）
2. cookie作用
   - cookie和session机制都是为了保存会话的状态
   - cookie是将会话的状态保存到浏览器客户端上
   - session是将会话状态保存到服务器端上
3. 为什么要有cookie和session机制
   - 因为http协议是无状态，无连接协议

## cookie经典案例

> 京东商城中，在未登录的情况下，向购物车中放几件商品，然后关闭商城，再次打开浏览器，访问京东商城的时候，购物车中的商品还在，这是怎么做的？为什么我没有登录，购物车中的还有商品呢？

- 将购物车中的商品编放到cookie中，cookie保存在硬盘文件中，这样即使关闭浏览器，硬盘上的cookie还在，下一次再打开京东商城的时候，查看购物车的时候，会自动读取本地硬盘中存储的cookie，拿到商品编号，动态展示购物车中的商品
- 这种方式存储的cookie信息，当cookie被清除删掉之后，购物车中的商品数据也会被清除

> 126邮箱中有一个功能，十天内免登录

- 这个功能也是需要cookie实现的
  - 用户输入正确的用户名和密码之后，并且同时选择十天内登录，登录成功之后，浏览器客户端会保存一个cookie，这个cookie中保存了用户名和密码等信息，这个cookie是保存在硬盘文件中的，十天有效，在十天内用户再次登录访问126的时候，浏览器会自动提交126的关联coolie给服务器，在服务器接受到了cookie之后，获取用户名和密码，验证通过之后，自动登录

- 让cookie失效的方法
  - 十天过后自动失效
  - 更改密码
  - 清除cookie

**Http协议中规定：cookie的形式就是name和value组成的，都是字符串类型的**

## cookie在java中的实现

jakarta对cookie的支持：`jakarta.servlet.http.cookie`

java程序怎么把cookie数据发送给浏览器呢？`response.addCookie(cookie)`

### 生成cookie

```java
package com.bjpowernode.javaweb.servlet;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

@WebServlet("/cookie/generate")
public class GenerateCookie extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        // 创建Cookie对象
        Cookie cookie = new Cookie("productid", "12345645623145612");
        Cookie cookie2 = new Cookie("username", "zhangsan");

        // 设置cookie在一小时之后失效。（保存在硬盘文件当中）
        //cookie.setMaxAge(60 * 60);
        // 设置cookie的有效期为0，表示该cookie被删除。主要应用在：使用这种方式删除浏览器上的同名cookie。
        //cookie.setMaxAge(0);
        // 设置cookie的有效期 < 0，表示该cookie不会被存储。（表示不会被存储到硬盘文件中。会放在浏览器运行内存当中。）
        cookie.setMaxAge(-1); // 和不调用sexMaxAge是同一个效果。
        cookie2.setMaxAge(-1);

        // 默认情况下，没有设置path的时候，cookie关联的路径是什么？
        //cookie.setPath("/servlet13");
        cookie.setPath(request.getContextPath());
        cookie2.setPath(request.getContextPath());

        // 将cookie响应到浏览器
        response.addCookie(cookie);
        response.addCookie(cookie2);
    }
}
```

### 获取cookie

```java
package com.bjpowernode.javaweb.servlet;

import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebServlet;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

@WebServlet("/sendCookie")
public class ReceiveCookie extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {
        // 通过java程序怎么接收到浏览器发送过来的cookie呢？
        // 当然通过request对象了。（返回值是一个数组，因为浏览器可能会提交多个cookie给服务器。）
        // 注意细节：这个方法可能会返回null。如果浏览器没有提交cookie，这个方法返回值是null，并不是返回一个长度为0的数组。
        Cookie[] cookies = request.getCookies();

        // 如果不是null，表示一定有cookie
        if (cookies != null) {
            // 遍历数组
            for (Cookie cookie : cookies) {
                // 获取cookie的name和value
                String name = cookie.getName();
                String value = cookie.getValue();
                System.out.println(name + "=" + value);
            }
        }

    }
}
```

## cookie的path，cookie关联的路径

- 假设现在发送的请求路径是“http://localhost:8080/servlet/cookie/geneate”生成的cookie，如果cookie没有设置path，默认的path是什么？
  - 默认的路径是：http://localhost:8080/servlet/cookie 以及他的子路径
  - 以后只要浏览器的请求路径是http://localhost:8080/servlet/cookie路径以及他的子路径，cookie都会被发送到服务器

