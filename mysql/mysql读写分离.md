# mysql读写分离

增加数据库个数，将读写操作分离开，主负责写，从负责读

主从数据库之间通过主从复制来进行数据同步

## 主从复制

**将主库（写）的数据同步到从库（读）中**

### 介绍

mysql的主从复制是一个异步的复制过程，底层是基于mysql的二进制日志功能，就是一台或者多台mysql数据库（slave， 即从库）从另一台mysql数据库（master，从库）进行日志的复制然后再解析日志并应用到自身，最终实现从库的数据和主库的数据保持一致，mysql主从复制是mysql数据库的自带功能，无需借助第三方工具

### 复制过程

master将改变记录到二进制文件（binary log）

slave将master的binary log拷贝到它的中继日志（relay log）

slave重做中继日志中的事件，将改变应用到自己的数据库中

## 配置

两台mysql数据库

##### 主库配置

1. 修改mysql数据库的配置文件  /etc/my.cnf

   ```xml
   [mysqld]
   log-bin=mysql-bin   # 启用二进制日志
   server-id=100   # 服务器唯一id
   ```

2. 重启mysql服务

3. 登录mysql数据库，执行以下sql

   ```sql
   GRANT REPLICATION SLAVE ON *.* TO 'yuluo'@'%'identified by '082916'; 
   ```

   上述sql的作用是创建一个用户yuluo，并且给yuluo用户授予REPLICATION SLAVE权限，用于建立复制时需要用到的用户权限，也就是slave必须被master授权具有该权限的用户，才能通过该用户复制二进制日志文件。

4. 登录mysql数据库，执行`show master status`，记录下File和Position的值，上面的sql是查看master的状态，**执行完此条sql之后不要做任何操作，会引起值变化**

##### 从库配置

1. 修改mysql数据库的配置文件  /etc/my.cnf

   ```sql
   [mysqld]
   server-id=101    # 服务器唯一id
   ```

2. 重启mysql数据库

3. 登录数据库，执行以下sql

   ```sql
   change master to master_host='主库ip', master_user='yuluo（将才创建的用于复制log的用户）' , master_password='082916（密码）', master_log_file='mysql-bin.000001', master_log_pos=439(日志文件信息，根据主库File和Position修改);
   
   start slave;
   ```

4. show slave status 查看slave状态，可以将文件粘贴到记事本查看

## 测试

**新建一个数据库，再从库位置刷新，从库应该也会多出一个数据库**