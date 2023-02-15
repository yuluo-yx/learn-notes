# 安装tomcat服务器

## 1 下载tomcat文件

> 到官网下载，可以下载最新的tomcat10的版本
>
> [Apache Tomcat® - Apache Tomcat 10 Software Downloads](https://tomcat.apache.org/download-10.cgi)

##  2 解压缩要安装的tomcat到指定的文件夹

**安装软件时一般注意三个步骤： 1 安装 or 解压缩 2 查看配置文件或者目录结构 3 启动安装的软件**

>  bin --->  二进制文件包，启动和关闭的脚本文件
>
> conf ---> 配置文件
>
> lib ---> 依赖的jar包
>
> logs ---> 日志文件
>
> webapps --> 存放网站信息

## 3 启动 和 关闭

启动 startup.bat

> 如果启动失败。出现闪退情况，查看是否有JAVA_HOME和JRE_HOME的环境变量
>
> 出现乱码的情况：修改conf里面的server.xml配置文件

关闭 shutdown.bat 或者关闭tomcat的命令窗口

可以配置启动的端口号和启动的网址

* tomcat 的默认端口号是：8080

* mysql：3306

* http:80

* https:443

    <Connector port="8080" protocol="HTTP/1.1"

  ​        connectionTimeout="20000"

  ​        redirectPort="8443" />

  可以配置的主机的名称： 在C:/system32/divers/etc/hosts中修改指向的域名

  DNS: 全世界的域名管理  **14.215.177.39.443**

> 网站是如何访问的
>
> 1. 输入一个域名，回车
>
> 2. 检查主机C:/system32/divers/etc/hosts中的配置文件有没有这个域名映射
>
>    * 1 有：直接返回对应的域名映射，这个地址中，有我们需要访问的web程序，可直接访问
>
>    ​	127.0.0.1    localhost
>
>    * 2 没有：去DNS服务器上找，找到就返回，找不到就返回找不到

## 4 可以配置一下环境变量，方便随时启动（可选性）

## 5 发布网站

> 1. 将自己写的网页放到tomcat服务器中指定的web应用文件夹下（webapps）下，就可以访问了
>
>    网站的结构
>
>    ​	--webapps: Tomcat服务器的web目录
>
>    ​		-ROOT
>
>    ​		-yuluo  :  网站的目录名
>
>    ​				-web-inf
>
>    ​					-CLASSES : java程序
>
>    ​					-lib.web 应用程序依赖的jar包
>
>    ​					-web.xml : 网站的配置文件
>
>    ​		-index.html 默认的首页
>
>    ​		-static 
>
>    ​			-css
>
>    ​					-style.css
>
>    ​			-js
>
>    ​			-img

