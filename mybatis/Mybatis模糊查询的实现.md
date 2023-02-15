# Mybatis模糊查询的实现

> 模糊查询有两种实现方式
>
> 1. 在java代码中给查询数据加上 % 
> 2. 在mapper文件sql语句的条件位置加上 % 

## 第一种方式

dao方法

```java
List<Student> selectLikeFirst(@Param("studentLikeName") String name);
```

mapper文件

```java
<!--执行like模糊查询的第一种方式-->
<select id="selectLikeFirst" resultType="indi.yuluo.domain.Student">
    select id, name, email, age from student where name like #{studentLikeName}
</select>
```

单元测试

```java
/**
 * 用于测试：like模糊查询的第一种方式
 */
@Test
public void testSelectLikeName() {
    SqlSession session = MyBatisUtils.getSqlSession();

    StudentDao mapper = session.getMapper(StudentDao.class);

    // 对like的值进行字符串拼接
    String name = "%u%";
    List<Student> u = mapper.selectLikeFirst(name);

    u.forEach(System.out::println);

    session.close();
}
```

## 第二种方式

在sql语句中组织like语句的内容

sql语句的格式: `where name like "%"空格#{name}"空格%"`

mapper文件

```java
<!--执行like模糊查询的第二种方式-->
<select id="selectLikeSecond" resultType="indi.yuluo.domain.Student">
    select id, name, email, age from student where name like "%" #{studentLikeName} "%"
</select>
```