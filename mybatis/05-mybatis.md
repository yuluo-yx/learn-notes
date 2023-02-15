mybatis

��һ��
  1. ����ܹ�
     ����㣺 ���û��򽻵��ģ� �����û������������ ��ʾ��������ġ���jsp ��html ��servlet��
     ҵ���߼��㣺 �����˽���㴫�ݵ����ݣ������߼����������ݿ⣬��ȡ����
     ���ݷ��ʲ㣺 ���Ƿ������ݿ⣬ ִ�ж����ݵĲ�ѯ���޸ģ�ɾ���ȵȵġ�


     �����Ӧ�İ�
       ����㣺 controller�� ��servlet��
       ҵ���߼��㣺 service ����XXXService�ࣩ
       ���ݷ��ʲ㣺 dao����XXXDao�ࣩ


     ��������Ľ���
       �û�ʹ�ý����--> ҵ���߼���--->���ݷ��ʲ㣨�־ò㣩-->���ݿ⣨mysql��

     
     �����Ӧ�Ĵ������
       �����---servlet---springmvc����ܣ�
       ҵ���߼���---service��--spring����ܣ�
       ���ݷ��ʲ�---dao��--mybatis����ܣ�

  
 2.���
   �����һ����̨�� һ��ģ��

   ģ�棺
     1. �涨�˺�һЩ������ݡ�
     2. �����Լ��Ķ���

   �����һ��ģ��
    1.����ж������һЩ���ܡ���Щ�����ǿ��õġ�
    2.���Լ�����Ŀ���Լ��Ĺ��ܣ� ��Щ���ܿ������ÿ����д�õĹ��ܡ�


   �����һ�����������Ʒ���������������һЩ�������ܣ� ��Ҫ������Ĺ��ܾ��������ġ�
   ���������ǿ��ظ�ʹ�õģ��������ġ�

   ����ص㣺
    1. ���һ�㲻��ȫ�ܵģ� ��������������
    2. ��������ĳһ��������Ч�� �س���ĳһ�����棬����mybatis�����ݿ����ǿ�������������������ġ�
    3. �����һ������



 mybatis���
   һ����ܣ����ڽ���ibatis,  ������github��
   mybatis�� MyBatis SQL Mapper Framework for Java ��sqlӳ���ܣ�
   1��sql mapper :sqlӳ��
         ���԰����ݿ���е�һ������  ӳ��Ϊ һ��java����
	 һ�����ݿ��Կ�����һ��java���󡣲���������󣬾��൱�ڲ������е�����

   2�� Data Access Objects��DAOs�� : ���ݷ��� �� �����ݿ�ִ����ɾ�Ĳ顣


 mybatis�ṩ����Щ���ܣ�
  1. �ṩ�˴���Connection ,Statement, ResultSet������ �����ÿ�����Ա������Щ������
  2. �ṩ��ִ��sql���������� ������ִ��sql
  3. �ṩ��ѭ��sql�� ��sql�Ľ��תΪjava���� List���ϵ�����
	  while (rs.next()) {
		Student stu = new Student();
		stu.setId(rs.getInt("id"));
		stu.setName(rs.getString("name"));
		stu.setAge(rs.getInt("age"));
		//�����ݿ�ȡ������תΪ Student ���󣬷�װ�� List ����
		stuList.add(stu);
	  }
  4.�ṩ�˹ر���Դ��������������ر�Connection, Statement, ResultSet


 ������Ա�����ǣ� �ṩsql���
 ����ǣ� ������Ա�ṩsql���--mybatis����sql---������Ա�õ�List���ϻ�java���󣨱��е����ݣ�

 �ܽ᣺
  mybatis��һ��sqlӳ���ܣ��ṩ�����ݿ�Ĳ�����������ǿ��JDBC,
  ʹ��mybatis�ÿ�����Ա���о���дsql�Ϳ����ˣ����ع���Connection,Statement,ResultSet
  �Ĵ��������٣�sql��ִ�С� 



 �ڶ��£�
  1 ��Ҫ��Ľ���
   1�� Resources�� mybatis�е�һ���࣬ �����ȡ�������ļ�
	  InputStream in = Resources.getResourceAsStream("mybatis.xml");

	2)SqlSessionFactoryBuilder : ����SqlSessionFactory���� 
	     SqlSessionFactoryBuilder builder  = new SqlSessionFactoryBuilder();
        //����SqlSessionFactory����
        SqlSessionFactory factory = builder.build(in);

  3��SqlSessionFactory �� ���������� ���򴴽�һ�������ʱ�Ƚϳ���ʹ����Դ�Ƚ϶ࡣ
        ��������Ŀ�У���һ���͹����ˡ�

     SqlSessionFactory:�ӿ�  �� �ӿ�ʵ���ࣺ DefaultSqlSessionFactory
	  SqlSessionFactory���ã� ��ȡSqlSession����SqlSession sqlSession = factory.openSession();

	  openSession()����˵����
	   1. openSession() ���޲����ģ� ��ȡ�Ƿ��Զ��ύ�����SqlSession����
		2. openSession(boolean): openSession(true)  ��ȡ�Զ��ύ�����SqlSession. 
		                         openSession(false)  ���Զ��ύ�����SqlSession����

 
 4)SqlSession: 
   SqlSession�ӿ� �������˲������ݵķ��� ���� selectOne() ,selectList() ,insert(),update(), delete(), commit(), rollback()
   SqlSession�ӿڵ�ʵ����DefaultSqlSession��

	ʹ��Ҫ�� SqlSession�������̰߳�ȫ�ģ���Ҫ�ڷ����ڲ�ʹ�ã� ��ִ��sql���֮ǰ��ʹ��openSession()��ȡSqlSession����
	��ִ����sql������Ҫ�ر�����ִ��SqlSession.close(). �����ܱ�֤����ʹ�����̰߳�ȫ�ġ�


