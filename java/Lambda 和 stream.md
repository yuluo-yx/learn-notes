# Lambda 和 stream

## lambda

```java
package indi.exer.yuluo.LambdAndStream;

/**
 * @author: yuluo
 * @FileName: Demo2.java
 * @createTime: 2022/6/1 11:25
 * @Description:
 */

public class Demo2 {

    public static void main(String[] args) {
        String val = "yuluo";
        // 匿名内部类形式
        Printer printer = new Printer() {
            @Override
            public void printer(String data) {
                System.out.println("匿名内部类形式输出");
            }
        };
        Demo2 demo2 = new Demo2();
        demo2.printerSomething(val, printer);

        /* lambda表达式 */

        Printer printer1 = (String data) -> {
            System.out.println(data);
        };
        printer1.printer("不省略数据类型和括号的表达式输出结果");

        Printer printer2 = (data) -> {
            System.out.println(data);
        };
        printer1.printer("省略数据类型和不省略括号的表达式输出结果");

        Printer printer3 = (data) -> System.out.println(data);
        printer3.printer("省略数据类型和大括号的输出结果");

        Printer printer4 = data -> System.out.println(data);
        printer4.printer("省略数据类型和大、小括号的输出结果，只有一个参数时可以去掉参数列表的小括号，当方法体只有一条语句时，大括号可以省略");
        // demo2.printerSomething("ererw", printer4);

        Printer printer5 = System.out::println;
        printer5.printer("使用类名：：方法名");
    }

    public void printerSomething(String data, Printer printer) {
        printer.printer(data);
    }

}

interface Printer {
    void printer(String data);
}
```

## Stream api

### 初体验 （代替for循环）

```java
package indi.exer.yuluo.LambdAndStream;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Locale;
import java.util.stream.Collectors;

/**
 * @author: yuluo
 * @FileName: StreamDemo2.java
 * @createTime: 2022/6/1 12:11
 * @Description:
 */

public class StreamDemo2 {

    public static void main(String[] args) {
        List<String> strings = Arrays.asList("Kobe", "james", "curry", "cyyt", "Lemur", "lacdbe");

        /* 要求：对list过滤出L开头的字母，然后L开头的字母变成大写，再排序，然后转换成为list */

        // 不使用stream
        List<String> tmp = new ArrayList<>();
        for (String string : strings) {
            if (string.startsWith("c")) {
                String temp = string;
                // 转大写
                String s = temp.toUpperCase();
                tmp.add(s);
            }
        }
        // 排序
        for (String s : tmp) {
            Arrays.sort(s.toCharArray());
        }
        // 输出
        System.out.println("非流式编程实现");
        System.out.println(tmp);


        // 使用stream api
        List<String> sortedList = strings.stream()
                .filter(s -> s.startsWith("c"))
                .map(String::toUpperCase)
                .sorted()
                .collect(Collectors.toList());

        System.out.println("流式编程实现");
        System.out.println(sortedList);

    }

}
```

**将数组转换成流：`stream.of(strings)`**

**也可以将文件，set，map等转换成流**

### filter函数与谓词逻辑

谓词逻辑：谓语是对主语的陈述或者说明，指出做什么，是什么，怎么样

where 和 and限定了主语employee是什么，那么where和and语句所代表的逻辑就是谓词逻辑

```sql
select * 
from employee
where 
age > 70 and gender = "男";
```

谓词逻辑的使用

```java
package indi.exer.yuluo.LambdAndStream;

import lombok.AllArgsConstructor;
import lombok.Data;

import java.util.function.Predicate;

/**
 * @author: yuluo
 * @FileName: Employee.java
 * @createTime: 2022/6/1 12:35
 * @Description:
 */

@Data
@AllArgsConstructor
public class Employee {

    private String name;
    private String sex;
    private Integer age;
    
    // 定义谓词逻辑
    public static Predicate<Employee> ageGreaterThan20 = x -> x.getAge() > 20;
    public static Predicate<Employee> genderM = x -> x.getSex().equals("男");    

}
```

