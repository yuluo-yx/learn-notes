# HttpServlet源码分析

- HttpServlet在jakarta.servlet.http.HttpServlet下，专门为HTTP协议准备的, 更加适合HTTP协议的开发
- 到目前为止我们接触了那些servlet规范中的接口
  - jakarta.servlet.Servlet								 	 核心接口 （class）
    - jakarta.servlet.ServletConfig			  Servlet配置接口  （interface）
    - jakarta.servlet.ServletContext			Servlet上下文接口 （interface）
    - jakarta.servlet.ServletResopnse		Servlet响应接口 （interface）
    - jakarta.servlet.ServletRequest			Servlet请求接口 （interface）
    - jakarta.servlet.Exception						Servlet异常  （class）
    - jakarta.servlet.GenericServlet 			标准通用的Servlet类（abstract class）
- HttpServlet包下的接口：
  - jakarta.servlet.http.HttpServlet  核心类(abstract class)
  - jakarta.servlet.http.HttpServletRequest  http请求对象
  - jakarta.servlet.http.HttpServletResponse  http响应对象
  - Tomcat服务器（web服务器）将“请求协议”中的数据全部解析出来，然后将这些数据全部封装在了request对象中
  - 也就是说，只要面向HttpServletRequest，就可以获取到请求协议中的内容
- HttpServletResponse对象是专门用来响应HTTP协议到浏览器的

# 源码分析

## 生命周期

> request和response的生命周期：
>
> 用户在发送请求的时候tomcat服务器新建request和response对象，对象只存在这个请求的时间
>
> 当用户发次下一个请求的时候，就会重新创建request和response对象

***ctrl + F12查看类中的方法***

```java
    /*
    用户第一次请求，服务器创建HelloServlet对象，执行无参构造方法创建对象
    public HelloServlet(){
    }
    */
```

```java
    /*
    调用父类的init方法，发现HttpServlet没有init方法
    继续向上找，执行GenericServlet中init方法
    如果你的HttpServlet重写了init方法，则使用你编写的init方法，如果没有，调用GenericServlet的init方法
    public abstract class GenericServlet implements Servlet, ServletConfig, Serializable {

        @Override
        public void init(ServletConfig config) throws ServletException {
            this.config = config;
            this.init();
        }

    }
     */
```

```java
    /*
    下一步调用service方法，如果类中重写了了service方法，则调用这个类中的service方法，不然则调用父类中service方法
    用户发送一次请求，service方法执行一次
    发送N次，执行N次
    public void service(ServletRequest req, ServletResponse res) throws ServletException, IOException {
        HttpServletRequest request;
        HttpServletResponse response;
        try {
        // 强制向下转型为HttpServletRequest
            request = (HttpServletRequest)req;
            response = (HttpServletResponse)res;
        } catch (ClassCastException var6) {
            throw new ServletException(lStrings.getString("http.non_http"));
        }

        this.service(request, response);
    }
     */


    // 注意这两个参数都是有http的
	// 在该方法中定义核心骨架，具体的实现步骤延迟到子类中去完成
    // 会调用这个service方法
    protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 获取请求方式
        // 这个请求方式最终是：“”
        // req.getMethod()获取的是请求方式，可能是七种之一
        String method = req.getMethod();
        long lastModified;
        if (method.equals("GET")) {
            lastModified = this.getLastModified(req);
            if (lastModified == -1L) {
                this.doGet(req, resp);
            } else {
                long ifModifiedSince;
                try {
                    ifModifiedSince = req.getDateHeader("If-Modified-Since");
                } catch (IllegalArgumentException var9) {
                    ifModifiedSince = -1L;
                }

                if (ifModifiedSince < lastModified / 1000L * 1000L) {
                    this.maybeSetLastModified(resp, lastModified);
                    this.doGet(req, resp);
                } else {
                    resp.setStatus(304);
                }
            }
        } else if (method.equals("HEAD")) {
            lastModified = this.getLastModified(req);
            this.maybeSetLastModified(resp, lastModified);
            this.doHead(req, resp);
        } else if (method.equals("POST")) {
            this.doPost(req, resp);
        } else if (method.equals("PUT")) {
            this.doPut(req, resp);
        } else if (method.equals("DELETE")) {
            this.doDelete(req, resp);
        } else if (method.equals("OPTIONS")) {
            this.doOptions(req, resp);
        } else if (method.equals("TRACE")) {
            this.doTrace(req, resp);
        } else {
            String errMsg = lStrings.getString("http.method_not_implemented");
            Object[] errArgs = new Object[]{method};
            errMsg = MessageFormat.format(errMsg, errArgs);
            resp.sendError(501, errMsg);
        }

    }
     */
```

