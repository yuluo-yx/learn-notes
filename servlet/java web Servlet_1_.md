

# java web Servlet

*******

## 模拟servlet本质

### 充当sun公司的角色，指定servlet规范

- javax.servlet.Servlet

### 充当tomcat服务器的开发者

- 

###  充当webapp的开发者

- BankServlet implements Servlet
- UserListServlet implements Servlet
- UserLoginServlet implements Servlet



### 分析结果

- 对于我们java web的程序员来说，我们只需要做两件事，

  > 编写一个类来实现Servlet接口，
  >
  > 将编写好的类配置到配置文件中，，在配置文件中指定  请求路径  和  类名  之间的关系

- 注意：

  > tomcat的开发者已经为我们写好了Tomat，所以
  >
  > - 这个配置文件的文件名不能乱写，固定的
  > - 这个配置文件的路径不能乱来，固定的
  > - 文件名，文件路径，servlet规范都是sun公司规定的

- Servlet种规定了

  > webapp是一个怎样的目录结构
  >
  > webapp应该有一个什么样的配置文件
  >
  > webapp的配置文件应该放在那里
  >
  > webapp的java程序放在那里
  >
  > 这些都是Servlet规定的

Tomcat要遵守Servlet的规范，java web的程序员要遵循这个规范，这样tomcat才能和webapp解耦合

## 代码：

```java
package indi.yuluo.apache.Tomcat;

import indi.yuluo.javax.Servlet.Servlet;

import java.io.FileReader;
import java.util.Properties;
import java.util.Scanner;

/**
 * @author: yuluo
 * @createTime: 2022/1/8 15:14
 * @Description:  充当tomcat的开发者
 */

public class tomcat_copy {

    public static void main(String[] args) throws Exception{
        System.out.println("Tomcat服务器启动成功，开始接受用户的访问");

        // 简单的使用Scanner来模拟用户的请求
        // 用户访问浏览器是通过服务器上的“请求路径”
        // 也就是说用户请求的路径不同，后台执行的servlet不同
        System.out.println("输入访问路径：");
        Scanner scanner = new Scanner(System.in);

        // 用户的请求路径
        String key = scanner.nextLine();

        /**
         * 这之间有一种规范，是请求路径和XXXServlet之间的对照关系，由webapp的开发者
         * 通过属性配置文件来指
         * 即 web.properties
         */
        String webpropertiesPath = "F:\\Java\\Java-project\\java-web-two\\servlet_copy\\src\\indi\\yuluo\\web.properties";
        FileReader fileReader = new FileReader(webpropertiesPath);
        Properties properties = new Properties();
        properties.load(fileReader);

        fileReader.close();

        // 通过key获取value
        String className = properties.getProperty(key);
        // 通过反射创建对象
        Class clazz = Class.forName(className);
        Object obj = clazz.newInstance();
        /*
        这个obj的类型对于tomcat开发人员来说，不知道obj的具体类型
        不知道webapp的开发者写了那些类，但是他们都继承了Servlet这个类
        但是都实现了这个servlet里面的service这个方法，直接使用service这个方法即可
         */
        Servlet servlet = (Servlet)obj;
        servlet.service();
    }

}

```

```java
package indi.yuluo.javax.Servlet;

/**
 * @author: yuluo
 * @createTime: 2022/1/8 15:00
 * @Description: 充当sun公司的开发者，指定servlet规范
 */

public interface Servlet{

    /**
     * 专门提供服务的方法
     */
    void service();

}

```

```java
package indi.yuluo.Servlet;

import indi.yuluo.javax.Servlet.Servlet;

/**
 * @author: yuluo
 * @createTime: 2022/1/8 15:06
 * @Description: 银行业务接口  充当webapp的开发者
 */

public class BankServlet implements Servlet {


    @Override
    public void service() {
        System.out.println("BankServlet`s service……");
    }
}

```

```java
package indi.yuluo.Servlet;

import indi.yuluo.javax.Servlet.Servlet;

/**
 * @author: yuluo
 * @createTime: 2022/1/8 15:11
 * @Description:
 */

public class UserLoginServlet implements Servlet{

    @Override
    public void service() {
        System.out.println("UserLoginServlet`s service……");
    }
}

```

```properties
/userlogin=indi.yuluo.Servlet.UserLoginServlet
/bankServlet=indi.yuluo.Servlet.BankServlet
```

结果：

```
F:\Java\Java-project\java-web-two\out\production\servlet_copy1 indi.yuluo.apache.Tomcat.tomcat_copy
Tomcat服务器启动成功，开始接受用户的访问
输入访问路径：
/userList
UserListServlet`s service……

```

## 开发一个带有servlet（java)的webapp

- 开发步骤
  1. 在webapps目录下新建一个目录，起名crm（crm就是这个webapp的名字。当然，也可以是其他项目）**注意：这个crm就是这个webapp的根**
  2. 在webapp的根下新建一个目录：WEB-INF  **注意：这个目录的名字是servlet规范中规定的，必须全部大写**
  3. 在WEB-INF下新建一个目录：classes  **注意：这个目录的名字是servlet规范中规定的，里面存放的是编译过后的class文件（字节码文件）**
  4. 在WEB-INF里面新建一个目录：lib  **注意：这个目录不是必须的，但是如果一个webapp用到了第三方jar包，这个jar包要放到这个目录下，比如数据库的jar包**
  5. 在WEB-INF目录里新建下一个文件：web.xml **注意：这个文件是必须的，必须要叫做web.xml,一个合法的webapp的程序吗，这个文件是必须的，在这个文件里面描述了请求路径和Servlet类之间的对照关系**
  6. 编写一个java程序，这个java程序不能随意编写，必须要实现Servlet接口，Servlet是javaEE规范的一员。
  
  > java代码可以在任意位置编写，只要保证编译之后的class文件在classes文件夹里面即可
  >
  > html页面只能放到WEB-INF目录之外
  
  ***不需要编写main方法，在tomcat服务器会复杂调用main方法，***
  
  
  
  ## 关于javaEE的版本
  
  - javaEE目前最高的版本是javaEE8
  - javaEE被Oracle捐献了，Oracle将javaEE规范捐献给了Apache
  - Apache把javaEE改名了，不叫作javaEE，就做jakartaEE（）
  - javaEE版本升级之后的javaEE9不是“javaEE9”，叫做jakarta [dʒəˈkɑ:tə]EE9.
  - javaEE8的时候对应的Servlet类名是javax.servlet.Servlet
  - jakartaEE9的时候对应Servlet类名是：jakarta.servlet.Servlet
  - 之前的项目不能使用Tomcat10运行，不识别，在Tomcat9—可以正常运行

## 学了HttpServlet之后，如何正确的编写一个servlet应用

1. 继承HttpServlet类
2. 该写doPost写doPost，doGet就是doGet，由java程序员说了算
3. 配置Servlet类到web.xml文件中
4. 准备前端页面，form表单，form表单指定请求路径

