# ssm拦截器总结

## 使用注解

在类或者需要跨域的方法上加入`@CrossOrigin`注解来实现跨域



## 全局跨域处理

```java
package indi.yuluo.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

/**
 * @author: yuluo
 * @FileName: CorsConfig.java
 * @createTime: 2022/6/6 20:54
 * @Description:
 */

@Configuration
public class CorsConfig implements WebMvcConfigurer {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")
                .allowCredentials(true)
                .allowedOrigins("*")
                .allowedMethods(new String[]{"GET", "POST", "PUT", "DELETE"})
                .allowedHeaders("*")
                .exposedHeaders("*");
    }
}
```

## 在mvc的配置文件中配置

```xml
<!-- 接口跨域配置 -->
<mvc:cors>
    <mvc:mapping path="/**"
                 allowed-origins="*"
                 allowed-methods="POST, GET, OPTIONS, DELETE, PUT"
                 allowed-headers="Content-Type, Access-Control-Allow-Headers, Authorization, X-Requested-With"
                 allow-credentials="true" />
</mvc:cors>
```



## 配置bean

```java
package com.atguigu.guli.apigate.config;
 
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.reactive.CorsWebFilter;
import org.springframework.web.cors.reactive.UrlBasedCorsConfigurationSource;
 
@Configuration
public class Cosconfig {
    @Bean
    public CorsWebFilter corsWebFilter() {
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        // 配置跨域
        CorsConfiguration corsConfiguration = new CorsConfiguration();
        // 允许哪个请求头
        corsConfiguration.addAllowedHeader("*");
        // 允许哪个方法进行跨域
        corsConfiguration.addAllowedMethod("*");
        // 允许哪个请求来源进行跨域
        corsConfiguration.addAllowedOrigin("*");
        // 是否允许携带cookie进行跨域
        corsConfiguration.setAllowCredentials(true);
        // 存活时间
        corsConfiguration.setMaxAge(18000L);
        source.registerCorsConfiguration("/**", corsConfiguration);
        return new CorsWebFilter(source);
    }
 
 
}
```



## 编写过滤器解决跨域问题

```java
package indi.yuluo.filter;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpMethod;
import org.springframework.http.HttpStatus;
import org.springframework.util.AntPathMatcher;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * @author: yuluo
 * @FileName: SimpleCORSFilter.java
 * @createTime: 2022/6/5 11:16
 * @Description:
 */

@Slf4j
public class SimpleCORSFilter implements Filter {
    private boolean isCross = false;

    // 路径匹配器，支持通配符
    public static final AntPathMatcher PATH_MATCHER = new AntPathMatcher();

    @Override
    public void destroy() {
        isCross = false;
    }

    // 定义白名单
    String[] uri = new String[]{
            "/login",
            "/logout"
    };

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        if (isCross) {
            HttpServletRequest httpServletRequest = (HttpServletRequest) request;
            HttpServletResponse httpServletResponse = (HttpServletResponse) response;

            String servletPath = httpServletRequest.getServletPath();

            System.out.println("拦截请求: " + servletPath);

            for (String s : uri) {
                // 匹配上为true
                boolean match = PATH_MATCHER.match(s, servletPath);
                if (!match) {
                    httpServletResponse.setCharacterEncoding("utf-8");
                    httpServletResponse.setHeader("Access-Control-Allow-Origin", "*");
                    httpServletResponse.setHeader("Access-Control-Allow-Methods", "POST, GET, OPTIONS, DELETE, PUT, HEAD");
                    httpServletResponse.setHeader("Access-Control-Max-Age", "0");
                    httpServletResponse.setHeader("Access-Control-Allow-Headers",
                            "Origin, Authorization, No-Cache, X-Requested-With, If-Modified-Since, Pragma, Last-Modified, Cache-Control, Expires, Content-Type, X-E4M-With,userId,token");
                    httpServletResponse.setHeader("Access-Control-Allow-Credentials", "true");
                    httpServletResponse.setHeader("XDomainRequestAllowed", "1");

                    // 如果是OPTIONS
                    System.out.println(httpServletRequest.getMethod() + ":" + HttpMethod.OPTIONS + "===" + HttpMethod.OPTIONS.toString().equals(httpServletRequest.getMethod()));
                    if (HttpMethod.OPTIONS.toString().equals(httpServletRequest.getMethod())) {
                       log.info("处理预检请求——————————————————————————————");
                        httpServletResponse.setStatus(HttpStatus.NO_CONTENT.value());
                    }
                }
            }
        }
        chain.doFilter(request, response);
    }

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        String isCrossStr = filterConfig.getInitParameter("IsCross");
        isCross = isCrossStr.equals("true") ? true : false;
        System.out.println(isCrossStr);
    }

}
```

在web.xml中配置

```xml
<!-- 跨域请求 -->
<filter>
    <filter-name>SimpleCORSFilter</filter-name>
    <filter-class>indi.yuluo.filter.SimpleCORSFilter</filter-class>
    <init-param>
        <param-name>IsCross</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>SimpleCORSFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

## 需要注意的问题

对静态资源的访问是可以跳过的，不需要拦截

对登录和退出请求不需要拦截



**以上三种任意一种即可解决问题**