# 关于一个web网站的欢迎页面

对于一个webapp来说，我们是可以设置他的欢迎界面的

设置了欢迎界面之后，当访问这个webapp的时候，或者访问这个web站点的时候，没有指定任何的“资源路径”这个时候会默认访问你的欢迎页面

## 怎么指定欢迎页面

1. 编写一个html的欢迎界面

2. 在web.xml里面配置

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_5_0.xsd"
            version="5.0">
       
       <!--将index.html指定为webapp的欢迎页面-->
       <welcome-file-list>
           <welcome-file>login.html</welcome-file>
       </welcome-file-list>
       
   </web-app>
   ```

3. 注意：设置欢迎页面的时候这个路径不需要以“/”开始。并且这个文件的路径默认是从webapp的根开始的

4. 如果有目录，则dir/dir/dir/xxx.html不需要以“/”开始

5. 欢迎页可以设置多个欢迎页

6. 越靠上的优先级越高

7. **当文件名设置为index.html时，不需要在web.xml文件中进行配置欢迎页面，为什么？**

   1. **因为Tomcat服务器提前配置好了，实际上有两个地方可以配置欢迎页面**
      - 一个是在webapp内部的web.xml文件中   （在这配置属于局部配置）
      - 一个是在CATALINA_HOME/conf/web.xml中进行配置  （属于全部配置）
      - 采用局部优先原则（就近原则）

8. Tomcat服务器的全局欢迎页面是index.jsp  index.htm  index.html

9. 欢迎页面也可以是一个servlet

```xml
    
    <welcome-file-list>
        <welcome-file>servlet的url-pattern（不要头部的“/”）</welcome-file>
    </welcome-file-list>
    
```