�����£�
  1. ��̬������ ʹ��SqlSession.getMapper(dao�ӿ�.class) ��ȡ���dao�ӿڵĶ���
  2. ��������� ��java�����а����ݴ��뵽mapper�ļ���sql����С�
     1��parameterType �� д��mapper�ļ��е� һ�����ԡ� ��ʾdao�ӿ��з����Ĳ������������͡�
	     ����StudentDao�ӿ�
		  public Student  selectStudentById(Integer id) 

	  2) һ�������͵Ĳ�����
     �����ͣ� mybatis��java�Ļ����������ͺ�String���м����͡�
     ��mapper�ļ���ȡ�����͵�һ��������ֵ��ʹ�� #{�����ַ�}

	  �ӿڣ�public Student  selectStudentById(Integer id) 
	  mapper:select id,name, email,age from student where id=#{studentId}

    3) ���������ʹ��@Param��������
	  �ӿ� public List<Student> selectMulitParam(@Param("myname") String name, @Param("myage") Integer age)
	  ʹ��  @Param("������")  String name 
     mapper�ļ���
	     <select>
             select * from student where name=#{myname} or age=#{myage}
		  </select>

    4) ���������ʹ��java����
	   �﷨ #{������}

   vo: value object , ��һЩ�洢���ݵ��ࡣ����˵ �ύ��������� name ,age 
	     �������name ,age ����һ��service �ࡣ

   vo: view object , ��servlet�����ݷ��ظ������ʹ�õ��࣬��ʾ��ʾ������ࡣ

   pojo: ��ͨ����set�� get������java�ࡣ ��ͨ��java����

		  Servlet --- StudentService( addStudent( MyParam  param)  )

   entity��domain��: ʵ���࣬ �����ݿ��еı���Ӧ���࣬ 
     

    5) # ��  $

      select id,name, email,age from student where id=#{studentId}
	  # �Ľ���� select id,name, email,age from student where id=? 


	   select id,name, email,age from student where id=${studentId}
	  $ �Ľ����select id,name, email,age from student where id=1001

	  String sql="select id,name, email,age from student where id=" + "1001";
	  ʹ�õ�Statement����ִ��sql�� Ч�ʱ�PreparedStatement�͡�


	  $:�����滻�������������� ����ȷ�������ǰ�ȫ�ġ�����ʹ��$


	  # �� $����
	  1. #ʹ�� ����sql�������վλ�ģ� ʹ��PreparedStatementִ��sql��Ч�ʸ�
	  2. #�ܹ�����sqlע�룬����ȫ��
	  3. $��ʹ��ռλ�������ַ������ӷ�ʽ��ʹ��Statement����ִ��sql��Ч�ʵ�
	  4. $��sqlע��ķ��գ�ȱ����ȫ�ԡ�
	  5. $:�����滻������������


 3. mybatis��������
    mybatisִ����sql��䣬�õ�java����

	 1��resultType������ͣ� ָsql���ִ����Ϻ� ����תΪ��java���� java����������ġ�
       resultType������͵���ֵ 1. ���͵�ȫ�޶�����   2. ���͵ı����� ���� java.lang.Integer������int
   

	    ������ʽ��
		   1. mybatisִ��sql��䣬 Ȼ��mybatis��������޲������췽������������
			2. mybatis��ResultSetָ����ֵ����ͬ�������ԡ�


			<select id="selectMultiPosition" resultType="com.bjpowernode.domain.Student">
          select id,name, email,age from student
        </select>

		  �Եȵ�jdbc
		  ResultSet rs = executeQuery(" select id,name, email,age from student" )
		  while(rs.next()){
               Student  student = new Student();
					student.setId(rs.getInt("id"));
					student.setName(rs.getString("name"))
		  }

   2) �����Զ������͵ı���
	   1����mybatis�������ļ��ж��壬ʹ<typeAlias>�������
		2��������resultType��ʹ���Զ������


   3��resultMap:���ӳ�䣬 ָ��������java��������Զ�Ӧ��ϵ��
	    1�����Զ�����ֵ��ֵ���ĸ�����
		 2���������������������һ��ʱ��һ��ʹ��resultMap


		 resultMap��resultType��Ҫһ���ã���ѡһ


