# Mybatis的一些重要对象

- ***Resources:*** mybatis框架中的对象：读取主配置文件的信息  （mybatis.xml）

  

  ```java
  InpurStream inputStream = Resources.getResourcesAsStream("mybatis.xml");
  ```

  

- ***SqlSessionFactoryBuilder***: 负责创建SqlSessionFactory对象

  

  ```java;
  SqlSessionFactoey factory = new SqlSessionFactoryBuilder().build(inputStream)
  ```

  

- ***SqlSessionFactory***: 是重量级对象：创建此对象需要更多的资源和时间 （是一个接口）

  - connection需要和mysql服务建立连接，需要验证，通过网络发送信息

    **在项目中只存在一个连接对象即可**

  - 创建SqlSession的工厂，创建sqlSession对象

  - 实现类:`public void DefaultSqlSessionFactory implements SqlSessionFactory{ }`

  - 其中的方法：

    - openSession(): 获取一个默认的SqlSession对象，默认是需要手动提交事务的
    - openSession(boolean): 有两个参数
      - true：开启自动提交
      - false：关闭自动提交，等同于没有参数的openSession

- SqlSession：通过SqlSessionFactory获取，本身是接口

  - 实现类:`public void DefaultSqlSession implements SqlSession{ }`
  - 提供了大量的执行sql语句的方法：
    - selectOne(): 执行sql语句，最多返回一条结果，多余1条报错
    - selectList()：执行sql语句，返回多条结果
    - selectMap(): 执行sql语句，返回一个map集合
    - insert(): 插入操作
    - update()：更新操作
    - delete(): 删除操作
    - commit()：提交事务
    - rollback(): 回滚事务
  - 注意：SqlSession不是线程安全的，使用的步骤：
    - 1. 在方法的内部执行Sql语句之前，先获取Sqlsession对象
      2. 条用SqlSession的对象，执行sql语句
      3. 关闭SqlSession对象



