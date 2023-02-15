# Mybatis扩展插件

## PageHelper

mybatis通用分页插件：https://github.com/pagehelper/Mybatis-PageHelper

- 引入依赖

  

  ```xml
  <!--数据库分页插件-->
  <!-- https://mvnrepository.com/artifact/com.github.pagehelper/pagehelper -->
  <dependency>
      <groupId>com.github.pagehelper</groupId>
      <artifactId>pagehelper</artifactId>
      <version>5.2.0</version>
  </dependency>
  ```

- 加入插件配置

  在environments之前加入

  ```xml
  <!--加入插件配置-->
  <plugins>
      <plugin interceptor="com.github.pagehelper.PageInterceptorn"/>
  </plugins>
  ```

  

- 在select语句之前 调用PageHelper.startPage(页数，每页要显示的数据数)

  `PageHelper。startPage(int pageNum, int pageSize);`

  

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
  
          student = null;
  
          PageHelper.startPage(1, 2);
          List<Student> students = mapper.selectForeach2(student);
  
          session.close();
          students.forEach(stu-> System.out.println("stu = " + stu));
      }
  ```

  

  

  



