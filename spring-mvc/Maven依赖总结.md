# ssm配置信息

## 创建ssm web项目

**创建web项目的时候确认文件编码为UTF-8，避免出现错误，确保JDK的版本一致**

1. 新建一个普通的maven项目
2. 在pom文件中添加<packaging>war</packaging>，引入依赖
3. 添加web模块
   - 在main目录中新建一个目录webapp，注意是否是web目录
   - 在Project Structur中的Moudles里面对应项目的web中Deployment Descriptors中点击+号新建一个web.xml文件，选择的路径是\src\main\webapp\WEB-INF\web.xml

## 创建java web servlet项目

1. 新建一个project，新建一个空工程，然后在空工程下新建module，
2. 新建模块
   - 这里新建的是一个普通的java se模块，
3. 让module变成javaEE模块，让Moudle变成webapp的模块，符合webapp规范，
   - 在Module上右击：选择Add Framework Support （添加框架支持）
   - 选择web application （添加web框架的支持）
   - IDEA会自动生成一个符合servlet规范的webapp目录结构
4. 添加jar包支持
   - 将tomcat里面CATALINA_HOME\lib\servlet-api.jar和jsp-api.jar添加到classpath中
   - 这里的IDEA指的是IDEA的classpath
   - project Structure中选择modules在Dependencies中点击+号选择添加jar

## 一个配置文件整合

### spring mvc 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd">

    <!--spring MVC的配置文件，包含网站跳转逻辑的控制，配置-->
    <context:component-scan base-package="indi.yuluo" use-default-filters="false">
        <!--只扫描控制器-->
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--配置视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/templates/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

    <!--两个标准配置-->
    <!--将springMVC不能处理的资源交给tomcat来处理-->
    <mvc:default-servlet-handler/>

    <!--支持springMVC的更加高级的功能， ajax的请求，JSR303校验-->
    <mvc:annotation-driven/>

</beans>
```

### web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--1，启动spring的容器-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring/applicationContext.xml</param-value>
    </context-param>

    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!--2，springMVC的前端控制器,拦截所有请求-->
    <servlet>
        <servlet-name>springDispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <!--指定spring mvc配置文件的位置-->
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/springMVC-config.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>springDispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--3，字符编码过滤器 一定要放在所有的过滤器之前-->
    <filter>
        <filter-name>CharacterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceRequestEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
        <init-param>
            <param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>CharacterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--4、使用Rest风格的url，将页面普通的post请求转为delete或者put请求-->
    <filter>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>HiddenHttpMethodFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

</web-app>
```



### spring

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:aop="http://www.springframework.org/schema/aop" xmlns:tx="http://www.springframework.org/schema/tx"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">

    <!--spring的配置文件，主要-->

    <!--扫描除了controller之外的所有包-->
    <context:component-scan base-package="indi.yuluo">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>

    <!--引入数据库链接配置-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--数据源，事务控制，……-->
    <bean id="comboPooledDataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"/>
        <property name="driverClass" value="${jdbc.driverClass}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!--配置和mybatis的整合-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <!--指定mybatis全局配置文件的位置-->
        <property name="configLocation" value="classpath:mybatis/mybatisConfig.xml"/>
        <!--指定数据库连接池-->
        <property name="dataSource" ref="comboPooledDataSource"/>
        <!--指定mapper文件的位置-->
        <property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"/>
    </bean>

    <!--配置一个可以批量执行的sqlsession，方便以后插入操作-->
    <bean id="sqlSessionTemplate" class="org.mybatis.spring.SqlSessionTemplate">
        <constructor-arg name="sqlSessionFactory" ref="sqlSessionFactory"/>
        <constructor-arg name="executorType" value="BATCH"/>
    </bean>

    <!--配置扫描器，将mybatis接口的实现加入到ioc容器中-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--扫描所有的dao接口的实现类，加入到ioc容器中-->
        <property name="basePackage" value="indi.yuluo.crud.dao"/>
    </bean>

    <!--事务控制的配置 这里的id名字必须是transactionManager 其他的报错-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--控制数据源-->
        <property name="dataSource" ref="comboPooledDataSource"/>
    </bean>
    <!--开启基于注解的事务控制 使用xml配置形式的事务（必要主要的都是使用配置）-->

    <!--配置事务增强，事务如何切入-->
    <tx:advice id="txAdvice">
        <tx:attributes>
            <!--所有方法都是事务方法-->
            <tx:method name="*"/>
            <!--以get开头的所有方法-->
            <tx:method name="get*" read-only="true"/>
        </tx:attributes>
    </tx:advice>

    <aop:config>
        <!--切入点表达式-->
        <aop:pointcut id="txPoint" expression="execution(* indi.yuluo.crud.service..*(..))"/>
        <!--配置事务增强-->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="txPoint"/>
    </aop:config>

    <!--spring配置文件的核心点
        1，数据源
        2，与mybatis的整合
        3，事务控制
    -->

