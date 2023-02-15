# jsp原理深度刨析

## jsp的执行原理

- index.jsp底层执行的是：index_jsp.java这个程序
- 这个index.jsp会被tomcat翻译生成index_jsp.java文件，然后tomcat服务器又会将index_jsp.java编译生成index_jsp.class文件，最终被解释执行。所以index.jsp实际上就是一个java类

- index_jsp继承HttpJspBase，而HttpJspBase类继承的是HttpServlet，所以jsp就是一个servlet，和servlet的生命周期相同，都是假单例的。

- jsp第一次访问的时候都会特别慢。为什么？

  1. 要把jsp文件编译生成java源文件
  2. java源文件要编译生成class字节码文件
  3. 然后通过class去创建servlet对象
  4. 然后调用servlet对象的init方法
  5. 最后调用servlet对象的service方法

  **第二次执行的时候直接调用单例servlet对象的service方法，所以第二次访问速度比较快**

## JSP是什么？

- JSP是java程序
- JSP是：JavaServer Pages的缩写 （基于java语言实现的服务器端的页面）
- Servlet是JavaEE的13个子规范之一，那么JSP也是JavaEE的13个子规范之一
- 每一个web容器/web服务器都会内置一个jsp翻译引擎，用来将jsp翻译成java源文件
- **jsp和servlet的区别**
  - 职责不同：
    - servlet的职责：收集数据
    - jsp的职责：展示数据


## JSP的错误调试

进行错误调式的时候，还是要直接打开jsp文件对应的java文件，检查java代码

开发jsp的最高境界是：

​	**写的是jsp，脑子里想的是java**

## JSP的基础语法

### 浏览器展示内容

- 在jsp中直接编写的文字，都会被直接编译生成到out.write里

- 被java文件当成普通的字符串打印到浏览器里面

```java
<%@page contentType="text/html;charset=UTF-8"%>
    
张三
李四
王五
    
// 最终都会被翻译到out.write输出方法中
out.write("String");  
out.write("<!DOCTYPE html>\n");
  out.write("<html>\n");
  out.write("	<head>\n");
  out.write("		<meta charset=\"utf-8\">\n");
  out.write("		<title>欢迎使用OA系统</title>\n");
  out.write("	</head>\n");
  out.write("	<body>\n");
  out.write("		");
  out.write('\n');
  out.write('	');
```

### JSP的Page指令，解决响应时的中文乱码问题

- `<%@page contentType="text/html;charset=UTF-8"%>`表示响应的内容类型是text/html。采用的字符集是UTF-8

### 脚本块

```java
// 脚本块 这里面编写的被是为java程序，编译到Servlet类的service方法内部
// 在脚本块中写代码的时候其实是在方法体内部编写代码
<% %>

// 报错原因：在service方法中定义的变量不能使用private等访问权限修饰符修饰    
<%--
    private int i;
--%>    
```

- 在这个在service方法中不能写静态代码块，不能写方法，不能定义成员变量
- 在同一个servlet中<%%>可以出现多个
- 在service方法中的写的代码是自上而下顺序执行的

```java
<%!
    // 成员变量
    private String name = "jackson";
    
    // 静态代码块
    static {
    	System.out.println("静态代码块执行了！");
	}
    
    // 方法
	public static void m1() {
        System.out.println("m1 method execute!");
    }
%>
```

- 在这编写的代码出现在service方法之外，类体当中
- 这个语法很少用，（不建议用）因为在service方法之外编写静态变量和实例变量，都会存在线程安全问题，JSP就是servlet，servlet是单例的，多线程并发的环境下，这个静态变量和实例变量一旦有修改操作，必然会存在线程安全的问题

### 注释

```java
<%--
    这是JSP注释
--%>
    
<!--这个HTML注释不是专业注释 不要使用这种注释方式-->    
```

### 输出语句

- 怎么向浏览器输出java变量

