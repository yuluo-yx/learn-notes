# mybatis框架中的参数传递

参数：通过java程序把数据传入到mapper文件中的sql语句中，主要是指dao接口方法中的形参

## 第一个参数 parameterType

- 第一种用法：全限定名称

  - 表示的是参数的类型，指定dao方法中的形参的数据类型，这个形参的数据类型是给mybatis在给sql语句赋值的时候使用的。

  - 在jdbc中有一个PreparStatment.setXXX(位置，值)  可以设定参数的值

  - ```java
    <select id="selectById" parameterType="java.lang.Integer" resultType="indi.yuluo.domain.Student">
        select id,name,email,age from student where id=#{studentId}
    </select>
    
    // 输出结果：
    
    ==>  Preparing: select id,name,email,age from student where id=?
    ==> Parameters: 1005(Integer)
    <==    Columns: id, name, email, age
    <==        Row: 1005, hongkongdell, hongkongdell@qq.com, 24
    <==      Total: 1
    ```

- 第二种用法：mybatis指定的别名：

  - | 别名       | 映射的类型 |
    | :--------- | :--------- |
    | _byte      | byte       |
    | _long      | long       |
    | _short     | short      |
    | _int       | int        |
    | _integer   | int        |
    | _double    | double     |
    | _float     | float      |
    | _boolean   | boolean    |
    | string     | String     |
    | byte       | Byte       |
    | long       | Long       |
    | short      | Short      |
    | int        | Integer    |
    | integer    | Integer    |
    | double     | Double     |
    | float      | Float      |
    | boolean    | Boolean    |
    | date       | Date       |
    | decimal    | BigDecimal |
    | bigdecimal | BigDecimal |
    | object     | Object     |
    | map        | Map        |
    | hashmap    | HashMap    |
    | list       | List       |
    | arraylist  | ArrayList  |
    | collection | Collection |
    | iterator   | Iterator   |

    

  - ```java
    <select id="selectById" parameterType="int" resultType="indi.yuluo.domain.Student">
        select id,name,email,age from student where id=#{studentId}
    </select>
    ```

  - 可以不写parametertype，mybatis会通过反射获取参数类型



## [掌握]参数，一个简单类型的参数

dao接口中的形参类型是一个简单类型的

简单类型：java基本数据类型和String

```java
<!--dao接口是一个简单类型的参数 mapper文件获取这个参数使用#{任意字符}-->
<select id="selectByEmail" resultType="indi.yuluo.domain.Student">
    select id, name, email, age from student where email=#{studentEmail}
</select>
        
    /**
     * 用于测试：简单类型的参数
     */
    @Test
    public void testSelectByEmail() {
        SqlSession session = MyBatisUtils.getSqlSession();
        StudentDao mapper = session.getMapper(StudentDao.class);
        Student student = mapper.selectByEmail("hongkongdell@qq.com");

        System.out.println(student);

        session.close();
    }        

```



## [掌握]多个简单类型的参数

dao方法的写法：

```java
	 /**
     * 有多个简单参数
     * 使用@param命名参数 注解是mybatis提供的
     * 位置：在形参定义的前面
     * 属性：value自定义的参数名称
     *
     * @param name
     * @param age
     * @return student
     */
    List<Student> selectStudentByAgeOrName(@Param("studentName") String name,
                                           @Param("studentAge") Integer age);
```

mapper文件的写法：

```xml
    <!--多个简单参数
        使用了@Param命名之后，例如@Param("studentName")
        #{和@Param名称相同}
    -->
    <select id="selectStudentByAgeOrName" resultType="indi.yuluo.domain.Student">
        select id, name, email, age from student where name=#{studentName} or age=#{studentAge}
    </select>
```

单元测试：

```java
    /**
     * 用于测试：使用注解获取简单类型的参数
     */
    @Test
    public void testSelectStudentByAgrOrName() {
        SqlSession session = MyBatisUtils.getSqlSession();
        StudentDao mapper = session.getMapper(StudentDao.class);
        List<Student> studentInfo = mapper.selectStudentByAgeOrName("yuluo", 21);

        studentInfo.forEach(System.out::println);

        session.close();
    }
```



## [掌握]dao接口方法使用一个对象作为参数

### 简单的语法

方法的形参是一个java对象，这个java对象表示多个参数，使用对象的属性值作为参数传递

