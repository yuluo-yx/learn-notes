# 分布式Session不同步问题

## 流程分析

<img src="C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20221122214410809.png" alt="image-20221122214410809" style="zoom: 67%;" />

## 问题解决：

### session共享问题解决-session复制

<img src="D:\information\Typora笔记\Java\认证服务\分布session不共享问题\分布式Session不同步问题.assets\image-20221123114510894.png" alt="image-20221123114510894" style="zoom:67%;" />

优点：

- web-server（Tomcat）原生支持，只需要修改配置文件即可

缺点：

- session同步需要数据传输，占用大量的网络带宽，降低了服务器群的业务处理能力
- 任意一台web-server保存的数据都是所有的web-server的session总和，受到内存限制无法水平扩展更多的web-server
- 大型分布式项目集群情况下，由于所有的web-server都全量保存数据，所以此方案不可取。

### session共享问题-客户端存储

**session存储在客户端cookie中**

优点：

- 服务器不需存储session，用户保存字的session信息到cookie中，节省服务器资源

缺点：

- 都是缺点，这只是一种实现思路
- 1、每次http请求，携带用户cookie中完整的信息，浪费网络带宽
- 2、session数据存放在cookie中，cookie有长度限制4k，不能保存大量信息
- 3、session数据放在cookie中，存在泄漏、篡改、窃取等安全隐患

> 这种方式不会使用！

### session共享问题解决-hash一致性

优点：

- 只需要修改nginx配置，不需要修改应用代码
- 负载均衡，只要hash属性的值是分布均匀的，多态web-server的负载是均衡的
- 可以支持web-server水平扩展（session同步法手内存限制）

缺点：

- session还是存在web-server中的，所以web-server重启可能导致部分session丢失，影响业务。如部分用户需要重新登录
- 如果web-server水平扩展，rehash之后session需要重新分布，也会有一部分用户路由不到正确的session

> 因为session本来是有有效期的，所以以上缺点问题不是很明显，丢失之后，用户重新登录即可。所以这两种代理方式可以使用

### session共享问题解决-统一存储

<img src="D:\information\Typora笔记\Java\认证服务\分布session不共享问题\分布式Session不同步问题.assets\image-20221123120753434.png" alt="image-20221123120753434" style="zoom:67%;" />

优点：

- 没有安全隐患
- 可以水品扩展，数据库/缓存水平切分即可
- web-server重启或者扩容都不会有session丢失

缺点：

- 增加了一次网络调用，并且需要修改应用代码，如将所有的getsession方法代替为从resdis查数据的方式。redis获取数据比内存慢很多。
- 上面的缺点可以用spring session完美解决。

### 

## 整合Spring Session

官方文档：https://spring.io/projects/spring-session

文档地址：https://docs.spring.io/spring-session/reference/2.7.0/index.html

> 核心原理：
>
> - `@EnableRedisHttpSession`导入`RedisHttpSessionConfiguration`配置、给容器中添加了一个组件
>
>   SessionRepository => `RedisOperationSessionRepository` => redis操作session，session的增删改查封装类
>
> - SessionRepositoryFilter: session存储过滤器，每个请求必须经过filter
>
>   1. 创建得时候，就自动从容器中获取到了SessionRepository
>   2. 原始得request和response都被包装成SessionRepositoryRequestWrapper和SessionRepositoryResponseWrapper
>   3. 以后获取得session，request.getSession() 等于调用
>   4. wrappedRequest.getSession(); spring session重写了getSession()方法。 是从 RedisOperationSessionRepository 中获取的，就是从redis中获取的。
>
> - session自动延期等功能，在对session中的数据进行操作了之后，就会对session进行延期。

```java
// Spring Session核心原理解读
@Override
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) {
    
    request.setAttribute(SESSION_RePOSITORY_ATTR, this.sessionRepository);
    
    // 包装原始请求对象  装饰者模式
    SessionRepositoryRequestWrapper wrappedRequest = new SessionRepositoryRequestWrapper(request, response, this.servletContext);
    // 包装原始得响应对象
    SessionRepositoryResponseWrapper wrapperResponse = new SessionRepositoryResponseWrapper(wrapperRequest, response);
    
    try {
        // 放行包装过后得对象
        filterChain.doFilter(wrappedRequest, wrappedRequest);
    }
    finally {
        wrappedRequest.commitSession();
    }
}
```