```java
package indi.exer.yuluo.LambdAndStream;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

/**
 * @author: yuluo
 * @FileName: StreamDemo3.java
 * @createTime: 2022/6/1 12:37
 * @Description:
 */

public class StreamDemo3 {

    public static void main(String[] args) {
        Employee e1 = new Employee("yuluo", "男", 20);
        Employee e2 = new Employee("jack", "男", 28);
        Employee e3 = new Employee("jon", "男", 15);
        Employee e4 = new Employee("Alex", "女", 23);
        Employee e5 = new Employee("Neetu", "男", 21);
        Employee e6 = new Employee("tomcat", "女", 19);

        List<Employee> employees = Arrays.asList(e1, e2, e3, e4, e5, e6);

        /*List<Employee> employeeList = employees.stream()
                .filter(e -> e.getAge() >= 20 && e.getSex().equals("男"))
                .collect(Collectors.toList());*/
        List<Employee> employeeList = employees.stream()
                // 使用谓词逻辑 and或者or、.negate() 否定
                .filter(Employee.ageGreaterThan20.and(Employee.genderM))
                .collect(Collectors.toList());
        
        List<Employee> employeeList = employees.stream()
        // 使用negatef否定
        .filter(Employee.ageGreaterThan20
                 .and(Employee.genderM)
                 .negate())
        .collect(Collectors.toList());

        System.out.println(employeeList);
    }

}
```

### map数据转换

```java
package indi.exer.yuluo.LambdAndStream;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;
import java.util.stream.Stream;

/**
 * @author: yuluo
 * @FileName: StreamMapDemo4.java
 * @createTime: 2022/6/1 12:51
 * @Description:
 */

public class StreamMapDemo4 {

    public static void main(String[] args) {
        List<String> alpha = Arrays.asList("Monkey", "Lion", "Girffe", "Lemur");

        // 不使用stream管道流
        List<String> alphaUpper = new ArrayList<>();
        for (String s : alpha) {
            alphaUpper.add(s.toUpperCase());
        }

        System.out.println(alphaUpper);

        // 使用管道流
        alphaUpper = alphaUpper.stream()
                // map中的用法叫方法引用
                .map(String::toUpperCase)
                .collect(Collectors.toList());
        System.out.println(alphaUpper);

        // 获取字符长度
        Stream.of("Monkey", "Lion", "Girffe", "Lemur")
                // 得到每个字符的长度转换为int，再输出
                .mapToInt(String::length)
                .forEach(System.out::println);
    }

}
```

```java
package indi.exer.yuluo.LambdAndStream;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

/**
 * @author: yuluo
 * @FileName: StreamMapDemo5.java
 * @createTime: 2022/6/1 12:58
 * @Description:
 */

public class StreamMapDemo5 {

    public static void main(String[] args) {
        Employee e1 = new Employee("yuluo", "男", 20);
        Employee e2 = new Employee("jack", "男", 28);
        Employee e3 = new Employee("jon", "男", 15);
        Employee e4 = new Employee("Alex", "女", 23);
        Employee e5 = new Employee("Neetu", "男", 21);
        Employee e6 = new Employee("tomcat", "女", 19);

        List<Employee> employees = Arrays.asList(e1, e2, e3, e4, e5, e6);

        /*
        如果map函数操作之前和操作之后返回的值一样，都是employee，那么可以使用peek函数来代替map函数，
        peek函数可以省略返回值
         */
        List<Employee> employeeList = employees.stream()
                .map(e -> {
                    // 将所有年龄+1
                    e.setAge(e.getAge() + 1);
                    // 如果是男的话转成male，女的是female
                    e.setSex(e.getSex().equals("男") ? "male" : "female");
                    // 返回元素 e
                    return e;
                }).collect(Collectors.toList());

        System.out.println(employeeList);
    }

}
```

### faltMap

