# docker 

## 安装docker

在win10环境下开启 Hyper-V功能

> Hyper-V 是微软开发的虚拟机，类似于 VMWare 或 VirtualBox，仅适用于 Windows 10。这是 Docker Desktop for Windows 所使用的虚拟机。但是，这个虚拟机一旦启用，QEMU、VirtualBox 或 VMWare Workstation 15 及以下版本将无法使用！如果你必须在电脑上使用其他虚拟机（例如开发 Android 应用必须使用的模拟器），请不要使用 Hyper-V！

双击下载好的docker安装文件。

## docker三要素

1. 镜像：相当于java类
2. 容器：基于类创建出来的java 对象
3. 仓库：存放镜像

## dockerv 帮助启动类命令

**linux启动docker服务**

启动：systemctl start docker

停止：systemctl stop docker

重启：systemctl restart docker

查看docker状态：systemctl status docker

开机启动：systemctl enable docker

查看docker概要信息：docker info

查看docker帮助文档：docker --help

查看docker具体命令的帮助文档：docker 具体命令 --help

## 镜像命令

docker images：列出本地主机上的镜像

> 各个选项说明
>
> | REPOSITORY       | TAG              | IMAGE ID | CREATED      | SIZE     |
> | ---------------- | ---------------- | -------- | ------------ | -------- |
> | 表示镜像的仓库源 | 镜像的标签版本号 | 镜像ID   | 镜像创建时间 | 镜像大小 |
>
> 同一个仓库源可以有多个TAG版本，代表这个仓库源的不同版本，我们使用REPOSITORY:TAG来定义不同的镜像，不指定tag的时候，默认使用ubuntu:latest镜像
>
> 操作参数说明
>
> -a 列出本地所有的镜像，包含历史镜像
>
> -q 只显示镜像id

docker search 某个服务镜像名字

> docker search hello-world
>
> 在远程仓库查找hello-world镜像
>
> 各个选项说明：
>
> | NAME     | DESCRIPTION | STARS    | OFFICIAL         | AUTOMATED        |
> | -------- | ----------- | -------- | ---------------- | ---------------- |
> | 镜像名称 | 镜像说明    | 点赞数量 | 是否是官方认证的 | 是否是自动构建的 |
>
> 操作参数说明
>
> docker search --limit 5 redis  （只显示点赞数前5的docker镜像）

docker pull 某个服务的镜像名字

> docker pull 镜像名字[:TAG]   TAG指定版本
>
> docker pull 镜像名字  默认为XXX:latest (最新的)

docker system df 查看镜像/容器/数据卷所占的空间

docker rmi 某个服务镜像名字id

> 删除多个
>
> docker rmi (IMAGE ID)
>
> docker rmi  feb5d9fea6a5
>
> 删除多个
>
> docker rmi 镜像名1[:TAG] 镜像名2[:TAG]
>
> 全部删除
>
> docker rmi -f $(docker images -qa)
>
> 操作参数说明
>
> -f 强制删除

## 容器命令

docker run

> 操作参数说明
>
> --name="容器新名字"  为容器指定一个名称
>
> -d：后台运行容器并且返回容器ID，即启动守护式容器
>
> -i：以交互模式运行，通常与-t同时使用
>
> -t：为容器重新分配一个伪输入终端，通常与0i同时使用
>
> -P：随机端口映射，大写P
>
> -p：指定端口映射，小写p
>
> | 参数                      | 说明                                              |
> | ------------------------- | ------------------------------------------------- |
> | -p hostPort:containerPort | -p 8080: 80 (宿主机端口 ：docker内部机器暴露端口) |
> |                           |                                                   |
>
> `docker run -it --name="yuluoUbuntu" ubuntu /bin/bash `
>
> 创建交互的bash命令行界面，设置容器名字为yuluoUbuntu，在系统中：常用的命令行命令都可以使用。需要退出终端 使用`exit`

docker ps

> 列出正在运行的容器id
>
> 操作参数说明
>
> -a 列出正在运行的容器id+历史运行的容器id
>
> -n 显示最近n个创建的容器
>
> -q 静默模式，只显示容器编号

退出方式：

> 1. exit: run进去容器，exit退出，容器停止
> 2. ctrl + p + q run进去容器，退出之后，容器不停止

启动已经停止的容器

> docker start 容器ID或者容器名

重启容器

> docker restart 容器ID或者容器名

停止容器

> docker stop 容器ID或者容器名

删除已经停止的容器

> docker rm 容器ID或者容器名

强制停止容器

> docker kill 容器ID或者容器名

重新进入正在运行的容器：

