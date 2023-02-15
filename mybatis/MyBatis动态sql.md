# MyBatis动态sql

> 动态sql：同一个dao的方法，根据不同的条件可以表示不同的sql语句，主要是由where部分变化，使用mybatis提供的标签，实现动态sql的功能，主要讲if， where，foreach，sql代码片段
>
> 使用动态sql的时候，dao方法的形参是java对象

## if标签

语法：

```java
<if test="boolean">
    sql代码
</if>
    
在mapper文件中
<select id="selectStudent" resultType="indi.yuluo.domain.Student">
	select id, name, email, age from student
    <if test="条件">
        sql语句
    </if>    
    if标签可以有多个    
</select> 

如果条件为真时，把if中的sql语句添加到主sql语句的后面，形成一个完成的sql语句        
```

dao方法

```java
/**
 * 动态sql之if语句
 * @param student
 * @return
 */
List<Student> selectIf(Student student);
```

mapper文件：

```xml
<select id="selectIf" resultType="indi.yuluo.domain.Student">
    select id, name, email, age from student
    /*为了语法正确，即在第一个if条件不成立时可以执行第二个if的条件要在where后面加上一个对结果不影响的sql语句*/
    where id = -1
    /*if 标签的使用 test的判断条件是对象的属性值
        通过传入的参数只进行判断 表示的是name属性为空或者为空字符串时
      */
    <if test="name != null and name != ''">
        or name = #{name}
    </if>

    <if test="age > 0">
       /*为了保证最后组合到一起的sql语句正确，最后需要加 or 或者 and
         使用大于和小于号时，大于>没问题，小于会被认为是一个标签的起始标签出现错误  需要使用实体符号替换
         */
        or age = #{age}
    </if>
</select>
```

单元测试

```java
    @Test
    public void testSelectIf() {
        SqlSession session = MyBatisUtils.getSqlSession();
        StudentDao mapper = session.getMapper(StudentDao.class);

        // 准备参数
        Student student = new Student();
        student.setAge(21);
//        student.setName("yuluo");

        List<Student> students = mapper.selectIf(student);
        session.close();

        students.forEach(System.out::println);

    }
```



| 符号 | 含义     | 实体符号 |
| ---- | -------- | -------- |
| <    | 小于     | \&lt;    |
| >    | 大于     | \&gt;    |
| <=   | 小于等于 | \&lt;=   |
| >=   | 大于等于 | \&gt;=   |

## where标签

使用if标签时，容易引起sql语句语法错误，使用where标签解决if语句产生的语法问题

使用时where，里面是一个或者多个if标签，当有一个if标签 判断条件为true，where标签会转为WHERE关键字附加到sql语句的后面，如果if没有一个条件为true，或略where标签和里面的if标签

语法：

```xml
<where>
	<if test="条件1">sql语句1</if>
	<if test="条件2">sql语句2</if>
    ……
</where>
```

mapper文件

```xml
<!--where标签-->
<select id="selectWhere" resultType="indi.yuluo.domain.Student">
    select * from student
    <where>
        <if test="name != null and name != ''">
            or name = #{name}
        </if>
        <if test="age > 0">
            or age &lt; #{age}
        </if>
    </where>
</select>
```

dao方法

```java
/**
 * 动态sql语句的where标签
 * @param student
 * @return student
 */
List<Student> selectWhere(Student student);
```

单元测试

```java
/**
 * 用于测试：动态sql语句的where标签
 */
@Test
public void testSelectWhere() {
    SqlSession session = MyBatisUtils.getSqlSession();
    StudentDao dao = session.getMapper(StudentDao.class);

    Student student = new Student();
    student.setName("huakai");
    student.setAge(24);

    List<Student> students = dao.selectWhere(student);
    session.close();

    students.forEach(System.out::println);
}
```

输出结果

```java
Opening JDBC Connection
Created connection 2005733474.
Setting autocommit to false on JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@778d1062]
==>  Preparing: select * from student WHERE name = ? or age < ?
==> Parameters: huakai(String), 24(Integer)
<==    Columns: id, name, email, age
<==        Row: 1001, yuluo, yuluo829@aliyun.com, 21
<==        Row: 1002, huakai, 2457608344@qq.com, 21
<==        Row: 1003, 张三, zhangsan@qq.com, 21
<==      Total: 3
Resetting autocommit to true on JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@778d1062]
Closing JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@778d1062]
Returned connection 2005733474 to pool.
学生信息{id=1001, name='yuluo', email='yuluo829@aliyun.com', age=21}
学生信息{id=1002, name='huakai', email='2457608344@qq.com', age=21}
学生信息{id=1003, name='张三', email='zhangsan@qq.com', age=21}
```