</beans>
```

### mybatis

**注意：数据库连接池的配置全部交给spring去管理，所以在这里不需要配置数据库连接**

**mybatis的配置文件选项是有先后限制的**

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!--mybatis连接信息的属性配置文件-->
    <properties resource=""/>

    <!--mybatis全局设置-->
    <settings>
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>

    <!--类型别名-->
    <typeAliases>
        <package name="indi.yuluo.crud.bean"/>
    </typeAliases>

    <!--引入分页插件-->
    <plugins>
        <plugin interceptor="com.github.pagehelper.PageInterceptor">
            <!--调整分页参数合理化， 不能到达不正确的页码-->
            <property name="reasonable" value="true"/>
        </plugin>
    </plugins>

</configuration>
```



### jdbc.properties

**注意mysql8的时区配置 url后面加 &serverTimezone=Asia/shanghai**

```properties
jdbc.jdbcUrl=jdbc:mysql://localhost:3306/ssm?useSSL=true&useUnicode=true&characterEncoding=utf8
jdbc.driverClass=com.mysql.cj.jdbc.Driver
jdbc.username=root
jdbc.password=w082916x
```

## 多个文件进行整合

### mybatis-config

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!--spring配置数据源-->

    <!--类型别名-->
    <typeAliases>
        <package name="indi.yuluo.pojo"/>
    </typeAliases>

    <!--mapper文件映射-->
    <mappers>
        <mapper class="indi.yuluo.dao.BookMapper"/>
    </mappers>

</configuration>
```

### spring-dao

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--关联数据库配置文件-->
    <context:property-placeholder location="classpath:jdbc.properties"/>

    <!--连接池-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="driverClass" value="${jdbc.jdbcUrl}"/>
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"/>
        <property name="user" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>

    <!--sqlSessionFactory-->
    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"/>
        <!--绑定mybatis的配置文件-->
        <property name="configLocation" value="classpath:mybatis/mybatis-config.xml"/>
        <!--指定mapper文件的位置-->
        <property name="mapperLocations" value="classpath:mybatis/mapper/*.xml"/>
    </bean>

    <!--配置dao接口扫描，动态的dao接口可以注入到spring的容器中-->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <!--注入sqlSessionFactory-->
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory"/>
        <!--扫描doa接口包-->
        <property name="basePackage" value="indi.yuluo.dao"/>
    </bean>

</beans>
```

### spring-service

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--扫描service下面的包-->
    <context:component-scan base-package="indi.yuluo.service"/>

    <!--将我们所有的业务类注入到spring-->
    <bean id="BookServiceImpl" class="indi.yuluo.service.impl.BookServiceImpl">
        <property name="bookMapper" ref="bookMapper"/>
    </bean>

    <!--声明式事务管理配置-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <!--注入数据源-->
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!--aop事务支持-->

</beans>
```

### applicationContext

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!--整合-->
    <import resource="classpath:spring/spring-dao.xml"/>
    <import resource="classpath:spring/spring-service.xml"/>
    <import resource="classpath:springMVC/springMVC-config.xml"/>


</beans>
```

### springMVC

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/tool http://www.springframework.org/schema/tool/spring-tool.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <!--注解驱动-->
    <mvc:annotation-driven/>

    <!--静态资源过滤-->
    <mvc:default-servlet-handler/>

    <!--扫描包-->
    <context:component-scan base-package="indi.yuluo.controller"/>

    <!--视图解析器-->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/templates/"/>
        <property name="suffix" value=".jsp"/>
    </bean>

