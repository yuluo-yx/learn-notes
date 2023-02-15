# 使用纯Servlet做一个单表的CRUD操作

********

## 1，构建数据库表dept

sql脚本

```sql
drop table if exists dept;

create table dept(
    deptno int primary key,
    dname varchar(255),
    loc varchar(255)
);

insert into dept(deptno, dname, loc) values(10, '销售部', '北京');
insert into dept(deptno, dname, loc) values(20, '研发部', '上海');
insert into dept(deptno, dname, loc) values(30, '人事部', '深圳');
insert into dept(deptno, dname, loc) values(40, '管理部', '西安');

commit;

select * from dept;
```

## 2，准备一套HTML页面（项目原型）【使用HBuilder前端工具】

- 准备好HTML的页面，
- 让HTML页面中的链接都能够跑通 （页面流转成功）
- 应该设计那些页面
  - 欢迎页面 ： index.html
  - 新增页面 : add.html
  - 修改页面 : edit.html
  - 查看详情页面 :  detail.html
  - 部门列表页面 ：list.html (以列表页面为核心，展开操作)

## 3，分析系统包括那些功能

- 什么是一个功能？
  - 只要这个操作连接了数据库这个就算一个功能。
- 包括那些功能
  - 查看部门列表
  - 删除部门
  - 新增部门
  - 查看部门详情信息
  - 跳转到修改页面
  - 修改部门

## 4，在IDEA中搭建开发环境

- 创建一个webapp, 添加依赖到classpath中
- 向webapp中添加数据库驱动（**新建lib目录，将数据库驱动放到lib中，必须是 lib **）
- JDBC的工具类
- 将所有的html页面放到web目录下，**注意不要放到WEB-IBF目录下**

## 5，实现功能

> 注意：可以从后端往前端写，也可以从前端往后端写，不要想起来哪里写哪里，这阿姨那个一个顺序流下来基本不会有什么错误。
>
> 在编写代码的时候，根据系统功能执行的顺序进行编写，系统先开始怎么样，代码就编写到哪一步

### 实现第一个功能：查看部门列表

- 从那里开始，

  - 假设从前端开始，那么一定是从用户点击按钮那里开始的

- 第一：先修改前端页面的超链接，因为用户先点击的就是这个超链接

  ```html
  <a href="/oa/dept/list">查看部门列表</a>
  ```

  

- 第二：编写web.xml文件

  ```html
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
           version="5.0">
  
      <servlet>
          <servlet-name>list</servlet-name>
          <servlet-class>indi.yuluo.javaweb.oa.controller.DeptListServlet</servlet-class>
      </servlet>
      <servlet-mapping>
          <servlet-name>list</servlet-name>
          <!--此路径不需要加项目名-->
          <url-pattern>/dept/list</url-pattern>
      </servlet-mapping>
  
  </web-app>
  ```

  

- 第三：编写DepeListServelt方类，继承HttpServlet类，重写doGet方法

  ```java
  package indi.yuluo.javaweb.oa.Controller;
  
  import jakarta.servlet.ServletException;
  import jakarta.servlet.http.HttpServlet;
  import jakarta.servlet.http.HttpServletRequest;
  import jakarta.servlet.http.HttpServletResponse;
  
  import java.io.IOException;
  
  /**
   * @author: yuluo
   * @createTime: 2022/1/15 11:26
   * @File : DeptListServlet.java
   * @Software : IntelliJ IDEA
   * @Description:
   */
  
  public class DeptListServlet extends HttpServlet {
  
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          
      }
  }
  ```

- 第四：在DeptListServlet中的doGet方法连接数据库，查询所有的部门，动态的展示部门列表页面

  - 分析list.html页面中的静态和动态展示的代码部分
  - **将html页面中的所有双引号替换为单引号，因为out.print("")这是双引号，以防产生冲突**

