# java web的继承结构

******************

```java
jakarta.servlet.Servlet(接口)【爷爷】

jakarta.servlet.GenericServlet implements Servlet(抽象类)【儿子】

jakarta.sevlet.http.HttpServlet extends GenericServlet(抽象类) 【孙子】



以后编写的Servlet要继承httpServlet类
```

