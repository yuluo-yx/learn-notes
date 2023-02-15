# Mongodb 复制集

主要意义是实现服务高可用。类似于服务集群的概念

- 数据分发
- 读写分离
- 异地容灾

多采用一主二从的集群结构，通过日志来实现数据的读写。

## 目的：搭建复制集

使用三个mongodb数据库服务搭建复制集

## 理论知识

- 安装最新的mongodb数据库
- 有一定的存储空间

**一般情况下，只要有机器就可以**

1. 创建数据目录，mongodb启动时将使用一个数据目录存放所有数据文件，我们将为3个复制集节点创建各自的数据目录

   `mkdir -p /data/db{1,2,3}`

2. 准备配置文件

   我们现在使用一台机器运行3个进程，因此需要为它们各自配置**在线上环境中，应该是一个节点一个机器**

   ```mark
   1. 端口使用28017， 28018，28019。
   2. 不同的数据目录，将使用/data/db1/，其他类似。
   3. 不同的日志文件，使用/data/db1/mongod.log，其他类似。
   ```

   配置文件:mongod.conf

   ```conf
   systemLog:
       destination: file
       logAppend: true
       path: /data/db1/mongod.log
   storage:
       dbPath: /data/db1/
       journal:
           enabled: true
   net:
       port: 28017
       bindIp: 127.0.0.1
   replication:
       replSetName: rs0
   processManagement:
     timeZoneInfo: /usr/share/zoneinfo
   
   ```

3. 配置复制集

   ```sh
   mongo --port 28017
   > rs.initiate()
   rs.add("HOSTNAME:28018")
   rs.add("HOSTNAME:28019")
   
   注意：此方式需要host可以被解析
   ```

4. 验证

   ```sh
   主节点写入
   db.test.insert({ a:1 })
   db.test.insert({ b:2 })
   
   从节点搜索
   rs.slaveok()
   db.test.find()
   ```

## 实际操作

- 文件准备

![image-20221105100601139](./\Mongodb 复制集.assets\image-20221105100601139.png)

- 创建data文件夹

  ![image-20221105100651913](./\Mongodb 复制集.assets\image-20221105100651913.png)

- 在data下创建三个文件夹，表示三个节点存储的日志文件和数据文件

  ![](./\Mongodb 复制集.assets\image-20221105100806277.png)

- 配置文件准备，拷贝mongod.conf文件并编辑

  ![image-20221105100951263](./\Mongodb 复制集.assets\image-20221105100951263.png)

  编辑文件 

  - 过滤掉配置文件中的注释 `cat../mongod.conf | grep -v '^#' | grep -v '^$' > mongod.conf`

    ![image-20221105101830351](./\Mongodb 复制集.assets\image-20221105101830351-16676147189651.png)

  - 编辑文件为

    ![image-20221105102453041](./Mongodb 复制集.assets\image-20221105102453041.png)

    **主要配置是：replcation这行配置**

    

- 复制配置文件到db1，db2，db3中

  ![image-20221105102234577](./\Mongodb 复制集.assets\image-20221105102234577.png)

  ![image-20221105102319035](./\Mongodb 复制集.assets\image-20221105102319035.png)

- 修改db2和3中的路径和端口

  **数据文件和日志文件和端口需要更改**

  db2：

  ![image-20221105102814012](./\Mongodb 复制集.assets\image-20221105102814012.png)

  db3：

  ![image-20221105102902319](./\Mongodb 复制集.assets\image-20221105102902319.png)

- 启动进程

  ![image-20221105103305423](./\Mongodb 复制集.assets\image-20221105103305423.png)

  > 出现此种情况时，前面加入sudo吗，以root用户启动
  >
  > `sudo ../mongodb-linux-x86_64-ubuntu2004-6.0.1/bin/mongod -f db1/mongod.conf `

  ![image-20221105103956679](./\Mongodb 复制集.assets\image-20221105103956679.png)

  > 出现启动失败时，是端口占用的原因，服务器上已经有一个27107的mongo数据库在跑，所以将主节点的服务器端口改为**27020**（db1中的配置文件）

  ![image-20221105104212369](./\Mongodb 复制集.assets\image-20221105104212369.png)

  > 再次启动，启动成功！

- 依次启动db2和db3即可

  ![image-20221105104323897](./\Mongodb 复制集.assets\image-20221105104323897.png)

  > 注意：一定要出现`successfully`在进行下一步，否则有可能是配置文件出错

- 查看进程 `sudo lsof -i -s tcp:listen`

  ![image-20221105104532655](./\Mongodb 复制集.assets\image-20221105104532655.png)

  服务启动成功！

- 使用mongosh来登录主数据库节点

  `sudo ../mongosh-1.5.4-linux-x64/bin/mongosh --port 27020`(这里不同于27017，端口冲突，所以改成了27020)

  ![image-20221105110504367](./\Mongodb 复制集.assets\image-20221105110504367.png)

  **登录成功！**

- 启动复制集  `rs.initiate()` 出现ok为1，则证明操作成功

  ![image-20221105110610273](./\Mongodb 复制集.assets\image-20221105110610273.png)

  `rs.status()` 查看状态

  - members：成员节点

    ![image-20221105110812731](./\Mongodb 复制集.assets\image-20221105110812731.png)

- 加入其他两个节点

  `rs.add('127.0.0.1:27018')`

  `rs.add('127.0.0.1:27019')`

  ![image-20221105111056668](./\Mongodb 复制集.assets\image-20221105111056668.png)

  **出现ok为1，则添加成功**

  再次查看状态`rs.status()`

  ![image-20221105111144482](./\Mongodb 复制集.assets\image-20221105111144482.png)

  **member就会出现变化，加入两个从节点**

- 插入一个数据

  ```sh
  db.user.insertOne({name: "tom"})
  ```

  ![image-20221105111325880](./\Mongodb 复制集.assets\image-20221105111325880.png)

  ```sh
  db.user.find()
  ```

  ![image-20221105111349996](./\Mongodb 复制集.assets\image-20221105111349996.png)

- 登录到另外一个节点去，

  这里可以再打开一个会话，点击复制会话即可

  ![image-20221105111554727](./\Mongodb 复制集.assets\image-20221105111554727.png)

  ![image-20221105111627849](./\Mongodb 复制集.assets\image-20221105111627849.png)

  在另一个页面中登录从节点

  `sudo ../mongosh-1.5.4-linux-x64/bin/mongosh --port 27018`\

  ![image-20221105111806144](./\Mongodb 复制集.assets\image-20221105111806144.png)

  **注意：这里左边的提示符是secondary**

- 读取数据

  ```sh
  输入db.use.find()的时候会出现
  
  MongoServerError: not primary and secondaryOk=false - consider using db.getMongo().setReadPref() or readPreference in the connection string
  
  默认不允许从从节点读取数据，需要做一个配置才可以读取
  
  执行  rs.secondaryOk()
  
  出现这个之后：
  DeprecationWarning: .setSecondaryOk() is deprecated. Use .setReadPref("primaryPreferred") instead
  Setting read preference from "primary" to "primaryPreferred"
  
  才允许从节点读取数据
  ```

  输入

  `db.user.find()`

  ![image-20221105112248714](./\Mongodb 复制集.assets\image-20221105112248714.png)

  从节点读取数据成功

- 完成！

  

  

  

  

  

  

  

  

  