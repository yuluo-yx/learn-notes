# java String类里面的常用方法

## 常用的构造方法

```java
String s = new String("");

String s = "";

String s = new String(byte数组);

String s = new String(byte数组, 起始下标，长度);

String s = new String(char数组);

String s = new String(char数组, 起始下标, 长度);
```

## 常用的方法

charAt()

```java
// 返回指定下标处的字符
String s = "yuluo";
char c = s.charAt(1);
System.out.println(c);   // c = u
```

compareTo()

```java
// 按字典顺序比较两个字符串，相等返回0，
//按字典顺序 比原先字符串少x个，返回x
// 比原先字符串多x个 返回-x
String s = "abc";
int result = s.compareTo("abc");
System.out.println(result);  // 相等为 0

int result1 = s.compareTo("a");
System.out.println(result1);   // 少2个 返回 2

int result2 = s.compareTo("abcdefg");
System.out.println(result2) // 多四个 返回-4
```

contains()

```java
// 判断字符串是否包含指定字符序列
```

endsWith()

```java
// 测试此字符串是否以指定的后缀结尾
```

getBytes()

```java
// 使用平台的默认字符集将此 String编码为字节序列，将结果存储到新的字节数组中
String s = "abc";
byte[] res = s.getBytes();
for (byte re : res) {
	System.out.println(res);  // 97 98 99
}

getBytes(Charset charset)
// 使用给定的charset将该String编码为字节序列，将结果存储到新的字节数组中    
```

isEmpty()

```java
// 返回 true如果，且仅当 length()为 0
```

replace()

```java
// 替换指定字符
String s  = "a b c";
String replace = s.replace("a", "哦");
System.out.println(replace);   // 哦 b c
```

replaceAll(String regex,  String replacement)

```java
//用给定的替换替换与给定的 [regular  expression](../util/regex/Pattern.html#sum)匹配的此字符串的每个子字符串
String res = s.replaceAll("[ab]", "e");
System.out.println(res);
```

substring()

```java
// 切割字符串
String substring = s.substring(1);
System.out.println(substring);

String substring1 = s.substring(0, 1);
System.out.println(substring1);
```

toCharArray()

```java
// 将此字符串转换为新的字符数组
```

trim()

```java
// 返回一个字符串，其值为此字符串，并删除任何前导和尾随空格
```

toLowerCase()

```java
// 转换小写
```

toUpperCase()

```java
// 转换大写
```

valueOf()

```java
// 将指定值转化为string
```

subString()

```java
// 按照指定的正则表达式切割字符串 返回一个字符串数组
String str = "a b c d e";
String[] s1 = str.split(" ");
for (String s2 : s1) {
     System.out.print("s2 = " + s2);
}
```



## StringBuilder 和 StringBuffer

StringBuffer中的方法都有：synchronized关键字修饰，表示他是在多线程环境下运行是安全的

StringBuilder中的方法没有synchronized关键字修饰，在多线程环境下是不安全的

在使用字符串拼接的时候建议使用jdk自带的append方法

**在使用StringBuffer的时候尽可能给定一个初始化容量**

**减少对底层数组的扩容次数，给一个较大的初始化荣富良**

```java
String s = "a b c";

StringBuilder stringBuilder = new StringBuilder(20);

stringBuilder.append(s);
System.out.println(stringBuilder); // a b c
```