- ```java
  <% 
      String name = "jack";
  
  	// 输出到控制台
  	System.out.println("name = " + name);
  
  	// 输出到浏览器
  	out.wirte("name =" + name);
  %>
  ```

- 注意：以上代码中的out是JSP的九大内置对象之一，可以直接拿来用，当然，必须只能在service方法内部使用

- 如果向浏览器上输出的内容中没有“java代码”，例如输出的字符串是一个固定的字符串，可以直接编写在jsp中，不需要写到<% %>中

- ```java
  // 下面的符号最终被翻译成了out.print(100 + 200);
  <%= 100 + 200 %>
  ```

  

### JSP文件的扩展名是xxx.jsp吗？

- 在jsp文件的扩展名是可以配置的，不是固定的
- 在CATALINA_HOME/conf/web.xml, 在这个文件当中配置jsp文件的扩展名
- jsp对于tomcat来说，只是一个普通的文本文件，tomcat会把jsp文件翻译成为java代码

### 总结

- JSP中直接编写普通字符串
  - 翻译到service方法中out.write()方法
- <%%>
  - 翻译到service方法体内部，里面是一条一条的java语句
- <%! %>
  - 被翻译到service方法之外
- <%= %>
  - 翻译到service方法体内部，翻译为 out.print();
- <%@page contentType="text/html;charset=UTF-8">
  - page指令，通过contrntType属性用来设置响应的内容类型

## JSP指令

### 指令分类

指令的作用：知道当前的JSP翻译引擎如何翻译当前的jsp文件

```jsp
include指令：包含指令， 在jsp中完成静态包含，很少用
taglib指令：引入标签库的指令。
page指令：重点
```

### 指令语法

`<%@指令名 属性名=属性值 属性名=属性值 ……%>`

### 关于page指令中的常用属性

```jsp
// true表示启用jsp的内置对象session，表示一定启动session对象，没有会创建，默认值为true
<%--<%@page session="true" %>--%>
<%--<%@page session="false" %>--%>

// 指定页面的响应内容类型
<%--<%@page contentType="text/html" %>--%>
<%--<%@page contentType="text/json" %>--%>

// pageEncoding和charset设置页面响应时的字符集
<%--<%@page contentType="text/json;charset=UTF-8" %>--%>
<%--<%@page contentType="text/json" pageEncoding="UTF-8" %>--%>

// 导包
<%--<%@page import="java.util.List, java.util.Date, java.util.ArrayList" %>
<%@page import="java.util.*" %>--%>

// 当前jsp出错后，会跳转到error.jsp页面。
// 即设置jsp出现异常时的跳转页面
<%@page errorPage="/error.jsp" %>

<%
    String name = null;
    // 空指针异常
    name.toString();
%>
```

```jsp
<%@ page contentType="text/html;charset=UTF-8"  %>
<%--在错误页面可以启用JSP九大内置对象之：exception--%>
<%--exception内置对象就是刚刚发生的异常对象。--%>
<%@page isErrorPage="true" %>
<html>
<head>
    <title>error</title>
</head>
<body>
<h1>网络繁忙，稍后再试！！！！</h1>

<%--打印异常堆栈信息，输出到后台控制台。exception是九大内置对象之一。--%>
<%
    exception.printStackTrace();
%>
</body>
</html>
```

## JSP的解答内置对象

```java
页面作用域
jakarta.servlet.jsp.PageContext pageContext
    
请求作用域    
jakarta.servlet.http.HttpServletRequest request
    
会话作用域    
jakarta.servlet.http.HttpSession session
    
应用作用域    
jakarta.servlet.ServletContext application
	PageContext < request < session < application    
    以上四个作用域都有：setAttribute, getAttribute, removeAttribute方法
    以上作用域的使用原则：尽可能使用小的域
    
java.lang.Throwable exception
jakarta.servlet.ServletConfig config
    
其实是this，当前的servlet对象    
java.lang.Obejct page
    
负责输出    
jakarta.servlet.jsp.jspWriter out
    
负责响应    
jakarta.servlet.httpHttpServletReqponse response    
```