```java
package indi.exer.yuluo.LambdAndStream;

import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

/**
 * @author: yuluo
 * @FileName: StreamMapDemo6.java
 * @createTime: 2022/6/1 15:56
 * @Description:
 */

public class StreamMapDemo6 {

    // 展开的map
    public static void main(String[] args) {
        List<String> strings = Arrays.asList("hello", "world");

        // 现在想遍历数组打印出hello world
        strings.stream()
                .map(w -> w.split(""))
                .forEach(System.out::println);

        /*
        上述这种方式并不能打印输出字符
        输出结果为
        [Ljava.lang.String;@6acbcfc0
        [Ljava.lang.String;@5f184fc6
        实际上这样的结果是:
        List<List<String>>  在对每个单词split之后
         */

        strings.stream()
                .flatMap(w -> Arrays.stream(w.split("")))
                .forEach(System.out::print);
        
        // hello world
    }

}
```

## Stream的状态与并行操作

Stream操作：

- Stream源操作

  1. 数组转stream
  2. 集合类转stream
  3. 行文本转stream

- Stream中间操作

  - 无状态操作

    1. filter
    2. map
    3. flatmap

  - 有状态操作

    1. distinct：去重
    2. limit：去前多少个
    3. skip：跳过前几个取后面的
    4. sorted：默认按字母表的顺序排序

  - 区分有，无状态的原因

    > 并行操作：对元素处理速度块，数据输出没办法保证，没办法得到想要的输出结果
    >
    > 串行操作：确保每一个输入都有对应的输出，输出和输入对应

- stream终端操作

  

## stream性能

在某些场景下，根据数据类型、数据容量的区别，for循环和stream的性能不同

不要人云亦云

> Stream并行流计算 >> 普通for循环 >> stream串行流计算
>
> 数据容量越大，stream流的执行效率越高
>
> stream并行流计算通常能够比较好的利用cpu的多核又是，cpu核心越多，stream并行流计算效率越高

## 集合排序

```java
package indi.exer.yuluo.LambdAndStream;

import java.util.Arrays;
import java.util.Comparator;
import java.util.List;

/**
 * @author: yuluo
 * @FileName: StreamSorted.java
 * @createTime: 2022/6/1 17:04
 * @Description:
 */

public class StreamSorted {

    public static void main(String[] args) {
        Employee e1 = new Employee("yuluo", "男", 20);
        Employee e2 = new Employee("jack", "男", 28);
        Employee e3 = new Employee("jon", "男", 15);
        Employee e4 = new Employee("Alex", "女", 23);
        Employee e5 = new Employee("Neetu", "男", 21);
        Employee e6 = new Employee("tomcat", "女", 19);

        List<Employee> employees = Arrays.asList(e1, e2, e3, e4, e5, e6);

        // 排序
        employees.sort(
                Comparator.comparing(Employee::getSex)
                // 然后性别，年龄倒序排序
                .thenComparingInt(Employee::getAge)
                .reversed()
        );
        employees.forEach(System.out::println);

        System.out.println();

        // 正序排序
        employees.sort(
                Comparator.comparing(Employee::getSex)
                .thenComparingInt(Employee::getAge)
        );

        employees.forEach(System.out::println);
    }

}
```

输出结果：

Employee(name=jack, sex=男, age=28)
Employee(name=Neetu, sex=男, age=21)
Employee(name=yuluo, sex=男, age=20)
Employee(name=jon, sex=男, age=15)
Employee(name=Alex, sex=女, age=23)
Employee(name=tomcat, sex=女, age=19)

Employee(name=tomcat, sex=女, age=19)
Employee(name=Alex, sex=女, age=23)
Employee(name=jon, sex=男, age=15)
Employee(name=yuluo, sex=男, age=20)
Employee(name=Neetu, sex=男, age=21)
Employee(name=jack, sex=男, age=28)

## 函数式接口（所有函数式接口都可以用lambda表达式实现）

- 接口有且仅有一个抽象方法，如上图的抽象方法cpmpare
- 允许定义静态非抽象方法
- 允许定义默认default非抽象方法（default方法也是java8才有的）
- FunctionInterface方法不是必须的
- 允许Object中的public方法，如上图的equals

