# 关于B/S结构系统的会话机制（session机制）

## 会话

- 用户打开浏览器，进行一系列操作，最终关闭浏览器，这个整个过程叫做：一次会话（对应的java对象是session）
- 一次请求：用户在浏览器上面点击了一下之后，然后到页面停下来，可以粗略认为是一次请求（对应的java对象是request）
- **一个会话中包含多次请求**
- 在java的servlet规范中，session对应的类名是：HttpSession (jakarta.servlet.HttpSession)
- session机制是属于B/S结构的一部分，如果使用php开发项目，也存在session机制，session实际上是一种规范，在不同的语言中有不同的会话机制实现
- session对象的主要作用是：保存会话状态，（用户登录成功了之后，这是一种登录的状态，怎么保存登录成功的这个状态？利用session机制保存！）

## 为什么需要session对象来保存会话状态呢？

- 因为HTTP协议是一种无协议状态
- 无状态：请求的时候，B和S是连接的，但是请求结束之后，连接就断开了，这样做可以降低服务器的压力，请求的瞬间是连接的，请求结束连接断开，服务器的压力小
- 只要B/S的连接断开了之后，关闭浏览器的这个动作，服务器知道吗
  - 不知道，服务器是不知道浏览器关闭的

## 域概述

ServletContext对象是服务器启动的时候创建的，服务器关闭的时候销毁，这个ServletContext对象只有一个

request请求域（HttpServletRequest）、session会话域（HttpSession）、application应用域（ServletCntext）

**request < session < application**

## session操作

```java
// 获得session对象
HttpSession session = response.getSession();

/*
每个人获得的session对象都不同，在打开浏览器之后开启一次会话，打开每张页面时都是同一个session对象，对应的是n次request请求
*/

// 向session会话域中绑定数据
session.setAttribute();
// 从session会话域中取出数据
session.getAttribute();
```

## session对象的实现原理

session对象的内部实现原理是通过记号的方式

> 在web服务器中有一个session对象，类似于map集合，
>
> 这个map集合的key存储的是sessionid，value存储的是session对象
>
> 用户发送第一次请求的时候，服务器会创建一个新的session对象，同时给session对象生成一个id，然后web服务器会将session的id发送给浏览器，浏览器将session的id保存在浏览器的缓存中
>
> 用户发送第二次请求的时候，会自动将浏览器缓存中的session自动发送给服务器，服务器获取到session的id，然后从session列表中查找到对应的session对象

- 为什么关闭浏览器，会话结束

  > 关闭浏览器之后，浏览器中保存的sessionid消失，下次重新打开浏览器之后，浏览器缓存中没有这个session，自然找不到服务器中对应的session对象，session对象找不到等同于会话结束！

session对象什么时候被销毁

1. 超时销毁
2. 手动销毁

JSESSIONID=xxxx 是以cookie的形式保存到浏览器中的，只要浏览器关闭，这个cookie就没有了

## cookie禁用了之后，session机制还能实现吗

- cookie禁用是服务器正常发送cookie给浏览器，但是浏览器拒收了，不是服务器不发了
- 找不到的情况下，每一次请求都会获取到新的cookie对象
- cookie禁用了之后，session机制还能实现吗？
  - 可以 需要使用URL重写机制
  - URL重写机制会提高开发者的成本，开发人员在编写任何请求路径的时候，后面都要添加一个sessionid，给开发带来了很大的难度，很大的成本，所以大部分的网站都是这样设计的，要是禁用cookie，就别用网站了。

## session的超时设置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
         version="5.0">

    <!--session的超时时长是30分钟。-->
    <!--如果30分钟过去了，session对象仍然没有被访问，session对象会被销毁。-->
    <session-config>
        <session-timeout>30</session-timeout>
    </session-config>

</web-app>
```