- 第五：详情页

  - 建议：从前端往后端写，首先要考虑的是用户点击的是什么，点击的东西在那里

  ```html
  <a href='/oa'>详情</a>
  ```

  用户点击了详情之后，要连接数据库，肯定要执行一段java代码，所以需要修改超链接的路径

  ***前端发送请求之后，要加项目名***

  ```html
  <a href='/oa/dept/datail'>详情</a>
  ```

  获取的是不同部门的详情，可以做一个字符串拼接，将部门编号加进去

  ```html
  <a href='"+contextPath+"/dept/detail?deptno="+deptno+"'>详情</a>
  ```

  - 向数据提交数据的格式

    ***重点：向服务器提交的数据格式为url?name=value&name=value&name=value……***

  - 编写 web.xml文件

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
             version="5.0">
    
      <servlet>
        <servlet-name>list</servlet-name>
        <servlet-class>indi.yuluo.javaweb.oa.controller.DeptListServlet</servlet-class>
      </servlet>
      <servlet-mapping>
        <servlet-name>list</servlet-name>
        <!--此路径不需要加项目名-->
        <url-pattern>/dept/list</url-pattern>
      </servlet-mapping>
    
      <servlet>
        <servlet-name>detail</servlet-name>
        <servlet-class>indi.yuluo.javaweb.oa.controller.DeptDetailServlet</servlet-class>
      </servlet>
      <servlet-mapping>
        <servlet-name>detail</servlet-name>
        <!--此路径不需要加项目名-->
        <url-pattern>/dept/detail</url-pattern>
      </servlet-mapping>
    
    </web-app>
    ```

  - 编写DeptDetialServlet类

### 删除部门信息

- 在删除的时候可以提示用户，确定要删除吗？

- ```html
  <!DOCTYPE html>
  <html>
  	<head>
  		<meta charset="utf-8">
  		<title>部门列表页面</title>
  	</head>
  	<body>
  		
  		<script type="text/javascript">
  			function del(dno) {
  				// 弹出确认框 会返回true和false
  				// true删除数据，false返回
  				var flag = window.confirm("确定要删除数据吗？")
  				if(flag) {
  					// 发送请求进行删除数据的操作
  					// 在js代码中如何发送请求给服务器
  					alert("正在删除数据，请稍后…………")
  					// 跟请求路径  一下四种方法都行
  					document.location.href = "/oa/dept/delete?deptno=" + dno;
  					//document.location = "请求路径"
  					//window.location.href = "请求路径"
  					//window.location = "请求路径"
  				}
  			}
  		</script>
  		
  		<h1 align="center">部门列表</h1>
  		<hr>
  		<table border="1px" align="center" width="50%">
  			<tr>
  				<th>序号</th>
  				<th>部门编号</th>
  				<th>部门名称</th>
  				<th>操作</th>
  			</tr>
  			<tr>
  				<td>1</td>
  				<td>10</td>
  				<td>销售部</td>
  				<td>
  					<!-- <a href="javascript:void(0)" onclick="window.confirm('确认删除该数据吗？')">删除</a> -->
  					<a href="javascript:void(0)" onclick=" del(den(10)) ">删除</a>
  					<a href="edit.html">修改</a>
  					<a href="detail.html">详情</a>	
  				</td>
  			</tr>
  			<tr>
  				<td>2</td>
  				<td>20</td>
  				<td>研发部</td>
  				<td>
  					<a href="javascript:void(0)" onclick=" del(dno) ">删除</a>
  					<a href="edit.html">修改</a>
  					<a href="detail.html">详情</a>	
  				</td>
  			</tr>
  			<tr>
  				<td>3</td>
  				<td>30</td>
  				<td>管理部</td>
  				<td>
  					<a href="javascript:void(0)" onclick=" del(dno) ">删除</a>
  					<a href="edit.html">修改</a>
  					<a href="detail.html">详情</a>	
  				</td>
  			</tr>
  			<tr>
  				<td>4</td>
  				<td>40</td>
  				<td>人事部</td>
  				<td>
  					<a href="javascript:void(0)" onclick=" del(dno) ">删除</a>
  					<a href="edit.html">修改</a>
  					<a href="detail.html">详情</a>	
  				</td>
  			</tr>
  		</table>
  		<hr>
  		<a href="add.html">新增部门</a>
  	</body>
  </html>
  
  ```

- 之后进行数据库删除操作，删除成功则跳转到list页面，删除失败跳转到error.html页面

### 新增部门

和其他操作一样，进行数据库操作，不需要结果集，只需要数据库连接和执行数据库操作prepareedstatement

```java
// 保存成功之后转发到DeptListServlet中，DeptListServlet只有doGet请求，这个请求是doPost请求
// 会引发405错误，暂时只能在doPost方法中调用doGet暂时解决
@Override
protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        doGet(req, resp);
    }
```

### 修改部门信息

和其他操作大致相同

- 1. 修改数据要在原来数据的基础之上进行操作，所以要先查询出来结果，
  2. 然后对结果进行修改，再写入到数据库中，用到了get和post请求

