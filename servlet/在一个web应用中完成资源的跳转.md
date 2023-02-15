# 在一个web应用中完成资源的跳转

在web应用中有两种方式可以完成资源的跳转

- 第一种方式：转发
- 第二种方式；重定向

转发和重定向的区别

- 在代码上的区别：

  - 转发：

  ```java
  //获取请求转发器对象
  RequestDispatcher dispatcher = request.getRequestDispatcher("/dept/list");
  
  // 调用forward方法完成转发
  dispatcher.forward(request, response);
  
  // 合并成为一行代码  传request和response是为了保证是同一个servelt对象（同一个请求），保证同一个请求域，可以取出请求域中的数据
  // 转发的是同一个请求，不管转发多少次，都是同一个request和response对象
  request.getRequestDispacther("/dept/list").forward(request, reponse)
  ```

  - 重定向：

  ```java
  // 重定向 这个路径需要加项目名  浏览器会发送一个全新的请求
  response.sendRedirect(""+contentPath+"/b");
  ```

  ***只要是浏览器发送的请求，请求需要加项目名***

- 形式上的区别：

  - 转发（一次请求）
    - 在浏览器的地址栏上显示的是 http://localhost:8080/redirect/a ，最终请求结束之后，浏览器地址栏上显示的地址还是这个没有变化
  - 重定向（两次请求）
    - 转发到另一个地址，浏览器的地址栏显示的是另一个地址

- 转发和重定向的本质区别

  - 转发：是由web服务器来控制的  A跳转到B资源，这个跳转由Tomcat服务器内部完成的
  - 重定向：是由浏览器来完成的  由浏览器控制。

转发和重定向怎么选择？

- 如果在上一个Servlet当中向request中v绑定了数据，希望从下一个Servlet中把request域里面的数据取出来，使用转发机制
- 其他情况下一律使用重定向机制

***跳转的资源只要是服务器中的合法资源即可***

- 存在浏览器的刷新问题