**exec: 在容器中打开一个新的终端，并且可以启动新的进程，用exit命令退出，不会导致容器的停止  （用的多）**

> docker exec -it 容器id  /bin/bash

**attach：直接进入容器启动命令的终端，不会启动新的进程，用exit退出，会导致容器的停止**

>  docker attach

查看容器id

> docker logs 容器id

查看容器内运行的进程

> linux中的查看进程命令：top
>
> docker中： docker top

查看容器内部的运行细节

> docker inspect 容器id

从容器内拷贝文件到主机上

> 容器  -> 主机
>
> docker cp 容器id：容器内路径 目的主机路径

导出容器

> export: 导出容器的内容留作为一个tar归档文件  【对应import命令】
>
> import从tar包中的内容创建一个新的文件系统再次导入镜像【对应export命令】
>
> docker export 容器id > 文件名.tar
>
> cat 文件名.tar | docker import - 镜像用户/镜像名：镜像版本号

## DockerFile文件

概述：是用来构建docker镜像的文本文件，是由一条条构建镜像所需要的指令和参数构成的脚本文件

构建步骤：

	1. 编写Dockerfile文件
	1. docker build构建镜像
	1. docker run运行镜像

基础知识：

- 每条保留字指令都必须为大写字母且后面要跟随至少一个参数
- 指令按照从上到下，顺序执行
- \# 表示注释
- 每条指令都会创建一个新的镜像层文件并对镜像进行提交

Dockerfile文件按保留字：

```markdown
FROM：继承的父类，表明当前镜像来自于那，指定一个已经存在的镜像作为模板，第一条必须是FROM
MAINTAINER：镜像维护者的名字和邮箱地址
RUN：在构建时需要执行的命令
	两种格式：
		shell格式：（等同于在shell里面的命令）安装vim RUN apt-get install vim
		exec格式
	RUN是在docker build时执行的
EXPOSE：暴露端口
WORKDIR：指定在创建容器之后，终端默认登录进来的工作目录，一个落脚点
USER：指定该镜像以什么样的用户去执行，如果都不指定，默认是root用户
ENV：用来在构建过程中设置环境变量，用键值的方式设置
ADD：将宿主机目录下的文件拷贝进镜像且自动处理url和解压jar包
COPY：类似与ADD
VOLUME：容器数据卷，用于数据保存和持久化操作
CMD：指定容器启动之后要干的事情，支持shell和exec格式，【DockerFile中可以有多个command指令，只有最后一个生效】
	和run命令的区别
		run：在docker build时执行
		cmd：在docker run时运行
ENTRYPOINT：也是用来指定一个容器启动时运行的命令，将输入的命令搞成可变参数运行，不会覆盖之前的cmd命令
```

根据Dockerfile构建镜像

> docker build -t 新镜像名字:tag(版本)

## Docke微服务实战

### 在IDEA中打包jar包

### 编写DockerFile

```dockerfile
# 基础镜像使用java8
FROM java:8

# 作者
MAINTAINER yuluo

# 将jar包添加到容器中并更名为yuluo_docker_test.jar
ADD docker-boot-0.0.1-SNAPSHOT.jar yuluo_docker_test.jar

# 运行jar包
RUN bash -c 'touch /yuluo_docker_test.jar'
ENTRYPOINT ["java", "- jar", "yuluo_docker_test.jar"]

# 暴露6001端口为微服务端口
EXPOSE 6001
```

### 构建镜像

> docker build -t yuluo_docker_test:1.0 .
>
> 最后的 . 指出文件夹路径
>
> 使用docker images即可看到自己构建的镜像

运行容器

> docker run -d -p 6001:829 1d46e90b06e2
>
> 输入http://localhost:6001/index即可访问到docker容器yuluo_docker_test里的微服务内容

## Docker轻量级管理工具portainer

概述：提供了图形化界面管理，用于方便的管理Docker环境，包括单机和集群环境

安装方式：

```dockerfile
docker run -d -p 8000:8000 -p 9000:9000    （指定两个映射端口）

--name portainer 

--restart    （跟随docker的重启儿重启）

-v /var/run/docker.sock:/var/run/docker.sock 

-v portainer_data:/data   （容器数据卷挂载）

portainer/portainer
```

自己环境下的portainer

```dockerfile
docker run -d -p 9000:9000 --name portainer --restart -v /var/run/docker.sock:/var/run/docker.sock -v E:\docker\portainer_data:/data portainer/portainer
```

使用方式：访问http://localhost:9000 输入username和password

## docker查看容器日志

docker logs --since 10 id

10表示时间，1o分钟

## docker 网络命令

docker network create nginx

创建一个名为nginx的网络环境