# EL表达式

## 介绍

- Expression Language （表达式语言）
- 可以代替jsp中的java代码，让jsp中的代码看起来更加的整洁和美观
- jsp中夹杂着java代码，例如：<% java代码 %> <%=  %>等，导致jsp文件混乱，不含看，不好维护
- EL表达式可以算是JSP语法的一部分
- EL表达式的主要作用
  - 第一：从某个域中取出数据
  - 第二：将取出的数据转成字符串
  - 第三：将字符串输出到浏览器

## EL表达式的基本语法：

```java
${ EL表达式 }
```

```jsp
<%@page contentType="text/html;charset=UTF-8" %>

<%
    // 向request作用域当中存储username为zhangsan
    request.setAttribute("username", "zhangsan");
    request.setAttribute("obj", new Object());
%>

<%--将request域当中的数据取出来，并且还要输出到浏览器，使用java代码怎么办？--%>
<%=request.getAttribute("username")%>
<br>
<%=request.getAttribute("obj")%>
<br>

<hr>

<%--使用EL表达式呢？--%>
${username}
<br>
${obj}
```

- 数据必须要存到四个作用域中的一个，**EL表达式只能取，不能存**

```java
package indi.yuluo.javaweb.jsp.bean;

/**
 * 符合javabean规范的一个java类。
 */
public class User {
    private String username;
    private String password;
    private int age;
    private Address addr;

    public Address getAddr222() {
        return addr;
    }

    public void setAddr(Address addr) {
        this.addr = addr;
    }

    /*@Override
    public String toString() {
        return "User{" +
                "username='" + username + '\'' +
                ", password='" + password + '\'' +
                ", age=" + age +
                '}';
    }*/

    public User() {
    }

    public User(String username, String password, int age) {
        this.username = username;
        this.password = password;
        this.age = age;
    }

    public String getUsername() {
        System.out.println("getUsername()方法执行了");
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        System.out.println("getPassword()方法执行了");
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    // 或者getage()
    // java程序员给方法起名的时候，建议驼峰。
    public int getAge() {
        System.out.println("getAge()方法执行了");
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    /**
     * 这个方法只是一个get方法而已。类中并没有声明email属性。
     * 使用EL表达式可以获取Email吗？
     * @return
     */
    public String getEmail(){
        return "dujubin@126.com";
    }
}
```

- EL表达式`${userObj.getXXX()}`使用的是get方法

```jsp
<%@ page import="com.bjpowernode.javaweb.jsp.bean.User" %>
<%@ page import="com.bjpowernode.javaweb.jsp.bean.Address" %>
<%@page contentType="text/html;charset=UTF-8" %>

<%
    // 创建User对象
    User user = new User();
    user.setUsername("jackson");
    user.setPassword("1234");
    user.setAge(50);

    // 创建地址Address对象
    Address a = new Address();
    a.setCity("北京");
    a.setStreet("大兴区");
    a.setZipcode("11111111");

    user.setAddr(a);

    // 将User对象存储到request域当中
    request.setAttribute("userObj", user);
%>

<%--使用EL表达式，从request域当中，取出User对象，并将其输出到浏览器--%>
<%--1. EL表达式会自动从某个范围中取数据。2. 将其转成字符串。 3. 将其输出到浏览器。--%>
${userObj}

<br>
<%--你想输出的是user对象的username属性--%>
${userObj.username}
<br>
<%--输出password--%>
${userObj.password}
<br>
<%--输出年龄age--%>
${userObj.age}
<br>
<%--输出email--%>
${userObj.email}

<br>
<%--在EL表达式中不能添加双引号，如果添加了双引号，EL表达式就会将其当做普通的字符串输出到浏览器。--%>
${"userObj"}
userObj

<br>
<%--取出User对象是哪个城市的？--%>
城市:${userObj.addr222.city}
街道：${userObj.addr222.street}
邮编：${userObj.addr222.zipcode}
```

