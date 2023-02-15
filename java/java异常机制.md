# java异常机制

## 异常

```md
异常以类的形式存在，可以通过new的方式创建一场对象
发生异常的时候，其实JVM已经new了一个新的异常对象，并且输出到控制台上，异常信息是JVM打印出来的
继承自 Throwable (可抛出的)
```

```md
try……catch中的finally子句，
    finally子句是最后执行的，并且一定会执行
    和try语句一起出现
    通常在什么情况下使用？
        1，finally子句通常完成资源的释放/关闭
        2，即使try中的代码出现异常，finally中的代码也会正常执行
        3,try语句可以和finally可以一起使用，即使try中有return语句，
        finally中的语句也会执行,并且在finally后面的语句不会执行
        4，退出JVM之后，finally中的语句就不执行了
            System.exit(0);

final finally finalize的区别：
    1，final是一个关键字，
    修饰的类不能被继承，
    修饰的变量不能被重新赋值
    修饰的方法不能被覆盖/重写
    2，finally语句和try语句一起使用，处理异常
    3，finalize是一个方法名，是object中的方法
    这个方法是有立即回收器GC负责调用的
```

## 自定义异常的使用方法

```java
package indi.exer.yuluo.exception;

/**
 * @author: yuluo
 * @createTime: 2022/2/3 21:26
 * @File: Exception.java
 * @Software: IntelliJ IDEA
 * @Description: 自定义异常：
 *      两步：
 *          第一步：编写一个继承Exception或者RunTimeException
 *          第二步：写一个无参或者String类型的构造器
 */


public class MySetExceptionTest extends Exception {

    public MySetExceptionTest(){}

    public MySetExceptionTest(String msg) {super(msg);}

}
```

测试

```java
package indi.exer.yuluo.exception;

/**
 * @author: yuluo
 * @createTime: 2022/2/4 15:30
 * @File: test.java
 * @Software: IntelliJ IDEA
 * @Description:
 */

public class test {

    public static void main(String[] args) {
        MySetExceptionTest e = new MySetExceptionTest("出现异常！");

        // 打印异常堆栈信息
        e.printStackTrace();

        // 打印异常的简要描述信息
        System.out.println(e.getMessage());

    }

}
```

