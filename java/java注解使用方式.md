# java注解使用方式

## 注解（Annotation）介绍

注解是一种引用数据类型，编译之后生成.class文件

- 怎么使用，用在什么地方：

  - 一：语法格式是：

    `@注解类型名`

  - 二：注解可以出现在类上，属性上，方法上，变量上，注解类型上……

## 注解的开发方式

### 自定义简单注解

```java
// 注解的语法格式
[修饰符列表] @interface 注解类型名 {
    
}

// 自定义注解
public @interface MyAnnotation() {
    
}
```

### jdk lang包中自带的注解

```java
/*
	表示一个方法声明打算重写超类中的另一个方法声明

	此注解只出现在方法上，是给编译器参考的，个运行阶段没有关系，
	凡是带有此注解的，编译器都会进行编译检查，如果这个方法不是重写父类中的方法，编译器报错
	只在编译阶段起作用和运行阶段无关
*/
@Override
public String toString() {
    return "toString";
}

/*
	此注解标记的元素已过时
	传达一个消息，告知已过时，有更好的解决方法存在
*/
@Deprecated
public String toString() {
    return "toString";
}
```

### 元注解

用来标注“注解类型”的“注解”称为元注解

常见的元注解有那些

- Target **标注此注解只能出现在哪种类型上**
  - @Target(value={TYPE, FIELD, METHOD, PARAMETER, CONSTRUCTOR, LOCAL_VARIABLE, ANNOTATION_TYPE,  PACKAGE, TYPE_PARAMETER, TYPE_USE})   
  - 表示该注解可以出现在类上，字段上，方法上，构造方法上，本地变量上，注解类型上……
- Retention **标注此注解最终保存在哪里**
  - @Retention(RetentionPolicy.SOURCE)  表示该注解只能被保留在java源文件中
  - @Retention(RetentionPolicy.CLASS) 表示该注解被保存在class文件中
  - @Retention(RetentionPolicy.RUNTIME) 表示该注解被保存在class文件中，并且可以被反射机制获取到

源码分析

```java
/*
 * Copyright (c) 2003, 2013, Oracle and/or its affiliates. All rights reserved.
 * ORACLE PROPRIETARY/CONFIDENTIAL. Use is subject to license terms.
 */

package java.lang;

import java.lang.annotation.*;

/**
 * Indicates that a method declaration is intended to override a
 * method declaration in a supertype. If a method is annotated with
 * this annotation type compilers are required to generate an error
 * message unless at least one of the following conditions hold:
 *
 * <ul><li>
 * The method does override or implement a method declared in a
 * supertype.
 * </li><li>
 * The method has a signature that is override-equivalent to that of
 * any public method declared in {@linkplain Object}.
 * </li></ul>
 *
 * @author  Peter von der Ah&eacute;
 * @author  Joshua Bloch
 * @jls 9.6.1.4 @Override
 * @since 1.5
 */
@Target(ElementType.METHOD)   // 修饰 Override 注解只能出现在方法上（METHOD）
@Retention(RetentionPolicy.SOURCE)  // 表示  Override 注解只能被保留在java源文件中
public @interface Override {
}
```

## 自定义带有属性的注解

```java
@Target()
@Retention()
public @interface MyAnnotation() {
    
    /**
    * 通常在注解中可以定义属性，以下这个是MyAnnotation的name属性
    * 看起来是一个方法，但我们称之为一个属性name
    */
    String name();
    
    // 指定属性默认值
    String name() default "yuluo";
    
}

public class AnnotationTest() {
    
    /*
    	当注解中有属性值的时候，在使用注解的时候必须给属性赋值，否则报错
    	如果有默认值的时候可以不用指出属性值
    	有多个属性时使用逗号分开
    	@MyAnnotation(属性名=属性值)
    */
    @MyAnnotation(name="zhangsan")
    public void doSome() {
        System.out.println("dosome……");
    }
    
}

————————————————————————————————————————————————————————————————
    
public @interface MyAnnotation1() {
   
    // 当属性名的名字是value并且只有一个的时候，可以不用写出属性名
    String value();
    
}   

public class AnnotationTest1() {
    
    /*@MyAnnotation1(value = "zhangsan")
    public void doSome() {
        System.out.println("dosome……");
    }*/
    
    @MyAnnotation1("zhangsan")
    public void doSome() {
        System.out.println("dosome……");
    }
    
}
```

### 注解当中的属性可以是那些类型?

> byte short char int long float double boolean String Class enum
>
> 以及以上每一种类型的数组

## 反射注解

```java
// 获取这个类
Class c = Class.forName("indi.yuluo.java.annotation.MyAnnotationTest");
// 判断类上面是否有MyAnnotation注解
boolean flag = c.isAnnotationPresent(MyAnnotation.class);
// 获取注解
MyAnnotation myAnnotation = (MyAnnotation) c.getAnnotation(MyAnnotation.class);
// 获取注解对象的属性
String value = myAnnotation.value();
```

