# MVC三层架构模式

> javaBean分为两种
>
> - 一种为实体类Bean ： 专门存储业务数据的，例如Student和User等
> - 一种成为业务bean ：指Service和Dao对象，专门用于处理业务逻辑和数据访问 

## 一：控制器层（controller）

> servlet
>
> 1 接受用户的请求
>
> 2 响应给客户端内容 （<u>**业务层负责处理**</u>，<u>**之后返回数据**</u>）
>
> 3 重定向或者转发（<u>**视图跳转**</u>）

## 二：视图层（view）

> jsp
>
> 1 展示数据
>
> 2 提供可以供我们操作的请求

**早些年的架构模式只有VC，没有Model，直接通过JavaBean（pojo实体类）操作数据库中的数据**

**用户直接访问控制层，控制层就可以直接操作数据库**

```java
servlet -> CRUD -> DataBases;

弊端：程序十分臃肿，不利于维护， servlet的代码中：处理请求，响应，试图跳转，处理JDBC, 处理业务和逻辑代码。
    
架构：没有什么是一层架构解决不了的，如果有，再加一层！

```



## 三：模型层（Model）

指工程中的JavaBean，作用是处理数据

>  控制业务操作，保存数据，修改，查询，删除数据
>
> 一般有service和Dao层
>
> **service：**login，logout，处理业务和逻辑代码
>
> **Dao**：对实体类的封装，（JavaBean（pojo）），实现对数据库的操作

##  四：总结

Model

- ·业务处理：业务逻辑 （Service）
- 数据持久层：CRUD （Dao）

View

- 展示数据
- 提供链接发起的Servlet请求（a， form，img……）

Controller

- 接受用户的请求：（req：请求参数，Session信息……）
- 交给业务层处理相对应的代码
- 控制视图的跳转