使用where标签组成的sql语句是

`select * from student WHERE name = ? or age < ?`

***自动加了where关键字***，紧跟在where之后的and或者or被删掉了

## foreach标签

<foreach>标签用于实现对于数组与集合的遍历。一般使用在in语句中，对其使用，需要注意

1. collection表示需要遍历的集合类型：list，array等
2. open，close，separator为对遍历内容的sql拼接

语法：

```java
语法：
<foreach collection="集合类型" open="开始的字符" close="结束的字符" item="集合中的成员" separator="集合成员之间的分隔符">
    #{item的值}
</foreach>
```

- collection 表示 循环的对象 数组或者list集合 如果dao接口方法的形参是数组collection="array" 如果是list ： collection="list".
- open 循环开始时的字符
- close 循环结束时的字符
- item 集合成员，自定义的变量
- separator 集合成员之间的分隔符
- #{item} 获取集合成员的值

### foreach遍历简单类型的集合

mapper文件

```xml
<!--foreach的第一种方式 简单类型
    当list集合不为空时进行这样的处理
-->
<select id="selectForeach" resultType="indi.yuluo.domain.Student">
    select * from student
    <if test="list != null and list.size() > 0">
        where id in
        <foreach collection="list" open="(" close=")" separator="," item="studentId">
            #{studentId}
        </foreach>
    </if>
</select>
```

dao文件

```java
/**
 * 使用foreach进行动态sql语句的拼接
 * 循环简单类型的List集合
 * @param idList
 * @return list
 */
List<Student> selectForeach(List<Integer> idList);
```

单元测试

```java
@Test
public void testForeach() {
    SqlSession session = MyBatisUtils.getSqlSession();
    StudentDao dao = session.getMapper(StudentDao.class);

    List<Integer> id = new ArrayList<>();
    id.add(1002);
    id.add(1003);
    id.add(1004);
    id = null;

    List<Student> students = dao.selectForeach(id);
    session.close();
    students.forEach(System.out::println);
}
```

### foreach遍历java对象类型的集合

mapper文件

```xml
<!--foreach第二种方式 遍历对象类型集合
    取出student的id属性进行动态sql语句的拼接-->
<select id="selectForeach2" resultType="indi.yuluo.domain.Student">
    select * from student
    <if test="list != null and list.size() > 0">
        where id in
        <foreach collection="list" open="(" close=")" separator="," item="students">
            #{students.id}
        </foreach>
    </if>
</select>
```

dao方法

```java
/**
 * foreach对对象类型的集合遍历
 * @param students
 * @return
 */
List<Student> selectForeach2(List<Student> students);
```

单元测试

```java
/**
 * 用于测试：foreach的第二种方式
 */
@Test
public void testSelectForeach2() {
    SqlSession session = MyBatisUtils.getSqlSession();
    StudentDao mapper = session.getMapper(StudentDao.class);

    List<Student> student = new ArrayList<>();
    Student student1 = new Student();
    Student student2 = new Student();
    student1.setId(1002);
    student2.setId(1005);
    student.add(student1);
    student.add(student2);

    List<Student> students = mapper.selectForeach2(student);

    session.close();
    students.forEach(stu-> System.out.println("stu = " + stu));
}
```

## 代码片段

> <sql/> 标签用于定义SQL片段，以便其他的sql标签复用，而其他标签使用该sql片段，需要使用<include/>子标签
>
> sql标签可以定义任意的sql标签，所以include子标签可以放在sql语句的任何地方

语法：

```java
在mapper文件中定义 sql代码片段
<sql id="唯一值">部分sql语句</sql>    

在其他地方引用
<include refid="唯一值"/>    
```

mapper文件

```xml
<!--定义sql代码片段-->
<sql id="select">
    select * from student
</sql>

<!--foreach第二种方式 遍历对象类型集合
    取出student的id属性进行动态sql语句的拼接-->
<select id="selectForeach2" resultType="indi.yuluo.domain.Student">
    <include refid="select"/>
    <if test="list != null and list.size() > 0">
        where id in
        <foreach collection="list" open="(" close=")" separator="," item="students">
            #{students.id}
        </foreach>
    </if>
</select
```