�����¶�̬sql
 ��̬sql: sql�������Ǳ仯�ģ����Ը���������ȡ����ͬ��sql��䡣
           ��Ҫ��where���ַ����仯��

 ��̬sql��ʵ�֣�ʹ�õ���mybatis�ṩ�ı�ǩ�� <if> ,<where>,<foreach>

 1)<if>���ж������ģ�
   �﷨<if test="�ж�java���������ֵ">
            ����sql���
	    </if>

 2)<where> �������� ���<if>�ģ� �����if��һ�������ģ� <where>���Զ�����һ��where�ؼ��֣�
            ��ȥ�� if�ж���� and ��or�ȡ�

 3��<foreach> ѭ��java�е����飬list���ϵġ� ��Ҫ����sql��in����С�
    ѧ��id�� 1001,1002,1003������ѧ��

	 select * from student where id in (1001,1002,1003)

	 public List<Student> selectFor(List<Integer> idlist)

	 List<Integer> list = new ...
	 list.add(1001);
	 list.add(1002);
	 list.add(1003);

	 dao.selectFor(list)


	 <foreach collection="" item="" open="" close="" separator="">
             #{xxx}
    </foreach>

    collection:��ʾ�ӿ��еķ������������ͣ� ���������ʹ��array , �����list����ʹ��list
	 item:�Զ���ģ���ʾ����ͼ��ϳ�Ա�ı���
	 open:ѭ����ʼ�ǵ��ַ�
	 close:ѭ������ʱ���ַ�
    separator:���ϳ�Ա֮��ķָ���


  4��sql����Ƭ�Σ� ���Ǹ���һЩ�﷨
    ����
	 1.�ȶ��� <sql id="�Զ�������Ψһ">  sql��䣬 �������ֶε� </sql>
    2.��ʹ�ã� <include refid="id��ֵ" />


�����£�
  1. ���ݿ�����������ļ��� �����ݿ�������Ϣ�ŵ�һ���������ļ��С� ��mybatis�������ļ��ֿ���
     Ŀ���Ǳ����޸ģ����棬����������ݿ����Ϣ��

	  1����resourcesĿ¼�ж���һ�����������ļ��� xxxx.properties ,���� jdbc.properties
        �����������ļ��У� �������ݣ���ʽ�� key=value 
		  key�� һ��ʹ�� . ���༶Ŀ¼�ġ�
		  ���� jdbc.mysql.driver    , jdbc.driver, mydriver
		  jdbc.driver=com.mysql.jdbc.Driver
		  jdbc.url=jdbc:mysql//.....
		  jdbc.username=root
		  jdbc.password=123456
	     
	  2����mybatis���������ļ���ʹ��<property> ָ���ļ���λ��
	     ����Ҫʹ��ֵ�ĵط��� ${key}

 2.mapper�ļ���ʹ��packageָ��·��
     <mappers>
    
        <!--�ڶ��ַ�ʽ�� ʹ�ð���
            name: xml�ļ���mapper�ļ������ڵİ���, �����������xml�ļ�һ�ζ��ܼ��ظ�mybatis
            ʹ��package��Ҫ��
             1. mapper�ļ�������Ҫ�ͽӿ�����һ���� ���ִ�Сд��һ��
             2. mapper�ļ���dao�ӿ���Ҫ��ͬһĿ¼
        -->
        <package name="com.bjpowernode.dao"/>
    </mappers>

�����£�PageHelper

 PageHelper�����ݷ�ҳ�ġ