## 面试题

`${abc}和${"abc"}`的区别在于：前者是从某个域中获取数据，而后者是把abc当作字符串输出到控制台

## EL表达式可以指定域范围来存取对象

```mark
EL表达式中的四个隐含隐式的范围
pageScope  				对应的是pageContext范围
requestScope			对应的是request范围
SessionScope			对应的是session范围	
applicationScope		对应的是application范围

```



```xml
从域中取出数据 （前提是要域中存储相对应的数据）
${pageScope.data}<br>
${requestScope.data}<br>
${sessionScop.data}<br>
${appliationScope.data}<br>
```

## 其他的EL表达式取值的方法

EL表达式对null进行了处理，如果是null，则向浏览器输出一个空字符串

EL表达式对取数据的时候有两种形式：

- 第一种：（大部分使用这种形式）

- 第二种：[] （如果存储到域中的时候，这个name中含有特殊字符，乐意使用[]）

  - request.setAttribute("abc.def", "zhangsan")；
  - ${requestScope.abd.def} 这样是无法取值的
  - 应该这样用：${requestScope["abd.def"]}

  

- 掌握使用EL表达式，怎么从map集合中取数据

  - ${map.key}

  - ```jsp
    <%@ page import="java.util.HashMap" %>
    <%@ page import="java.util.Map" %>
    <%@ page import="com.bjpowernode.javaweb.jsp.bean.User" %>
    <%@page contentType="text/html; charset=UTF-8" %>
    
    <%
        // 一个Map集合
        Map<String,String> map = new HashMap<>();
        map.put("username", "zhangsan");
        map.put("password", "123");
    
        // 将Map集合存储到request域当中。
        request.setAttribute("userMap", map);
    
        Map<String,User> userMap2 = new HashMap<>();
        User user = new User();
        user.setUsername("lisi");
        userMap2.put("user", user);
        request.setAttribute("fdsafdsa", userMap2);
    %>
    
    <%--使用EL表达式将Map集合中的user对象中的username取出--%>
    ${fdsafdsa.user.username}
    
    <hr>
    
    <%--使用EL表达式，将map中的数据取出，并输出到浏览器--%>
    ${userMap.username}
    <br>
    ${userMap.password}
    <br>
    ${userMap["username"]}
    <br>
    ${userMap["password"]}
    ```

    

- 从数组中取数据 (list集合也是以下标的方式取出数据的)

  - ${数组名[index]}

    ```jsp
    <%@ page import="indi.yuluo.javaweb.jsp.bean.User" %>
    <%@ page import="java.util.ArrayList" %>
    <%@ page import="java.util.List" %>
    <%@ page import="java.util.HashSet" %>
    <%@ page import="java.util.Set" %>
    <%@page contentType="text/html; charset=UTF-8" %>
    
    <%
        // 数组对象
        String[] usernames = {"zhangsan", "lisi", "wangwu"};
    
        // 向域中存储数组
        request.setAttribute("nameArray", usernames);
    
        User u1 = new User();
        u1.setUsername("zhangsan");
    
        User u2 = new User();
        u2.setUsername("lisi");
    
        User[] users = {u1, u2};
    
        request.setAttribute("userArray", users);
    
        List<String> list = new ArrayList<>();
        list.add("abc");
        list.add("def");
    
        request.setAttribute("myList", list);
    
        Set<String> set = new HashSet<>();
        set.add("a");
        set.add("b");
    
        request.setAttribute("set", set);
    %>
    
    <%--取出set集合--%>
    set集合：${set}
    <%--无法获取：PropertyNotFoundException: 类型[java.util.HashSet]上找不到属性[0]--%>
    <%--${set[0]}--%>
    <hr>
    
    <%--取出List集合--%>
    <%--list集合也是通过下标的方式取数据的。--%>
    ${myList}
    ${myList[0]}
    ${myList[1]}
    <hr>
    
    <%--取出数组中第二个用户的用户名--%>
    ${userArray[1].username}
    <hr>
    
    <%--使用EL表达式取出数组中的元素--%>
    ${nameArray} <%--将数组对象直接输出--%>
    ${nameArray[0]} <%--取出数组中的第一个元素--%>
    ${nameArray[1]}
    ${nameArray[2]}
    
    <%--取不出数据，在浏览器上直接显示的就是空白。不会出现数组下表越界。--%>
    ${nameArray[100]}
    ```

    