</beans>
```

### jdbc.properties

```properties
jdbc.jdbcUrl=jdbc:mysql://localhost:3306/ssm
jdbc.driverClass=com.mysql.cj.jdbc.Driver
jdbc.username=root
jdbc.password=w082916x
```

### web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">

    <!--DispatchServlet-->
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:/spring/applicationContext.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

    <!--乱码过滤-->
    <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>utf-8</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>

    <!--session超时设置-->
    <session-config>
        <session-timeout>15</session-timeout>
    </session-config>

</web-app>
```

## 如果报错

错误信息为：

> org.springframework.beans.factory.BeanDefinitionStoreException: IOException parsing XML document from class path resource [spring/applicationContext.xml]; nested exception is ![img](file:///C:\Users\14815\AppData\Roaming\Tencent\TIM\Temp\%W@GJ$ACOF(TYDYECOKVDYB.png)com.sun.org.apache.xerces.internal.impl.io.MalformedByteSequenceException: 2 字节的 UTF-8 序列的字节 2 无效。

**2 字节的 UTF-8 序列的字节 2 无效。**

在pom文件中添加配置

```xml
<properties>

    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

</properties>
```

## 常用jar包配置组合，避免jar冲突问题

###  ssm

```xml
<!--导入依赖-->
<dependencies>

<!--springMVC, spring相关的jar包-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
    <version>4.3.7.RELEASE</version>
</dependency>
<!--spring JDBC-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>4.3.7.RELEASE</version>
</dependency>
<!--spring-test spring的单元测试jar包-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-test</artifactId>
    <version>4.3.7.RELEASE</version>
</dependency>
<!--spring面向切面编程模块-->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>4.3.7.RELEASE</version>
</dependency>

<!--mybatis相关的jar包-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.4.2</version>
</dependency>
<!--分页PagerHelper插件-->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.3.0</version>
</dependency>
<!--引入mybatis整合spring的适配包-->
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis-spring</artifactId>
    <version>1.3.1</version>
</dependency>
<!--数据连接池驱动-->
<dependency>
    <groupId>c3p0</groupId>
    <artifactId>c3p0</artifactId>
    <version>0.9.1</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.25</version>
</dependency>

<!--web开发的其他包-->
<dependency>
    <groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.13.2</version>
    <scope>test</scope>
</dependency>
<!-- https://mvnrepository.com/artifact/javax.servlet.jsp.jstl/jstl -->
<dependency>
    <groupId>javax.servlet.jsp.jstl</groupId>
    <artifactId>jstl</artifactId>
    <version>1.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/taglibs/standard -->
<dependency>
    <groupId>taglibs</groupId>
    <artifactId>standard</artifactId>
    <version>1.1.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/javax.servlet.jsp.jstl/jstl-api -->
<dependency>
    <groupId>javax.servlet.jsp.jstl</groupId>
    <artifactId>jstl-api</artifactId>
    <version>1.2</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.glassfish.web/jstl-impl -->
<dependency>
    <groupId>org.glassfish.web</groupId>
    <artifactId>jstl-impl</artifactId>
    <version>1.2</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>3.0.1</version>
    <scope>provided</scope>
</dependency>
<!--jackson json数据解析jar包-->
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.12.5</version>
</dependency>


<!--mybatis逆向工程需要的jar包-->
<!-- https://mvnrepository.com/artifact/org.mybatis.generator/mybatis-generator-core -->
<dependency>
    <groupId>org.mybatis.generator</groupId>
    <artifactId>mybatis-generator-core</artifactId>
    <version>1.3.7</version>
</dependency>

<!--JSR303校验的依赖jar包-->
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>5.4.1.Final</version>
</dependency>

</dependencies>
```

## 完整的spring命名空间的配置

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-4.2.xsd
       http://www.springframework.org/schema/mvc
       http://www.springframework.org/schema/mvc/spring-mvc-4.2.xsd
       http://www.springframework.org/schema/context
       http://www.springframework.org/schema/context/spring-context-4.2.xsd">
```

## maven的静态资源导出问题

在pom文件里加入下列代码

```xml
<!--静态资源导出问题-->
<build>
    <resources>
        <resource>
            <directory>src/main/java</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
        <resource>
            <directory>src/main/resources</directory>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>true</filtering>
        </resource>
    </resources>
</build>
```

