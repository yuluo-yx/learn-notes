## 关于WEB—INF目录

WEB—INF目录是受保护的，里面的文件不能通过路径来访问

所以HTML，css，js和image等图片是放到WEB—INF目录外面的



**************

# HttpServletResquest接口

## HttpServletRequest的本质

HttpServletResquest是一个接口，全称是jakarta.servlet.http.HttpServletRequest

实现类是：

```java
org.apache.catalina.connector.RequestFacade@21513029
org.apache.catalina.connector.ResponseFacade@2f96ca73
```

HttpServletRequest接口是Servlet规范中的一员，父接口是```ServletRequest```

**是由Tomcat服务器实现的，java web程序员在开发的时候只需要关心HttpServletRequest的方法，这些方法有什么功能即可，不用管HttpServlrtRequest的具体实现类，只需要面向接口编程即可，这就是Servlet规范解耦合的一种体现。**

## HttpServletRequest封装的信息

HttpServletRequest对象是由Tomcat服务器（web服务器）负责创建的，封装了浏览器的请求信息

在用户发送请求的时候，遵循了HTTP协议，发送的是HTTP的请求协议，Tomcat将请求中的数据和信息全部解析出来封装到HttpServletRequest对象中，传给Java web的程序员。

**所以，只要面向HttpServletRequest接口调相应的方法就能获取到请求头信息数据**

## HttpServletRequest中的常用的方法

```html
<!DOCTYPE html>
<html lang="en" xmlns="http://www.w3.org/1999/html">
<head>
    <meta charset="UTF-8">
    <title>user register</title>
</head>
<body>

  <h1>用户注册</h1>
  <form action="/HttpServletRequest/request" method="post">
      用户名：<input type="text" name="用户名"></br>
      密码：<input type="password" name="密码"></br>
      interest：
      smoke<input type="checkbox" name="爱好" value="抽烟">
      drink<input type="checkbox" name="爱好" value="喝酒">
      study<input type="checkbox" name="爱好" value="学习">
      </br>
      <input type="submit" vlue="注册">
  </form>

</body>
</html>
```



**获取用户提交的数据：**

```java
这个是获取Map
String getParameter(String name);
这个是获取Map集合中所有的key
Map<String, String[]> getParameterMap();
根据key获取Map集合的value
Enumeration<String> getParameterNames();
或者value这个一维数组中的第一个元素，这个方法最常用
String[] getParameterValues(String name);
// 以上的四个方法和用户提交的数据有关系
```

**在你前端的form表单提交了数据之后，你打算怎样存储这些数据，准备采用什么数据结构去存储数据**

- 前端提交的数据格式（**前端提交的数据格式以字符串提交，后端获取的也是字符串**）

  ```xml
  用户名: yuluo
  密码: 082916
  爱好: 抽烟
  爱好: 喝酒
  爱好: 学习
  ```

- 复选框会提交多个数据

- 考虑采用map集合存储数据

  > - key存储String
  >
  > - value存储String
  >
  > - 这种存储方式是不合理的，
  >
  >   ———————————
  >
  >   key                 value
  >
  >   ————————————
  >
  >   username      yuluo
  >
  >   password		082916
  >
  >   爱好				 抽烟
  >
  >   爱好				 喝酒
  >
  >   爱好				 学习

- 如果采用以上的数据结构存储会发现key重复的时候value被覆盖, 存在弊端，爱好的数据会被覆盖掉

- **所以采用Map<String,  String[]>的方式存储数据**

```java
———————————

key                 value

————————————

username         {'yuluo'}

password		 {'082916'}

爱好				{'抽烟‘, ’喝酒‘, ’学习‘}
```

```java
package indi.yuluo.javaweb.httpServletRequest;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;
import java.util.Enumeration;
import java.util.Iterator;
import java.util.Map;
import java.util.Set;

/**
 * @author: yuluo
 * @createTime: 2022/1/12 21:34
 * @File : RequestTextServlet.java
 * @Software : IntelliJ IDEA
 * @Description:
 */

public class RequestTextServlet extends HttpServlet {

/*    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        out.print(request + "</br>" + response);

    }*/

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        /*
        使用map集合获取前端数据
        username         {'yuluo'}
        password		 {'082916'}
        爱好				 {'抽烟‘, ’喝酒‘, ’学习‘}
         */
        Map<String, String[]> parameterMap = request.getParameterMap();
        // 遍历map集合
        Set<String> keys = parameterMap.keySet();
        Iterator<String> iterator = keys.iterator();
        while(iterator.hasNext()) {
            String key = iterator.next();
            //System.out.println(next);
            String[] values = parameterMap.get(key);
            System.out.println(key + " = " + values);
            /**
             * 输出的是一维数组的地址。需要遍历
             * 用户名 = [Ljava.lang.String;@3016c67a
             * 密码 = [Ljava.lang.String;@44230d8d
             * 爱好 = [Ljava.lang.String;@6b59a7bf
             */
            System.out.print(key + " = ");
            for (String value : values) {
                System.out.print(value);
            }
            System.out.println();
        }

        System.out.println("getParameterNames-----------------");

        /*
        通过这个方法可以获取到map集合中的所有key
         */
        Enumeration<String> parameterNames = request.getParameterNames();
        while(parameterNames.hasMoreElements()) {
            String s = parameterNames.nextElement();
            System.out.println(s);
        }

        System.out.println("getParameter------------------");

        /*
        getParameter(String name)获取的是String字符串
         */
        String username = request.getParameter("用户名");
        String password = request.getParameter("密码");
        // 在这里只能显示一个
        String habits = request.getParameter("爱好");

        System.out.println(username);
        System.out.println(password);
        System.out.println(habits);

        System.out.println("getParameterValues-----------------");

        String[] usernames = request.getParameterValues("用户名");
        String[] passwords = request.getParameterValues("密码");
        String[] interests = request.getParameterValues("爱好");

        for (String s : usernames) {
            System.out.println(s);
        }

        for (String s : passwords) {
            System.out.println(s);
        }

        for (String s : interests) {
            System.out.println(s);
        }

    }
}

```