- 指令 （idELIgnored） --> 是否忽略页面中的所有EL表达式 **false 不忽略 true 忽略**

- ```java
  <%--<%@page contentType="text/html;charset=UTF-8" isELIgnored="false" %>--%>
  <%@page contentType="text/html;charset=UTF-8" %>
  <%--<%@page contentType="text/html;charset=UTF-8" isELIgnored="true" %>--%>
  
  <%
      request.setAttribute("username", "zhangsan");
  %>
  
  <%-- isELIgnored="true" 表示忽略JSP中整个页面的所有EL表达式。
      如果想忽略其中某个，可以使用以下反斜杠。 --%>
  \${username}
  ${username}
  ${username}
  ```



## 通过EL表达式来获取应用的根

```jsp
<%@page contentType="text/html;charset=UTF-8" %>
<%@page import="jakarta.servlet.http.HttpServletRequest"%>

<%--
// JSP有九大内置对象
// pageContext,request,session,application,response,out,config,page,exception
// 其中四个域对象，其中最小的域是pageContext
// pageContext翻译为：页面上下文。通过pageContext可以获取？？？？
--%>

<%--从以下代码来看，pageContext.getRequest() 方法是没用的。这是获取request对象。而JSP中自带了内置对象request，直接用request内置对象就行了。--%>
<%=pageContext.getRequest() %>
<br>
<%=request %>

<hr>

<%--
    在EL表达式当中没有request这个隐式对象。
    requestScope 这个只代表“请求范围”。不等同于request对象。
    在EL表达式当中有一个隐式的对象：pageContext
    EL表达式中的pageContext和JSP中的九大内置对象pageContext是同一个对象。
--%>
<%--<%=pageContext.getRequest() %>--%>
<%=((HttpServletRequest)pageContext.getRequest()).getContextPath() %>
这段java代码对应的EL表达式：
使用EL表达式来获取应用的根路径：
${pageContext.request.contextPath}
```

## EL表达式获取前端提交的参数

```java
<%@page contentType="text/html;charset=UTF-8" %>

<%--JSP中EL表达式的隐含对象：
    1. pageContext
    2. param
    3. paramValues
    4. initParam
    5. 其他（不是重点）
--%>
应用的根路径：${pageContext.request.contextPath}<br>

<%--request是JSP九大内置对象之一。--%>
<%--request.getParameter("username") 获取请求的参数。--%>
<%--用户在浏览器上提交数据：http://localhost:8080/jsp/15.jsp?username=lisi --%>
用户名：<%=request.getParameter("username")%><br>
用户名：${param.username}<br>

<%--假设用户提交的数据：http://localhost:8080/jsp/15.jsp?aihao=smoke&aihao=drink&aihao=tangtou--%>
<%--以上提交的数据显然是采用checkbox进行提交的。同一组的checkbox的name是一样的。--%>
<%--param 获取的是请求参数一维数组当中的第一个元素。--%>
爱好：${param.aihao} <br>
爱好：<%=request.getParameter("aihao")%> <br>

一维数组：${paramValues.aihao}<br>
一维数组：<%=request.getParameterValues("aihao")%><br>

<%--获取数组当中的元素：[下标]--%>
爱好：${paramValues.aihao[0]}、${paramValues.aihao[1]}、${paramValues.aihao[2]} <br>

<%--EL表达式中的隐含对象：initParam--%>
<%--ServletContext是Servlet上下文对象，对应的JSP九大内置对象之一是：application --%>
<%
    String a = application.getInitParameter("pageSize");
    String b = application.getInitParameter("pageNum");
%>

每页显示的记录条数：<%=a%> <br>
页码：<%=b%> <br>

每页显示的记录条数：<%=application.getInitParameter("pageSize")%> <br>
页码：<%=application.getInitParameter("pageNum")%> <br>


每页显示的记录条数：${initParam.pageSize} <br>
页码：${initParam.pageNum} <br>
```