- 我们编写的HelloeServlet直接继承自HttpServlet

## 405方法不允许

**前端错误，发送的请求不合适**

```java
package indi.yuluo.javaweb.httpServlet;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;
import java.io.PrintWriter;

/**
 * @author: yuluo
 * @createTime: 2022/1/12 16:21
 * @File : HelloServlet.java
 * @Software : IntelliJ IDEA
 * @Description:
 */

public class HelloServlet extends HttpServlet {

/*    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        out.print("<h1>get请求</h1>" + "</br>");
    }*/

    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();

        out.print("<h1>post请求</h1>" + "</br>");
    }
}
```

当我们在HelloServlet中重写了doPost方法，而没有写doGet方法，而前端发来了一个get请求，此时就会发生405 方法不允许的错误。

分析源码时，发现：

- 当前端发送了get请求，而我们的HelloServlet中没有重写doGet方法，就会执行HttpServlet中service的doGet方法

- ```java
  protected void service(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          String method = req.getMethod();
          long lastModified;
          if (method.equals("GET")) {
              lastModified = this.getLastModified(req);
              if (lastModified == -1L) {
                  
                  // 执行
                  this.doGet(req, resp);
              } else {
                  long ifModifiedSince;
                  try {
                      ifModifiedSince = req.getDateHeader("If-Modified-Since");
                  } catch (IllegalArgumentException var9) {
                      ifModifiedSince = -1L;
                  }
  
                  if (ifModifiedSince < lastModified / 1000L * 1000L) {
                      this.maybeSetLastModified(resp, lastModified);
                      this.doGet(req, resp);
                  } else {
                      resp.setStatus(304);
                  }
              }
          } else if (method.equals("HEAD")) {
              lastModified = this.getLastModified(req);
              this.maybeSetLastModified(resp, lastModified);
              this.doHead(req, resp);
          } else if (method.equals("POST")) {
              this.doPost(req, resp);
          } else if (method.equals("PUT")) {
              this.doPut(req, resp);
          } else if (method.equals("DELETE")) {
              this.doDelete(req, resp);
          } else if (method.equals("OPTIONS")) {
              this.doOptions(req, resp);
          } else if (method.equals("TRACE")) {
              this.doTrace(req, resp);
          } else {
              String errMsg = lStrings.getString("http.method_not_implemented");
              Object[] errArgs = new Object[]{method};
              errMsg = MessageFormat.format(errMsg, errArgs);
              resp.sendError(501, errMsg);
          }
  
      }
  ```

- 

- ```java
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          String msg = lStrings.getString("http.method_get_not_supported");
          this.sendMethodNotAllowed(req, resp, msg);
      }
  ```

- ```this.sendMethodNotAllowed(req, resp, msg);```根据这句代码，我们发现执行了HttpServlet中的doGet方法，出现了405错误

- ```java
  private void sendMethodNotAllowed(HttpServletRequest req, HttpServletResponse resp, String msg) throws IOException {
          String protocol = req.getProtocol();
          if (protocol.length() != 0 && !protocol.endsWith("0.9") && !protocol.endsWith("1.0")) {
              resp.sendError(405, msg);
          } else {
              resp.sendError(400, msg);
          }
  
      }
  ```

- ***同样，当我们写了doGet方法，没有写doPost方法，就会出现POST方法不允许的错误***

- 怎么避免：

  后端重写了doGet方法，前端一定要发get请求

  后端重写了doPost方法，前端一定要发post请求

  这样可以避免405错误

  - 后端让发什么方式，前端就发什么方式

  **有的人，在代码中为了避免405错误，在Servlet类中，对doGet和doPost方法都进行了重写，以避免405方式的发生，非常不建议写，405错误是有用的，在要提交用户密码的时候，就用post请求。不要用doGet，如果都重写了，可以去重写service类，不走HttpServlet的service方法！**

