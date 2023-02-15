# MyBatis配置文件

- mybatis的配置文件有两大类
  - mybatis主配置文件  **全局设置**  标签出现次序是有规定的
  - mybatis的mapper文件  **sql语句**

## settings 

mybatis的全局设置

```xml
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

## 环境配置

```xml
<!--default 环境标识 默认的是development-->
<environments default="development">
    <!--id指的是下面这个环境的名称 自定义的环境表标识-->
    <environment id="development">
        <!--transactionManager 事务管理器
			type : 表示的是事务管理器的类型
			JDBC：属性值1 使用的connection对象 由mybatis自己完成事务的处理
			MANAGED：属性值2 管理 表示把事务的处理交给容器实现（其他的软件玩车个事务的提交，回滚。 例如spring）
		-->
        <transactionManager type="JDBC"/>
        <!--配置数据源
			type：表示数据源的类型
			POOLED：属性值1 mybatis会在内存中创建PooledDataSource类，管理多个connecion对象 使用的是连接池
			UNPOOLED：属性值2 不使用连接池，mybatis创建一个UnPooledDataSource类，每次执行sql语句先创建connection对象，执行完毕之后，关闭connection对象
		-->
        <dataSource type="POOLED">
            <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
            <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false"/>
            <property name="username" value="root"/>
            <property name="password" value="w082916x"/>
        </dataSource>
    </environment>
    
    		<!--项目上线之后的数据库-->
            <environment id="online">
            <transactionManager type="JDBC"/>
            <!--配置数据源-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="w082916x"/>
            </dataSource>
        </environment>
    
</environments>
```

可以在environments中设置多个数据库的连接配置

一个environment标签是一个数据库的连接信息

## 用属性配置文件进行数据库连接信息的配置

属性配置文件

```properties
mysql.Driver=com.mysql.cj.jdbc.Driver
mysql.url=jdbc:mysql://localhost:3306/mybatis
mysql.username=root
mysql.password=w082916x
```

mybatis配置文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>

    <!--mybatis连接信息的属性配置文件-->
    <properties resource="mybatisDataSources.properties"/>

    <settings>
        <setting name="logImpl" value="STDOUT_LOGGING"/>
    </settings>

    <!--类型别名-->
    <typeAliases>
        <!--第一种语法格式
            type：java类型的全限定名称（自定义类型）
            alias：自定义别名
        -->
        <!--<typeAlias type="indi.yuluo.domain.Student" alias="stu"/>-->

        <!--别名就是每个类的类名的小写-->
        <package name="indi.yuluo.domain.Student"/>

    </typeAliases>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <!--配置数据源-->
            <dataSource type="POOLED">
                <!--<property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useSSL=false"/>
                <property name="username" value="root"/>
                <property name="password" value="w082916x"/>-->
                <!--使用属性配置文件-->
                <property name="driver" value="${mysql.Driver}"/>
                <property name="url" value="${mysql.url}"/>
                <property name="username" value="${mysql.username}"/>
                <property name="password" value="${mysql.password}"/>
            </dataSource>
        </environment>
    </environments>

    <!--指定其他mapper文件的位置，找到其他的sql语句位置-->
    <mappers>
        <mapper resource="indi/yuluo/dao/StudentDao.xml"/>
    </mappers>

</configuration>
```

## mapper文件指定格式

第一种：resources=“mapper文件的路径”

```xml
<!--指定其他mapper文件的位置，找到其他的sql语句位置-->
<mappers>
    <mapper resource="indi/yuluo/dao/StudentDao.xml"/>
</mappers>
```

第二种：使用packsge标签 name：mapper文件在的包名

- 使用要求：
  - mapper文件和dao接口在同一目录
  - mapper文件和dao接口的名称完全一样

```xml
<!--指定其他mapper文件的位置，找到其他的sql语句位置-->
<mappers>
    <mapper package="indi.yuluo.dao"/>
</mappers>
```