default关键字

- default解决的问题：当一个接口有很多实现类的时候，修改这个接口就会变得很麻烦，需要修改这个接口所有的实现类

- default方法可以有自己默认实现，既有方法体
- 接口实现类可以不去实现default方法，并且可以使用default方法

## stream流的方式查找和匹配元素

### 匹配

anyMatch() : 匹配stream流中是否有符合匹配规则的元素，匹配规则可以是谓词和lambda表达式

```java
package indi.exer.yuluo.LambdAndStream;

import java.util.Arrays;
import java.util.List;

/**
 * @author: yuluo
 * @FileName: MatchFind.java
 * @createTime: 2022/6/1 17:24
 * @Description:
 */

public class MatchFind {

    public static void main(String[] args) {
        Employee e1 = new Employee("yuluo", "男", 20);
        Employee e2 = new Employee("jack", "男", 28);
        Employee e3 = new Employee("jon", "男", 15);
        Employee e4 = new Employee("Alex", "女", 23);
        Employee e5 = new Employee("Neetu", "男", 21);
        Employee e6 = new Employee("tomcat", "女", 19);

        List<Employee> employees = Arrays.asList(e1, e2, e3, e4, e5, e6);

        // 查找是否有年龄大于70的
        // for循环方式
        boolean isExistAgeThan70 = false;
        for (Employee employee : employees) {
            if (employee.getAge() > 20) {
                isExistAgeThan70 = true;
                break;
            }
        }
        System.out.println("for: " + isExistAgeThan70);

        // stream流
        isExistAgeThan70 = employees.stream().anyMatch(e -> e.getAge() > 70);
        System.out.println(isExistAgeThan70);

        // 使用谓词 判断是否有年龄大于20的
        System.out.println(employees.stream().anyMatch(Employee.ageGreaterThan20));

    }
}
```

allMatch() : 判断所有的元素是否满足一个条件，比如所有员工的年龄都大于10岁

noneMatch() : 判断是否存在和条件相反的元素

### 查找

**Operation可以解决空指针异常问题**

`findFirst() `： 找出第一个

```java
// 查找是否有年龄大于20的员工，并且把第一员工信息打印出来
Optional<Employee> optionalEmployee
        = employees.stream().filter(e -> e.getAge() > 20).findFirst();
System.out.println(optionalEmployee.get());
```

`Employee(name=jack, sex=男, age=28)`

`orElse()`：查找值不存在，不想返回一个NullPoint，可以给一个默认值

`ifPresent()`: 如果这个值存在，做一些操作

`isPresent`

`get()`: 存在这个值，拿出来这个值

`findAny()`: 找出所有的满足条件的值

## 集合元素规约

概念：集合类转换成一个（Integer、String……）对象

```java
package indi.exer.yuluo.LambdAndStream;

import java.util.Arrays;
import java.util.List;

/**
 * @author: yuluo
 * @FileName: ReduceDemo.java
 * @createTime: 2022/6/1 17:43
 * @Description:集合元素规约
 */

public class ReduceDemo {

    public static void main(String[] args) {
        List<Integer> integers = Arrays.asList(1, 3, 4, 6, 7, 8, 30);

        // 参数为初始值，累加器，合并器
        // 合并器作用：将累加器结果合并，当类型不匹配的时候，转换累计器的结果
        Integer reduce = integers.stream().reduce(0, (subtotal, element) -> subtotal + element);
        System.out.println(reduce);

        int total = integers.stream().reduce(0, Integer::sum);
        System.out.println(total);

        // 字符串累加
        List<String> strings = Arrays.asList("a", "g", "d", "e", "r");
        String totalString = strings.stream().reduce("", String::concat);
        System.out.println(totalString);
        
        // 并行流合并器
        Integer t = integers.parallelStream()
                .reduce(0, Integer::sum, Integer::sum);
        System.out.println(t);
    }

}
```