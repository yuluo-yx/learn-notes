# Maven 项目架构管理工具

******************

在项目中导入jar包

约定大于配置

## 1 下载安装maven

下载完成之后，解压即可

[Maven – Download Apache Maven](http://maven.apache.org/download.cgi)

## 2 配置环境变量

M2-HOME maven目录下的bin目录

MAVEN_HOME  maven的目录

在系统的path环境变量下配置%MAVEN_HOME%bin

测试mvn --version

## 3 阿里云镜像

配置国内镜像，下载依赖速度更快

打开 Maven 的配置文件(windows机器一般在maven安装目录的conf/settings.xml)，在`<mirrors></mirrors>`标签中添加 mirror 子节点: 

```xml
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

## 4 本地仓库

建立一个本地仓库

在配置文件中将仓库地址指向你的目录

`<localRepository>F:\Java\Maven\apache-maven-3.8.3\maven-repo</localRepository>`

## 5 IDEA中maven的操作

### 1 创建一个用模板的Maven web项目

### 2 创建一个普通的maven项目

### 3 创建java和resources目录并且标记为源码和资源目录

在项目结构或者右键都可以完成

### 4 在IDEA中配置tomcat

## 6 pom文件

```xml
    <dependencies>
<!--    自动导入配置文件-->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
        </dependency>

        <!-- https://mvnrepository.com/artifact/org.springframework/spring-webmvc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>5.3.10</version>
        </dependency>


    </dependencies>
```

## 7 maven资源导出失败的问题

在build中配置resources文件，防止资源导出失败的问题