```java
// java对象方法：
package indi.yuluo.domain;

import java.io.Serializable;
import java.util.Objects;

/**
 * @author: yuluo
 * @createTime: 2022/1/22 19:15
 * @File : Student.java
 * @Software : IntelliJ IDEA
 * @Description: javbean
 */

public class Student implements Serializable {

    private Integer id;
    private String name;
    private String email;
    private Integer age;

    public Student() {
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    @Override
    public String toString() {
        return "学生信息{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", age=" + age +
                '}';
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Student student = (Student) o;
        return Objects.equals(id, student.id) && Objects.equals(name, student.name) && Objects.equals(email, student.email) && Objects.equals(age, student.age);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id, name, email, age);
    }
}

    
```



dao方法：

```java
    /**
     * 一个java对象作为参数，对象有属性，每个属性有set和get方法
     * @param student
     * @return studentList
     */
    List<Student> selectByObject(Student student);
```

mapper文件

```java
    <!--一个java对象作为参数, 使用对象的属性作为参数值使用
        简单的语法：#{属性名}， mybatis调用此属性的getXXX()方法来获取属性值
    -->
    <select id="selectByObject" resultType="indi.yuluo.domain.Student">
        select id, name, email, age from student where #{name} or #{age}
    </select>
```

单元测试方法

```java
    /**
     * 用于测试：将一个java对象的属性值作为参数
     */
    @Test
    public void testSelectByObject() {
        Student student = new Student();
        student.setName("yuluo");
        student.setAge(21);
        SqlSession session = MyBatisUtils.getSqlSession();
        StudentDao mapper = session.getMapper(StudentDao.class);
        List<Student> studentInfo = mapper.selectByObject(student);

        studentInfo.forEach(System.out::println);

        session.close();
    }
```

***也可以使用其他类作为mybatis的参数，操作方法和Student类一样。***

### 复杂的语法

语法格式：`#{propety,javaType=java中数据类型名,jdbcType=数据类型名}`

javaType，hdbcType的类型MyBatis可以检测出来，一般不需要设置，常用格式`#{property}`

```java
    <select id="selectByObject" resultType="indi.yuluo.domain.Student">
        select id, name, email, age from student where 
        #{name,javaType=java.lang.String,jdbcType=VARCHAR} 
		or 
        #{agem,javaType=java.lang.Integer,jdbcType=INTEGER}
    </select>
```

## [了解]多个简单类型参数，使用位置获取参数值

```java
用位置获取参数，dao接口方法是多个简单类型的参数
语法：#{arg0}，#{arg1}
<select id="selectByObject" resultType="indi.yuluo.domain.Student">
   select id, name, email, age from student where name=#{arg0} or age=#{arg1} 
</select>
```

dao方法

```jaav
   /**
     * 使用位置获取参数值
     * @param name
     * @param age
     * @return studentlist
     */
    List<Student> selectByPosition(String name, Integer age);
    								 #{arg0}		#{arg1}
```

单元测试

```java
    /**
     * 用于测试：使用位置获取参数
     */
    @Test
    public void testSelectByPosition() {

        SqlSession session = MyBatisUtils.getSqlSession();
        StudentDao mapper = session.getMapper(StudentDao.class);
        List<Student> studentInfo = mapper.selectByPosition("huakai", 21);

        studentInfo.forEach(System.out::println);

        session.close();
    }
```

## [了解]使用map传参

dao方法

```java
List<Student> selectStudentByMap(Map<String, Object> map);
```



mapper文件

```java
select id, name, email, age from student where name=#{myname} or age=#{myage} 
```

test方法

```java
Map<String, Object> map = new HashMap<>();
map.put("myname", "yuluo");
map.put("myage", 21);
List<Student> studentInfo = mapper.selectByPosition(map);
```

## # 和 $ 的区别

### # : 占位符

- 语法：#{字符}

- mybatis处理 #{} 使用的jdbc对象是PreparedStatement对象

- ```java
  <select id="selectById" parameterType="int" resultType="indi.yuluo.domain.Student">
      select id,name,email,age from student where id=#{studentId}
  </select>
  
  // mybatis创建PreparedStatement对象，执行sql语句
  String sql = "select id, name, email, age from student where id=?";
  PreparedStatement pst = connection.preparedStatement(sql);
  // 传递参数
  pst.setInt(1, 1001);
  
  ResultSet rs = pst.executQuery();
  ```

- 特点：

  - 效率高 
  - 能避免sql注入，sql执行更安全 
  - #{}常作为列值来使用位于等好的右侧，#{}位置的值金和数据类型有关

### $：占位符

- 语法：${字符}

- ```java
  <select id="selectById" parameterType="int" resultType="indi.yuluo.domain.Student">
      select id,name,email,age from student where id=${studentId}
  </select>
      
  ${} 表示字符串连接，把sql语句的其他内容和\${}内容使用 “+” 连接的方式连在一起
  String sql = "select id, name, email, age from student where id= " + "1001";
  // mybatis创建Statement对象，执行sql语句
  Statement stmt = connection.createStatement(sql);
  ResultSet rs = stmt.executQuery();
  ```