## EL表达式参与数学运算

```jsp
<%@ page import="com.bjpowernode.javaweb.jsp.bean.Student" %>
<%@page contentType="text/html;charset=UTF-8" %>

<%--
    关于EL表达式中的运算符
        1. 算术运算符
            + - * / %
        2. 关系运算符
            == != > >= < <= eq
        3. 逻辑运算符
            ! && || not and or （注意：!和not都是取反）
        4. 条件运算符
            ? :
        5. 取值运算符：
            [] 和 .
        6. empty 运算符
--%>
${10 + 20} <br>

<%-- 在EL表达式当中“+”运算符只能做求和运算。不会进行字符串拼接操作。 --%>
<%--"20"会被自动转换成数字20--%>
${10 + "20"} <br>

<%-- java.lang.NumberFormatException: For input string: "abc" --%>
<%-- + 两边不是数字的时候，一定会转成数字。转不成数字就报错：NumberFormatException--%>
\${10 + "abc"} <br>

\${"king" + "soft"} <br>

<%-- 关系运算符 --%>
${"abc" == "abc"} <br>
${"abc"} == ${"abc"} <br>

<%
    Object obj = new Object();
    request.setAttribute("k1", obj);
    request.setAttribute("k2", obj);
%>
${k1 == k2} <br> <%--true--%>

<%
    String s1 = new String("hehe");
    String s2 = new String("hehe");
    request.setAttribute("a", s1);
    request.setAttribute("b", s2);
%>
${a == b} <%--true--%> <br>

<%
    Object o1 = new Object();
    Object o2 = new Object();
    request.setAttribute("o1", o1);
    request.setAttribute("o2", o2);
%>
${o1 == o2} <br> <%--false--%>

<%
    Student stu1 = new Student("110", "警察");
    Student stu2 = new Student("110", "警察");

    request.setAttribute("stu1", stu1);
    request.setAttribute("stu2", stu2);
%>

<%--EL表达式当中的 "==" 调用了equals方法。--%>
\${stu1 == stu2} <br>
\${stu1 eq stu2} <br>

<hr>
<%-- != 也会调用对象的equals方法。--%>
${stu1 != stu2} <br>
${200 != 200} <br>

<%--以下语法错误，没有加小括号--%>
\${!stu1 eq stu2} <br>
<%--正确的--%>
${!(stu1 eq stu2)}<br>
${not(stu1 eq stu2)}<br>

<%--条件运算符--%>
${empty param.username ? "对不起，用户名不能为空！" : "欢迎访问！！！！" }


<hr>
<%--empty运算符：运算结果是boolean类型。--%>
<%--判断是否为空，如果为空，结果是true。如果不为空，结果是false--%>
${empty param.username} <br>
${!empty param.username} <br>
${not empty param.username} <br>

<hr>
<%--结果false--%>
<%--前半部分是boolean类型。后面是null--%>
${empty param.password == null} <br>
${(empty param.password) == null} <br>

${param.password == null} <br>
```

# JSTL标签库

## 介绍

Java Standad TagLib（java标准的标签库）

通常结合EL表达式一起使用，目的是让JSP中的java代码消失

## 使用步骤

- 引入jstl的相关jsr包
  - tomcat10之后的jar包
  - 
