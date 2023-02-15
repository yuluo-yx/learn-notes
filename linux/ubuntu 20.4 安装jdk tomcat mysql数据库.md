# ubuntu 20.4 安装jdk tomcat mysql数据库

## 安装jdk

jdk有很多安装方式，下面只介绍命令行的安装方法

`sudo apt-get install openjdk-8-jdk`

检查：`java -version`

手动解压缩安装时需要配置环境变量

## 搭建ftp服务器

`sudo apt-get install vsftpd`

查看ftp服务器启动状态

`sudo service vsftpd status`

测试连接：ftp:localhost



**使用ftp只能下载无法上传的原因是：write_enable=YES的只是未打开，再次重启服务器即可**

## 安装tomcat

在官网下载tomcat版本

解压 `tar -zxvf + tomact压缩包`

移动到/usr/local/tomcat10

进入bin目录下，启动tomcat服务器

成功！

## 安装数据库

### 安装数据库
```sql
sudo apt-get install mysql-server

sudo apt-get install mysql-server

sudo apt-get install libmysqlclient-dev
```

启动数据库：sudo mysql -uroot -p

**密码为root用户的密码**

### 检查

`检查数据库是否启动
ps -ef | grep mysql`

### 设置远程访问

> 设置mysql可以远程访问
> 	sudo vim /etc/mysql/mysql.conf.d/nysqld.cnf 
> 	注释掉 bind-address = 127.0.0.1

如果是虚拟机，需要将虚拟机的网络设置为桥接模式

开放3306端口
也可以直接关闭防火墙
systemctl stop firewalld
或者只开放3306端口
firewall-cmd --add-port=3306/tcp
注意：可能会提示firewall-cmd命令找不到，可以使用命令：sudo apt-get install firewalld安装。

> 设置数据库可以远程连接
> 	use mysql
> 	update user set host = '%' where user = 'root';
> 	select host, user from user;
> 刷新权限
> 	flush privileges;
> **注意：以上方式在mysql8.0版本会出错**
> 8.0版本要求分开写sql语句即：
> 	create user 'yuluo'@'%' identified by 'password'
> 	grant all privileges on *.* to 'yuluo'@'%';
> 	flush privileges;
>
> 重启数据库
> 	sudo /etc/init.d/mysql restart
> 	或者 sudo service mysql restart

## 部署java web服务

将war放置在tomcat10下的webapps里面

编写数据库文件

启动服务器

访问成功！
