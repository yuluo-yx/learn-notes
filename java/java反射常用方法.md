# java反射常用方法

通过反射机制可以操作字节码文件

## 反射class的三种方式

### 一

`Class.forName()`

- 静态方法
- 方法的参数是一个字符串
- 完整类必须带有包名
- **此方法的执行会导致类加载行为**，只希望静态代码块执行，不希望别的代码块执行的时候可以使用forName方法，

```java
// 数据库的驱动类 
Class.forName(com.mysql.cj.jdbc.Driver)
```

### 二

java中任何一个对象都有一个方法: getClass()

```java
String s = "abc";
Calss x = s.getClass();
```

### 三

任何一个对象都有class属性

```java
// Class c = 任何类型.class;
Class s = String.class;
```



## 通过反射实例化对象

### newInstance()

底层会调用类的无参构造方法来完成对象的创建

没有无参构造方法时出现**实例化异常**

```java
Class s = Class.forName("indi.yuluo.bean.Student");
Object obj = s.newInstance();
System.out.println(obj);  //indi.yuluo.bean.Student@of7864
```





