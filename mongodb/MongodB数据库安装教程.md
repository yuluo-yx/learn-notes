# MongodB数据库安装教程



******



## 1 官网下载msi安装文件

官网地址：[传送门]([MongoDB Community Download | MongoDB](https://www.mongodb.com/try/download/community))

阿里云盘：「mongodb-windows-x86_64-5.0.5-signed.msi」https://www.aliyundrive.com/s/kDPg5WCCz5o 点击链接保存，或者复制本段内容，打开「阿里云盘」APP ，无需下载极速在线查看，视频原画倍速播放。

这里推荐下载社区版，免费。

## 2 开始安装

双击msi文件，

安装开始运行了之后，在

![image-20220101200113132](C:/Users/14815/AppData/Roaming/Typora/typora-user-images/image-20220101200113132.png)

这个页面可以选择custmoer自定义安装路劲



![image-20220101200314026](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220101200314026.png)

这里安装 **"Install MongoDB Compass"** 不勾选，否则可能要很长时间都一直在执行安装，MongoDB Compass是一个图形界面管理工具，这里不安装也是没有问题的，可以自己去下载一个图形界面管理工具，比如[Robo3T](https://robomongo.org/)

![image-20220101200445134](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220101200445134.png)

之后一路next + install，稍微等待会即可安装完成

## 3 配置

### 1 建立文件夹新建log文件

在安装好的mongodb文件夹中data中新建db和log文件夹，并且在log文件夹中新建文件mongo.log

![image-20220101201717826](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220101201717826.png)

![image-20220101201742219](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220101201742219.png)

### 2 添加环境变量到系统的环境变量中

![image-20220101201839991](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220101201839991.png)

### 3 运行命令

```sql
 mongod --dbpath F:\MongoDB\data\db
```

### 4 验证数据库是否启动

> 在浏览器url输入 localhost:27017验证

![image-20220101202134532](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220101202134532.png)

如果出现如上图，说明数据库启动成功

### 5 添加服务

> ps: 作者这里不采用开机启动的方法，有需要自行百度

运行如下命令：**注意：以管理员身份启动**

````sql
mongod --dbpath "F:\MongoDB\data\db" --logpath "EF\MongoDB\data\log\mongo.log" -install -serviceName "MongoDB"
````

![image-20220101202505050](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220101202505050.png)

可以win + R 进入services.msc 查看服务是否添加成功。如图

![image-20220101202703791](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220101202703791.png)

### 6 错误处理

有如下错误时，不要着急

![image-20220101202857615](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220101202857615.png)

先执行

```sql
sc delete mongodb
```

在执行一次服务配置的命令，即可配置成功

````sql
mongod --dbpath "F:\MongoDB\data\db" --logpath "EF\MongoDB\data\log\mongo.log" -install -serviceName "MongoDB"
````

再次以管理员身份运行net start mongodb，启动成功

![image-20220101210053446](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220101210053446.png)

## 4 安装完成

输入mongo进入数据库

![image-20220101210534644](C:/Users/14815/AppData/Roaming/Typora/typora-user-images/image-20220101210534644.png)

