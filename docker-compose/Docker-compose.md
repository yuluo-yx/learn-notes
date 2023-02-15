# Docker-compose

## 示例

### 配置文件

### data.json

```
 {
     "name": "The docker-compose study!",
     "tags": ["software", "devops"],
     "author": {
         "first_name": "Mosh",
         "last_name": "Hamedani"
     }
 }
```

### yaml

```
 name: The docker-compose study!
 tags: 
     - software
     - devops
 author:
     first_name: Mosh,
     last_name: Hamedani
```

### 编写docker-compose.yml

根据文档，查看docker-enginee版本，确定compose文件格式

```
 # compose文件格式
 version: "3.8"
 
 # 服务块
 service:
     web:
         # 直接运行当前文件下frontend文件里面的DockerFile文件
         build: ./frontend
         # 指定端口
         ports: 
             - 3000:3000
     api:
         build: ./backend
         ports:
             - 3001:3001
         # 指定环境信息 在此例中是数据库信息 db是主机域名
         envirment:
             DB_URL: mongodb://db/dbname
     db:
         # 从dockerhup获取 这是linux镜像，windows镜像大些
         image: mongo:4.0-xenial
         ports: 
             - 27017:27017
         # 指定卷挂载
         volumes:
             - dbname：/data/db
 
 # 需要先定义卷，之后才能使用！
 volumes:
     dbname:
```

### 运行

```
 # 构建
 docker-compose build
 
 # 查看构建的镜像
 docker-compose image
 
 # 运行
 docker-compose up
 
 # 查看运行的容器
 docker-compoes ps
 
 # 后台运行
 docker-compose up -d
 
 # 停止并删除容器，但镜像仍然存在
 docker-compose down
```

### 进入容器

进入web ping api主机

```
 # 需要root用户权限进入
 docker exec -it -u root continer-name sh
```

> **因为docker中自带一个dns服务器，所以使用容器名是可以ping通主机的！**

## docker-compose安装

本文介绍shell的安装方式，更多方式查看官方文档：https://docs.docker.com/compose/install/

```shell
sudo curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```

为安装脚本添加权限

```shell
chmod +x /usr/local/bin/docker-compose
```

执行完之后，可以使用

```shell
docker-compose --version
```

查看安装是否成功

> 也可以采用`pip install docker-compose`的安装方式，需要先安装python环境

## Docker-compose命令

### `build`

构建或重新构建服务。

服务一旦构建后，将会带上一个标记名，例如 web_db。

可以随时在项目目录下运行 `docker-compose build` 来重新构建服务。

### `help`

获得一个命令的帮助。

### `kill`

通过发送 `SIGKILL` 信号来强制停止服务容器。支持通过参数来指定发送的信号，例如

```sh
$ docker-compose kill -s SIGINT
```

### `logs`

查看服务的输出。

### `port`

打印绑定的公共端口。

### `ps`

列出所有容器。

### `pull`

拉取服务镜像。

### `rm`

删除停止的服务容器。

### `run`

在一个服务上执行一个命令。

例如：

```
$ docker-compose run ubuntu ping docker.com
```

将会启动一个 ubuntu 服务，执行 `ping docker.com` 命令。

默认情况下，所有关联的服务将会自动被启动，除非这些服务已经在运行中。

该命令类似启动容器后运行指定的命令，相关卷、链接等等都将会按照期望创建。

两个不同点：

- 给定命令将会覆盖原有的自动运行命令；
- 不会自动创建端口，以避免冲突。

如果不希望自动启动关联的容器，可以使用 `--no-deps` 选项，例如

```
$ docker-compose run --no-deps web python manage.py shell
```

将不会启动 web 容器所关联的其它容器。

### `scale`

设置同一个服务运行的容器个数。

通过 `service=num` 的参数来设置数量。例如：

```
$ docker-compose scale web=2 worker=3
```

### `start`

启动一个已经存在的服务容器。

### `stop`

停止一个已经运行的容器，但不删除它。通过 `docker-compose start` 可以再次启动这些容器。

### `up`

构建，（重新）创建，启动，链接一个服务相关的容器。

链接的服务都将会启动，除非他们已经运行。

默认情况， `docker-compose up` 将会整合所有容器的输出，并且退出时，所有容器将会停止。

如果使用 `docker-compose up -d` ，将会在后台启动并运行所有的容器。

默认情况，如果该服务的容器已经存在， `docker-compose up` 将会停止并尝试重新创建他们（保持使用`volumes-from` 挂载的卷），以保证 `docker-compose.yml` 的修改生效。如果你不想容器被停止并重新创建，可以使用 `docker-compose up --no-recreate`。如果需要的话，这样将会启动已经停止的容器。

## Docker-compose文件

**docker-compose.yml文件中的version版本号需要和docker engine对应**

| **Compose file format** | **Docker Engine release** |
| :---------------------- | :------------------------ |
| Compose specification   | 19.03.0+                  |
| 3.8                     | 19.03.0+                  |
| 3.7                     | 18.06.0+                  |
| 3.6                     | 18.02.0+                  |
| 3.5                     | 17.12.0+                  |
| 3.4                     | 17.09.0+                  |
| 3.3                     | 17.06.0+                  |
| 3.2                     | 17.04.0+                  |
| 3.1                     | 1.13.1+                   |
| 3.0                     | 1.13.0+                   |

## Docker-compose模板文件

默认的模板文件是 `docker-compose.yml`，其中定义的每个服务都必须通过 `image` 指令指定镜像或 `build` 指令（需要 Dockerfile）来自动构建。

其它大部分指令都跟 `docker run` 中的类似。

