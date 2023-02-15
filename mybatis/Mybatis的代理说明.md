# Mybatis的代理说明

## 分析：

```java
@Override
public Student selectById(Integer id) {
    SqlSession sqlSession = MyBatisUtils.getSqlSession();
    String sqlId = "indi.yuluo.dao.StudentDao.selectById";
    Student student = sqlSession.selectOne(sqlId, id);
    sqlSession.close();
    return student;
}
// 在dao层的实现类中，主要执行的是
String sqlId = "indi.yuluo.dao.StudentDao.selectById";
Student student = sqlSession.selectOne(sqlId, id);
这两部分代码
    
在单元测试中调用dao里面的方法
Student student = dao.selectById(1002);

/**
* 用于测试：在实现类中执行Dao接口中的方法
*/
@Test
public void testSelectOne() {
    StudentDao dao = new StudentDaoImpl();
    // 调用dao层方法
    Student student = dao.selectById(1002);
    System.out.println("通过执行实现类中的dao的方法获得的学生对象" + student);
}
```

- 因此执行sql语句的重要的信息是：

- ```java
  String sqlId = "indi.yuluo.dao.StudentDao.selectById";
  Student student = sqlSession.selectOne(sqlId, id);
  ```

- **能不能通过**`Student student = dao.selectById(1002);`**得到执行sql语句的信息？**

  - 1. dao：通过反射能够得到 ***全限定名称***

       `dao是StudentDao类型的，全限定名称indi.yuluo.dao.StudentDao`

       `selectById(): dao中的方法名， 方法名称就是mapper文件中标签的id名`

       **通过这两部分能够得到sqiId的信息：`inid.yuluo.dao.StudentDao.selectById`**

    2. 确定要执行的sqlSession的那个方法：

       `看dao中方法的返回值，如果返回的是一个对象就是selectOne(), 如果是list集合，就是selectList()方法`

       `根据mapper文件中的标签，如果标签是<insert>,调用的是sqlSession().insert()方法`

## 通过分析发现

***使用dao方法可以获得执行sql语句的必要信息***，而mybatis也发现了这个，于是mybatis简化了对dao对象的实现类的写法

## mybatis对dao对象的实现：

由mybatis框架在程序执行期间，根据你的dao接口，创建了一个在内存中的接口的实现类对象，而mybatis把这个技术叫做dao技术（动态代理技术 dao的动态代理）

代理技术: 由mybatis创建的StudentDao接口的实现类proxy(StudentDaoImpl)，使用框架创建的StudentDaoImpl代替你自己手工实现的StudentDaoImpl类的功能，不用开发人员写dao接口的实现类

## 使用proxy技术的要求：

mapper文件中的namespace要是dao类的***全限定名称***

mapper文件中的sql语句的标签id要是dao方法中的方法名

## mybatis代理的实现方式：

使用sqlSession对象的getMapper(dao.class)方法

```java
    /**
     * 用于测试：mybatis的proxy dao
     */
    @Test
    public void testUtils() {

        SqlSession sqlSession = MyBatisUtils.getSqlSession();

        /*String sqlId = "indi.yuluo.dao.StudentDao.selectById";
        Student student = sqlSession.selectOne(sqlId, 1001);*/

        StudentDao mapper = sqlSession.getMapper(StudentDao.class);
        System.out.println("mapper====" + mapper.getClass().getName());
        Student student = mapper.selectById(1005);

        System.out.println("student = " + student);
        sqlSession.close();

    }
```

`mapper====com.sun.proxy.$Proxy5`