- 特点

  - 效率低
  - ${} 使用的是字符串连接的方式，有sql注入的风险
  - ${} 数据是原样使用的，不会区分数据类型
  - ${} 常用做表名或者列名，在保证数据安全的前提下使用\${}
  
- ${} 使用的例子：

  - mapper文件

  ```java
      <!--使用${}接受参数-->
      <select id="selectQueryStudent" resultType="indi.yuluo.domain.Student">
          select id,name,email,age from student where name=${studentName}
      </select>
  ```

  - dao方法

  ```java
      /**
       * 使用${}接受参数
       * @param name 要查询的学生姓名
       * @return studentList
       */
      List<Student> selectQueryStudent(@Param("studentName") String name);
  ```

  - 单元测试

  ```java
      /**
       * 用于测试${}的使用
       */
      @Test
      public void testSelectQueryStudent() {
  
          SqlSession session = MyBatisUtils.getSqlSession();
          StudentDao mapper = session.getMapper(StudentDao.class);
          List<Student> studentInfo = mapper.selectQueryStudent("yuluo");
  
          studentInfo.forEach(System.out::println);
  
          session.close();
      }
  ```

  - 输出结果

  ```java
  Setting autocommit to false on JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@78aab498]
  ==>  Preparing: select id,name,email,age from student where name=yuluo
  ==> Parameters: 
  
  org.apache.ibatis.exceptions.PersistenceException: 
  ### Error querying database.  Cause: java.sql.SQLSyntaxErrorException: Unknown column 'yuluo' in 'where clause'
  ### The error may exist in indi/yuluo/dao/StudentDao.xml
  ### The error may involve defaultParameterMap
  ### The error occurred while setting parameters
  ### SQL: select id,name,email,age from student where name=yuluo
  ### Cause: java.sql.SQLSyntaxErrorException: Unknown column 'yuluo' in 'where clause'
  ```

  - 分析：

    - 用${} 接受参数时，使用了字符串拼接的方式，将sql语句组合成如下形式

    `select id, name,emial,age ftom student where name=yuluo`

    而不是像#{} 占位符那样成为

    `select id,name,emial,age from student where name='yuluo'`

    - 修改方法为在参数传递时加上引号 字符串拼接正确

    ```java
        /**
         * 用于测试${}的使用
         */
        @Test
        public void testSelectQueryStudent() {
    
            SqlSession session = MyBatisUtils.getSqlSession();
            StudentDao mapper = session.getMapper(StudentDao.class);
            List<Student> studentInfo = mapper.selectQueryStudent("'yuluo'");
    
            studentInfo.forEach(System.out::println);
    
            session.close();
        }
    ```

    - 结果

    ```java
    Created connection 2024453272.
    Setting autocommit to false on JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@78aab498]
    ==>  Preparing: select id,name,email,age from student where name='yuluo'
    ==> Parameters: 
    <==    Columns: id, name, email, age
    <==        Row: 1001, yuluo, yuluo829@aliyun.com, 21
    <==      Total: 1
    学生信息{id=1001, name='yuluo', email='yuluo829@aliyun.com', age=21}
    Resetting autocommit to true on JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@78aab498]
    Closing JDBC Connection [com.mysql.cj.jdbc.ConnectionImpl@78aab498]
    Returned connection 2024453272 to pool.
    ```

  - sql注入风险

    - 在传递参数时传递`"'yuluo' or id > 0"`查询的结果就不是预想的那样了 造成数据暴露

- 在合适的场景下使用 **${} **表示 ***列名*** 或者 ***表名***

  - mapper文件

    ```java
        <!--传递列名进行排序-->
        <select id="queryStudentOrderByColname" resultType="indi.yuluo.domain.Student">
            select * from student order by ${colName}
        </select>
    ```

  - dao方法

    ```java
        /**
         * 传递列名进行排序
         * @param colName
         * @return
         */
        List<Student> queryStudentOrderByColname(@Param("colName") String colName);
    ```

  - 单元测试

    ```java
        /**
         * 用于测试：使用 ${} 传递数据库的列名进行排序
         */
        @Test
        public void testQueryStudentOrderByColname() {
    
            SqlSession session = MyBatisUtils.getSqlSession();
            StudentDao mapper = session.getMapper(StudentDao.class);
            List<Student> studentInfo = mapper.queryStudentOrderByColname("name");
    
            studentInfo.forEach(System.out::println);
    
            session.close();
        }
    ```

    

