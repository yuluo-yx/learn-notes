# Windows安装nginx服务器

## 下载nginx：

网址：http://nginx.org/en/download.html

## 解压启动服务

下载之后解压，进入相应的目录之中，启动nginx服务器

```xml
cd nginx-1.19.2

# 启动nginx服务器
start nginx

# 查看nginx的运行状态
nginx -t
```

官方推荐的查看进程命令：

`tasklist /fi "imagename eq nginx.exe"`

## 访问测试

默认监听的是80端口，(如果端口被占用，修改带其他端口[conf/nginx.conf]) 浏览器输入：http://127.0.0.1

## nginx的控制命令

nginx -s reopen #重启Nginx

nginx -s reload #重新加载Nginx配置文件，然后以优雅的方式重启Nginx

nginx -s stop #强制停止Nginx服务

nginx -s quit #优雅地停止Nginx服务（即处理完所有请求后再停止服务）

nginx -t #检测配置文件是否有语法错误，然后退出

nginx -?,-h #打开帮助信息

nginx -v #显示版本信息并退出

nginx -V #显示版本和配置选项信息，然后退出

nginx -t #检测配置文件是否有语法错误，然后退出

nginx -T #检测配置文件是否有语法错误，转储并退出

nginx -q #在检测配置文件期间屏蔽非错误信息

nginx -p prefix #设置前缀路径(默认是:/usr/share/nginx/)

nginx -c filename #设置配置文件(默认是:/etc/nginx/nginx.conf)

nginx -g directives #设置配置文件外的全局指令

killall nginx #杀死所有nginx进程

## 配置ipv6解析

在配置文件中加入

```mar
    server {
        listen       80;

        # 加入ipv6监听
        listen [::]:80 ipv6only=on;

        server_name  localhost
```

再次以ipv6访问 ，http://[fe80::1845:8706:61e6:79ab]/  进入成功

