## 纯注解版spring与mybatis整合

### 基础配置 （配置Bean）

``` java
1.连接池
    <!-- 连接池 -->
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
    <property name="driverClassName" value="com.mysql.cj.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/jdbc?useSSL=false"/>
    <property name="username" value="root"/>
    <property name="password" value="123456"/>
</bean>
    
    @Bean
    public DruidDataSource dataSource() {
    	DruidDataSource dataSource = new DruidDatasource();
    	datasource.setDriverclassName("  ")
        dataSource.setUrl();
    	……
        return dataSource;
	}

2.SqlSessionFactoryBean
    <!-- 创建SqlSessionFactoryBean SqlSessionFactoryBean -->
    <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean"/>
        <property name="dataSource" ref="dataSource"/>
        <property name="typeAliasePackage" value="indi.exer.yuluo.entity"/>
        <property name="mapperLocations">
        	<list>
        		<value>classpath:indi.exer.yuluo.mapper/\*Mapper.xml<value>
            </list>
        </property>
     </bean>
                    
      @Bean
      public SqlSessionFactoryBean sqlSessionFactoryBean(Datasource dataSource) {
      	SqlSesssionFactoryBean sqlSessionFactortyBean = new SqlSessionFactortyBean(); 
        sqlSessionFactortyBean.setDataSource(dataSource);
        sqlSessionFactortyBean.setTypeAliasesPackage(" ");    
        ……
        return sqlSessionFactoryBean;
      }
3.MapperScannerConfigure
  <!-- 创建DAO对象 MapperScannerConfigure-->
  <bean id="scanner" class="org.mybatis.spring.mapper.MapperScannerConfigure"/>
   	<property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryBean"/>
    <property name="basePackage" value="indi.exer.yuluo.dao"/>
  </bean>
   
  @MapperScan(basePackages={"indi.exer.,yuluo.dao"})
  // 注解会自动扫描sqlSessionFactoryBean
```

### 详细编码：

````java
配置Bean MyBatisAutoConfiguration

package indi.exer.yuluo.mybatis;

import com.alibaba.druid.pool.DruidDataSource;
import org.mybatis.spring.SqlSessionFactoryBean;
import org.mybatis.spring.annotation.MapperScan;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.Resource;
import org.springframework.core.io.support.PathMatchingResourcePatternResolver;
import org.springframework.core.io.support.ResourcePatternResolver;

import javax.sql.DataSource;
import java.io.IOException;

/**
 * @author: yuluo
 * @createTime: 2021/12/11 11:22
 * @Description:
 */

@Configuration
@ComponentScan(basePackages = "indi.exer.yuluo.mybatis")
@MapperScan("indi.exer.yuluo.mybatis")
public class MyBatisAutoConfiguration {

    // 对字符串的耦合进行相关的成员变量的注入
    @Autowired
    private MybatisProperties mybatisProperties;

    @Bean
    public DataSource dataSource() {
        DruidDataSource dataSource = new DruidDataSource();
        dataSource.setDriverClassName(mybatisProperties.getDriverClassName());
        dataSource.setUrl(mybatisProperties.getUrl());
        dataSource.setUsername(mybatisProperties.getUsername());
        dataSource.setPassword(mybatisProperties.getPassword());
        return dataSource;
    }

    @Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource) {

        SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
        sqlSessionFactoryBean.setDataSource(dataSource);
        sqlSessionFactoryBean.setTypeAliasesPackage(mybatisProperties.getTypeAliasesPackages());
        /*sqlSessionFactoryBean.setMapperLocations(new ClassPathResource[]{
                new ClassPathResource("UserDAOMapper.xml")});*/

        // 通配写法
        ResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
        Resource[] resources = new Resource[0];
        try {
            resources = resolver.getResources(mybatisProperties.getMapperLocations());
        } catch (IOException e) {
            e.printStackTrace();
        }
        sqlSessionFactoryBean.setMapperLocations(resources);
        return sqlSessionFactoryBean;
    }

}

通过properties属性配置文件来解决字符串之间的耦合 MybatisProperties

package indi.exer.yuluo.mybatis;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;

/**
 * @author: yuluo
 * @createTime: 2021/12/11 16:09
 * @Description:
 */

@Component
@PropertySource("classpath:mybatis.properties")
public class MybatisProperties {
    @Value("${mybatis.driverClassName")
    private String driverClassName;
    @Value("${mybatis.url")
    private String url;
    @Value("${mybatis.username")
    private String username;
    @Value("${mybatis.password")
    private String password;
    @Value("${mybatis.typeAliasesPackages")
    private String typeAliasesPackages;
    @Value("${mybatis.mapperLocations")
    private String mapperLocations;

    public String getDriverClassName() {
        return driverClassName;
    }

    public void setDriverClassName(String driverClassName) {
        this.driverClassName = driverClassName;
    }

    public String getUrl() {
        return url;
    }

    public void setUrl(String url) {
        this.url = url;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPassword() {
        return password;
    }

    public void setPassword(String password) {
        this.password = password;
    }

    public String getTypeAliasesPackages() {
        return typeAliasesPackages;
    }

    public void setTypeAliasesPackages(String typeAliasesPackages) {
        this.typeAliasesPackages = typeAliasesPackages;
    }

    public String getMapperLocations() {
        return mapperLocations;
    }

    public void setMapperLocations(String mapperLocations) {
        this.mapperLocations = mapperLocations;
    }
}

mybatis.properties 文件
    
# spring和mybatis整合问题的配置Bean耦合问题 用properties文件解决

mybatis.driverClassName = com.mysql.ci.jdbc.Driver
mybatis.url = jdbc:mysql://localhost:3306/jdbc?useSSL=false
mybatis.username = root
mybatis.password = 123456
mybatis.typeAliasesPackages = indi.exer.yuluo.mybatis
mybatis.mapperLocations = indi.exer.yuluo.mapper/*Mapper.xml

````

