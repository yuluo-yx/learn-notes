# java中常用的的方法

## Integer类中的方法

parseInt(String s)

```java
// 将字符串参数解析为带符号的十进制整数
String s = "123";
System.out.println(Integer.parseInt(s));  // 123
```

toBinaryString(int i)`

```java
// 在基数2中返回整数参数的字符串表示形式为无符号整数
int num = 3;
System.out.println("2进制" + Integer.toBinaryString(num));
```

toHexString(int i)

```java
// 返回整数参数的字符串表示形式，作为16位中的无符号整数
System.out.println("8进制" + Integer.toOctalString(num));
```

toOctalString(int i)

```java
// 在基数8中返回整数参数的字符串表示形式为无符号整数
System.out.println("16进制" + Integer.toHexString(num));
```

## String int Integer互相转换的方法

int -> String

`数字 + '', String.valueOf(int i)`

String -> int

`Integer.parseInt(String s)`

int -> Integer

`自动装箱， Integer.valueOf(int i)`

Integer -> int

`自动拆箱，intValue(Integer i)`

String -> Integer

`Integer.valueOf(String s)`

Integer -> String

`String.valueOf(Integer i)`

## java中对日期的处理

### 构造器

```java
// 获取系统当前时间
Date nowTime = new Date();
System.out.println(nowTime);
```

### 日期格式化

#### Date -> String

SimpleDateFormat类在java.text包下

````java
// 其中一个构造方法
SimpleDateFormat(String pattern)

 使用给定模式 `SimpleDateFormat`并使用默认的 [`FORMAT`](../../java/util/Locale.Category.html#FORMAT)语言环境的默认日期格式符号`
````



- 指定的字符串格式

- > 
  >
  > | Date and Time Pattern            | Result                                 |
  > | -------------------------------- | -------------------------------------- |
  > | `"yyyy.MM.dd G 'at' HH:mm:ss z"` | `2001.07.04 AD at 12:08:56 PDT`        |
  > | `"EEE, MMM d, ''yy"`             | `Wed, Jul 4, '01`                      |
  > | `"h:mm a"`                       | `12:08 PM`                             |
  > | `"hh 'o''clock' a, zzzz"`        | `12 o'clock PM, Pacific Daylight Time` |
  > | `"K:mm a, z"`                    | `0:08 PM, PDT`                         |
  > | `"yyyyy.MMMMM.dd GGG hh:mm aaa"` | `02001.July.04 AD 12:08 PM`            |
  > | `"EEE, d MMM yyyy HH:mm:ss Z"`   | `Wed, 4 Jul 2001 12:08:56 -0700`       |
  > | `"yyMMddHHmmssZ"`                | `010704120856-0700`                    |
  > | `"yyyy-MM-dd'T'HH:mm:ss.SSSZ"`   | `2001-07-04T12:08:56.235-0700`         |
  > | `"yyyy-MM-dd'T'HH:mm:ss.SSSXXX"` | `2001-07-04T12:08:56.235-07:00`        |
  > | `"YYYY-'W'ww-u"`                 | `2001-W27-3`                           |

```java
// 日期格式化
SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd:HH:mm:ss");
String date = sdf.format(nowTime);
System.out.println(date);   // 2022-02-05:13:29:22
```

#### String -> Date

```java
// String -> Date
String date1 = "2020-01-01:12:12";
SimpleDateFormat sdf1 = new SimpleDateFormat("yyyy-MM-dd:HH:mm");
Date date2 = sdf1.parse(date1);
System.out.println(date2); // Wed Jan 01 12:12:00 CST 2020
```

### 记录程序的运行时间

```java
// 获取执行前的毫秒数
long begin = System.currentTimeMillis();

// 要检测的代码块

// 获取执行后的毫秒数
long end = System.currentTimeMillis();

System.out.println("耗费时长" + (end - begin) + "毫秒")；

```

## System；类中的常用方法

```java 
System.out   [out是System类的静态变量]
System.out.println()  [println()是PrintStream类的的方法]
System.gc() 建议启动垃圾回收期
System.currentTimeMillies() 获取自1970年00时00分00秒 000毫秒到今的总毫秒数
System.exit(0) 退出jvm虚拟机
```

## 数字格式化

```java
// DecimalFormat专门负责数字格式化
// DecimalFormat df = new DecimalFormat("数字格式");
```

- 数字格式
  - \# 代表任意数字
  - , 代表千分位
  - . 代表小数点
  - 0 代表不够时补0

```java
double number = 1234.56;
DecimalFormat df = new DecimalFormat("###,###.##");
String data = df.format(number);

System.out.println(data);  // 1,234.56
```



## 读取资源文件

资源绑定器，只能绑定xxx.properties文件，并且这个文件必须在类路径下

`ResourceBundle bundle = ResourceBundle.getBundle("jdbc");`

`String Driver = bundle.getString("mysql.Driver")`