如果使用 `build` 指令，在 `Dockerfile` 中设置的选项(例如：`CMD`, `EXPOSE`, `VOLUME`, `ENV` 等) 将会自动被获取，无需在 `docker-compose.yml` 中再次设置。

### `image`

指定为镜像名称或镜像 ID。如果镜像在本地不存在，`Compose` 将会尝试拉去这个镜像。

例如：

```sh
image: ubuntu
image: orchardup/postgresql
image: a4bc65fd
```

### `build`

指定 `Dockerfile` 所在文件夹的路径。 `Compose` 将会利用它自动构建这个镜像，然后使用这个镜像。

```
build: /path/to/build/dir
```

### `command`

覆盖容器启动后默认执行的命令。

```sh
command: bundle exec thin -p 3000
```

### `links`

链接到其它服务中的容器。使用服务名称（同时作为别名）或服务名称：服务别名 `（SERVICE:ALIAS）` 格式都可以。

```sh
links:
 - db
 - db:database
 - redis
```

使用的别名将会自动在服务容器中的 `/etc/hosts` 里创建。例如：

```sh
172.17.2.186  db
172.17.2.186  database
172.17.2.187  redis
```

相应的环境变量也将被创建。

### `external_links`

链接到 docker-compose.yml 外部的容器，甚至 并非 `Compose` 管理的容器。参数格式跟 `links` 类似。

```
external_links:
 - redis_1
 - project_db_1:mysql
 - project_db_1:postgresql
```

### `ports`

暴露端口信息。

使用宿主：容器 `（HOST:CONTAINER）`格式或者仅仅指定容器的端口（宿主将会随机选择端口）都可以。

```
ports:
 - "3000"
 - "8000:8000"
 - "49100:22"
 - "127.0.0.1:8001:8001"
```

*注：当使用 `HOST:CONTAINER` 格式来映射端口时，如果你使用的容器端口小于 60 你可能会得到错误得结果，因为 `YAML` 将会解析 `xx:yy` 这种数字格式为 60 进制。所以建议采用字符串格式。*

### `expose`

暴露端口，但不映射到宿主机，只被连接的服务访问。

仅可以指定内部端口为参数

```sh
expose:
 - "3000"
 - "8000"
```

### `volumes`

卷挂载路径设置。可以设置宿主机路径 （`HOST:CONTAINER`） 或加上访问模式 （`HOST:CONTAINER:ro`）。

```sh
volumes:
 - /var/lib/mysql
 - cache/:/tmp/cache
 - ~/configs:/etc/configs/:ro
```

### `volumes_from`

从另一个服务或容器挂载它的所有卷。

```sh
volumes_from:
 - service_name
 - container_name
```

### `environment`

设置环境变量。你可以使用数组或字典两种格式。

只给定名称的变量会自动获取它在 Compose 主机上的值，可以用来防止泄露不必要的数据。

```
environment:
  RACK_ENV: development
  SESSION_SECRET:

environment:
  - RACK_ENV=development
  - SESSION_SECRET
```

### `env_file`

从文件中获取环境变量，可以为单独的文件路径或列表。

如果通过 `docker-compose -f FILE` 指定了模板文件，则 `env_file` 中路径会基于模板文件路径。

如果有变量名称与 `environment` 指令冲突，则以后者为准。

```sh
env_file: .env

env_file:
  - ./common.env
  - ./apps/web.env
  - /opt/secrets.env
```

环境变量文件中每一行必须符合格式，支持 `#` 开头的注释行。

```sh
# common.env: Set Rails/Rack environment
RACK_ENV=development
```

### `extends`

基于已有的服务进行扩展。例如我们已经有了一个 webapp 服务，模板文件为 `common.yml`。

```sh
# common.yml
webapp:
  build: ./webapp
  environment:
    - DEBUG=false
    - SEND_EMAILS=false
```

编写一个新的 `development.yml` 文件，使用 `common.yml` 中的 webapp 服务进行扩展。

```sh
# development.yml
web:
  extends:
    file: common.yml
    service: webapp
  ports:
    - "8000:8000"
  links:
    - db
  environment:
    - DEBUG=true
db:
  image: postgres
```

后者会自动继承 common.yml 中的 webapp 服务及相关环节变量。

### `net`

设置网络模式。使用和 `docker client` 的 `--net` 参数一样的值。

```sh
net: "bridge"
net: "none"
net: "container:[name or id]"
net: "host"
```

### `pid`

跟主机系统共享进程命名空间。打开该选项的容器可以相互通过进程 ID 来访问和操作。

```sh
pid: "host"
```

### `dns`

配置 DNS 服务器。可以是一个值，也可以是一个列表。

```sh
dns: 8.8.8.8
dns:
  - 8.8.8.8
  - 9.9.9.9
```

### `cap_add, cap_drop`

添加或放弃容器的 Linux 能力（Capabiliity）。

```sh
cap_add:
  - ALL

cap_drop:
  - NET_ADMIN
  - SYS_ADMIN
```

### `dns_search`

配置 DNS 搜索域。可以是一个值，也可以是一个列表。

```sh
dns_search: example.com
dns_search:
  - domain1.example.com
  - domain2.example.com
```

### `working_dir, entrypoint, user, hostname, domainname, mem_limit, privileged, restart, stdin_open, tty, cpu_shares`

这些都是和 `docker run` 支持的选项类似。

```
cpu_shares: 73

working_dir: /code
entrypoint: /code/entrypoint.sh
user: postgresql

hostname: foo
domainname: foo.com

mem_limit: 1000000000
privileged: true

restart: always

stdin_open: true
tty: true
```

