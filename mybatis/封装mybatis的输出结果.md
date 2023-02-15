# 封装mybatis的输出结果

- 封装输出结果：MyBatis执行sql语句，得到ResultSet，转为java对象。

## resultType

resultType属性：***在执行select时使用，作为<select>标签的属性值出现的***

resultType：表示类型，mysql执行sql语句，得到java对象的类型，它的值有两种

- java类型的全限定名称( 根据Dao接口中的方法返回值类型确定)
- 使用别名

### resultType表示java自定义对象

#### 全限定名称

```java
Student selectById(Integer id);

<select id="selectById" parameterType="int" resultType="indi.yuluo.domain.Student">
    select id,name,email,age from student where id=#{studentId}
</select>

resultType: 使用java对象的全限定名称。表示的意思是mybatis执行sql语句之后，把ResultSet中的数据转为Student类型的对象。mybatis会做以下的操作：
    1，调用indi.yuluo.domain.Student的无参构造方法，创建对象：
    Student student = new Student();
	2, 同名的列赋值给同名的属性
    student.setId(rs.getInt("id"));
	student.setString(rs.get("name"));
	3,得到java对象，如果dao接口返回值是List集合，mybatis把student对象放入到List集合中
        
所以执行 Student studen = dao.selectById(1001);得到的是数据库中id=1001的这行数据，这行数据的列值，赋给了student对象的属性，得到studen对象，就相当于得到了id=1001这行数据
        
所以在声明实体类的时候，属性名和列名尽量保持一致！
```

#### 使用别名

- 自定义别名

  - mybatis提供的对java类型定义简短，好记的名称

  - 自定义别名的步骤

    - 1. 在mybatis主配置文件中，使用typeAliases标签声明别名
      2. 在mapper文件中，resultType = ”别名“

      ```java
          <!--类型别名-->
          <typeAliases>
              
              <!--第一种语法格式
                  type：java类型的全限定名称（自定义类型）
                  alias：自定义别名
              -->
              <typeAlias type="indi.yuluo.domain.Student" alias="stu"/>
              <typeAlias type="indi.yuluo.******" alias="****"/>
              
          </typeAliases>
      ```

    - 优点：别名可以自定义

    - 缺点：每个类型都需要自己定义

    - **使用<package name="">来定义别名**

    - ```java
          <!--类型别名-->
              
              <!--别名就是每个类的类名的小写-->
              <package name="indi.yuluo.domain.Student"/>
              <package name="indi.yuluo.****">
              
          </typeAliases>
      ```

- 建议使用全限定名称



### resultType表示简单类型

mapper文件

```java
    <!--执行sql语句，得到的是一个一行一列的值-->
    <select id="countStudent" resultType="java.lang.Integer">
        select count(*) from student
    </select>
```

dan方法

```java
    /**
     * 得到student这张表的记录数
     * @return
     */
    Integer countStudent();
```

### resultType表示map数据结构

mapper文件

```java
    <!--执行sql得到一个map数据结构
        {列名 = 列值，……}
    -->
    <select id="selectByMap" parameterType="int" resultType="java.util.HashMap">
        select * from student where id=#{studentId}
    </select>
```

dao方法

```java
    // 返回结果是一个Map
    Map<Object, Object> selectByMap(@Param("studentId") Integer id);
```

单元测试

```java
    /**
     * 用于测试：resultMap 返回的是一个map的数据结构
     */
    @Test
    public void testSelectByMap() {
        SqlSession sqlSession = MyBatisUtils.getSqlSession();
        StudentDao mapper = sqlSession.getMapper(StudentDao.class);

        Map<Object, Object> objectObjectMap = mapper.selectByMap(1002);

        System.out.println(objectObjectMap);
        sqlSession.close();
    }
```

- 只能返回一条数据多了会报异常

## resultMap

**因为数据库的列名定义规范和java的属性值的命名规范不同，所以会经常使用到resultMap这个属性**

```java
<!--
	定义resultMap
    id：给resultMap的映射关系七个名称，唯一值
    type：java类型的全限定名称
-->
<resultMap id="customMap" type="indi.yuluo.vo.CustomerOnject">
    <!--定义列名和属性名的对应-->
    <!--主键类型使用id标签-->
    <id column="id" property="cid"/>
    
    <!--非主键类型使用result标签-->
    <result column="name" property="cname"/>
    
    <!--列名和属性名相同不定义-->
    <result column="emial" property="email"/>
    
</result>
    
<!--使用resultMap属性的id值来指定映射关系-->    
<select id="selectByMap" resultMap="customMap">
    select * from student where id=#{studentId}
</select>
```

- **定义了之后可以反复使用**

***resultType和ruseltMap二选一，不能同时使用***

