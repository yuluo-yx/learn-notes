# JVM

## JVM介绍

- java代码一次编写，到处运行。

- 自动内存管理，垃圾回收功能
- 代码错误编译时期检查

### 学习JVM的作用

> 面试
>
> 理解底层的实现原理
>
> 中高级程序员的必备技能

## 内存结构

### 程序计数器 【Program counter Register】（寄存器）

> **将java代码由jvm解释为二进制字节码（jvm指令）讲过解释器翻译为机器码，再交给cpu执行**
>
> 程序计数器：表示程序代码的执行顺序，记住下一条jvm指令的地址。
>
> 在物理上通过寄存器来执行

特点：

- 是线程私有的
- 不会存在内存溢出问题

### 虚拟机栈

#### 定义

栈数据结构的特点：先进后出，

> 栈：线程运行时需要的内存空间
>
> 栈内的元素叫做**栈帧**（局部变量，参数，返回地址）：每个方法运行时需要的内存
>
> 每个栈只能有一个活动栈帧，对应着当前正在执行的方法

#### 问题辨析：

- 垃圾回收是否涉及栈内存？

  不涉及

- 栈内存分配越大越好吗？

  不是，栈分配内存越大，线程运行越少。因为物理内存有限
  
- 方法内的局部变量是否线程安全

  - 如果方法内的局部变量没有逃离方法的作用范围访问，它是线程安全的
  - 如果局部变量引用了对象，并且逃离了方法的作用范围，需要考虑线程安全的问题


#### 栈内存溢出

- 栈帧（局部变量，参数，返回地址）过多引发内存溢出。

  ```java
  /**
   * @Author: yuluo
   * @CreateTime: 2022-11-11  21:50
   * @Description: 可以通过-Xss256k 来设置栈内存的大小
   */
  
  public class StackOverFlow {
  
      private static int count;
  
      public static void main(String[] args) {
          try {
              method1();
          } catch (Throwable e) {
             e.printStackTrace();
              System.out.println(count);
          }
      }
  
      private static void method1() {
          count ++;
          method1();
      }
  
  }
  ```

  **在不设置栈大小的情况下，运行到20000多次出现`	at StackOverFlow.method1(StackOverFlow.java:22)`异常**

  

- 栈帧（局部变量，参数，返回地址）过大导致栈内存溢出  （不轻易出现）

#### 线程运行诊断

- CPU占用过多

  - **jstack** 工具 可以帮助我们查看那个线程出现CPU占用过多的问题 使用方式 `jstack + pid（进程id）`

- 程序运行很长时间没有结果

  - 程序出现死锁

    ```java
    /**
     * @Author: yuluo
     * @CreateTime: 2022-11-11  22:08
     * @Description: TODO
     */
    
    public class DiedLock {
    
        static A a = new A();
        static B b = new B();
    
        public static void main(String[] args) throws InterruptedException {
            new Thread(()->{
                synchronized (a) {
                    try{
                        Thread.sleep(2000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    synchronized (b) {
                        System.out.println("我获得了a和b");
                    }
                }
            }).start();
    
            Thread.sleep(1000);
    
            new Thread(()->{
                synchronized (b) {
                    try{
                        Thread.sleep(2000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    synchronized (a) {
                        System.out.println("我获得了b和a");
                    }
                }
            }).start();
        }
    }
    
    class A {}
    
    class B {}
    ```

### 本地方法栈（Native Method Stacks）

不是由java代码编写的方法，在一些需要调用操作系统底层的资源时，java不好处理。所以java间接通过本地方法接口的形式去调用`c`或者`c++`写的代码去完成功能。

此时使用的栈就是**本地方法栈**为方法运行提供空间

例子：

Object中的clone方法、hacode方法和wait方法等

```java
protected native Object clone() throws CloneNotSupportedException;
```

### 堆

#### 简介

通过new 关键字创建的对象都都会使用堆内存

特点：

- 堆是线程共享的，堆中对象都需要考虑线程安全的问题
- 有垃圾回收机制

#### 堆内存溢出

对象被回收的条件是当前不被使用，就会被回收。如果当前对象一直被使用，GC就无法回收此对象，造成内存溢出

```java
public static void main(String[] args) {
    int i = 0;
    try {
        List<String> list = new ArrayList<>();
        
        String a = "hello";
        while(true) {
            list.add(a);
            a = a + a;
            i ++;
        }
    } catch (Throwable e) {
        e.printStackTrace();
        System.out.println(i);
    }  
}
```

代码中的String字符串一直追加，放入list数组中。最终会造成堆内存溢出异常

> 堆内存参数调整：`-Xmx8m`  默认为4G

#### 堆内存诊断

> 相关工具
>
> - jps：查看当前系统中有哪些java进程，并显示对应的进程id
> - jmap：查看堆内存占用情况  `jmap -heap 进程id`
> - jconsole：图形界面监测工具。可以连续检测
> - jvirsualvm：推荐使用

- 垃圾回收多次，但是内存空间一直没有得到释放

  > 通过jvirsalVM监控堆内存中对象

### 方法区

#### JVM方法区定义

- 所有jvm线程共享的区域
- 存储的是和类相关的信息，成员属性，方法等

#### 不同的实现

Oracle的`HotSpot`虚拟机

> 在1.8版本之前，方法区的实现方式是`PermGen(永久代)`实现的
>
> 在1.8及以后的版本通过`Metaspace(元空间)`实现

#### 方法区的内存溢出

```java
import jdk.internal.org.objectweb.asm.ClassWriter;
import jdk.internal.org.objectweb.asm.Opcodes;

/**
 * @Author: yuluo
 * @CreateTime: 2022-11-15  12:39
 * @Description: 演示元空间内存溢出
 *
 * 设置元空间大小
 * 参数 -XX:MaxPermSize=8m
 *
 * ClassLoader：用来加载类的二进制字节码
 * ClassWriter：作用是生成类的二进制字节码
 * visit方法的参数：版本号，权限修饰符 public，类名，包名，父类，继承接口
 * defineClass执行类的加载
 */

public class MethodDemo extends ClassLoader {
    public static void main(String[] args) {
        int j = 0;
        try {
            MethodDemo test = new MethodDemo();
            for (int i = 0; i < 10000; i++, j++) {
                ClassWriter cw = new ClassWriter(0);
                cw.visit(Opcodes.V1_8, Opcodes.ACC_PUBLIC, "Class" + i, null, "java/lang/Object", null);
                byte[] code = cw.toByteArray();
                test.defineClass("Class" + i, code, 0, code.length);
            }
        } finally {
            System.out.println(j);
        }
    }
}
```

> 动态加载字节码的应用：spring和mybatis框架中的`cglib`动态类加载

#### 运行时常量池

- 常量池

  > 就是一张表，虚拟机指令根据这张常量表找到需要执行的类名，方法名，参数类型，字面量等信息

常量池是*.class文件中的，当该类被加载，它的常量池信息就会被放入运行时常量池，并把里面的符号地址变为真实地址

#### StringTable（串池）面试题

反编译指令：`javap -v Demo.class`

所有的字符串对象都是懒加载的形式，当用到了此字符串时，string对象才会被创建。

```java
/**
 * @Author: yuluo
 * @CreateTime: 2022-11-15  17:23
 * @Description: TODO
 */

public class StringDemo {

    public static void main(String[] args) {
        String a = "a";
        String b = "b";
        String ab = "ab";
        
        String s1 = "a" + "b";
        String s2 = "ab";
        // 上述s1和s2的两个对象是相对的
    }

}
```

```markdown
PS E:\Java\Java_project\basic\jvm\jvm\target\classes> javap -v .\StringDemo.class
Classfile /E:/Java/Java_project/basic/jvm/jvm/target/classes/StringDemo.class
  Last modified 2022-11-15; size 467 bytes
  MD5 checksum 79b3530c41b2e886562d0f69b5dbda82
  Compiled from "StringDemo.java"
public class StringDemo
  minor version: 0
  major version: 52
  flags: ACC_PUBLIC, ACC_SUPER
Constant pool:
   #1 = Methodref          #6.#24         // java/lang/Object."<init>":()V  // 调用无参构造
   #2 = String             #18            // a
   #3 = String             #20            // b
   #4 = String             #21            // ab
   #5 = Class              #25            // StringDemo
   #6 = Class              #26            // java/lang/Object
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               LocalVariableTable
  #12 = Utf8               this
  #13 = Utf8               LStringDemo;
  #14 = Utf8               main
  #15 = Utf8               ([Ljava/lang/String;)V
  #16 = Utf8               args
  #17 = Utf8               [Ljava/lang/String;
  #18 = Utf8               a
  #19 = Utf8               Ljava/lang/String;
  #20 = Utf8               b
  #21 = Utf8               ab
  #22 = Utf8               SourceFile
  #23 = Utf8               StringDemo.java
  #24 = NameAndType        #7:#8          // "<init>":()V
  #25 = Utf8               StringDemo
  #26 = Utf8               java/lang/Object
{
  public StringDemo();
    descriptor: ()V
    flags: ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 7: 0
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0       5     0  this   LStringDemo;

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: ACC_PUBLIC, ACC_STATIC
    Code:
      stack=1, locals=4, args_size=1
         0: ldc           #2                  // String a
         2: astore_1
         3: ldc           #3                  // String b
         5: astore_2
         6: ldc           #4                  // String ab
        line 13: 9
      LocalVariableTable:
        Start  Length  Slot  Name   Signature
            0      10     0  args   [Ljava/lang/String;
            3       7     1     a   Ljava/lang/String;
            6       4     2     b   Ljava/lang/String;
            9       1     3    ab   Ljava/lang/String;
}
SourceFile: "StringDemo.java"

```

> 串池中已经有的字符串不会再出现，可以避免重复创建相同的字符串
>
> 常量池中的字符串仅是符号，当用到的时候才会被创建为对象
>
> 字符串拼接的原理是StringBuilder（1.8）
>
> 字符串拼接的原理是编译器优化
>
> 可以使用intern()【】将串池中没有的字符串对象放入串池
>
> - （1.8 堆空间）如果有，则不会放入。没有就会放入，并且将串池中的对象返回
> - （1.6 永久代）如果有，则不会放入。没有就会将堆中的此对象复制一份放入串池中，并且将复制后的对象返回，不再是同一个对象。

#### StringTable垃圾回收

> 参数调整：`-Xmx10m -XX:+PrintStringTableStatistics -XX:+PrintGCDetails -verbose:gc
>
> ​		堆内存最大值  打印字符串表的统计信息				打印垃圾回收的详细细心

```java
/**
 * @Author: yuluo
 * @CreateTime: 2022-11-15  21:05
 * @Description: TODO
 */

public class StringTableGC {
    public static void main(String[] args) {
        int i = 0;

        try {
            for (int j = 0; j < i; j++) {
                String.valueOf(j);
                i ++;
            }
        } catch (Throwable e) {

        } finally {
            System.out.println(i);
        }
    }

}
```

#### 性能调优

> 主要是调整HashTable桶的个数
>
> `--XX:StringTableSize=1000`
>
> 调优主要老驴是否有大量重复的字符串产生，比如重复的地址信息等。此时就可以考虑放入串池中避免产生大量的重复浪费系统内存

### 直接内存（Direct Memory）

#### 定义

- 常见于NIO操作，用于数据缓冲区
- 分配回收成本较高，但读写性能高
- 不受JVM内存回手（GC）管理

#### 直接内存释放原理

```java
public import java.io.IOException;
import java.nio.ByteBuffer;

/**
 * @Author: yuluo
 * @CreateTime: 2022-11-16  22:07
 * @Description: TODO
 */

public class DirectMemory {

    static int _1Gb = 1024 * 1024 * 1024;
    
    public static void main(String[] args) throws IOException {
        ByteBuffer byteBuffer = ByteBuffer.allocateDirect(_1Gb);
        System.out.println("分配1GB内存空间完毕！");
        
        // 读入回车继续执行
        System.in.read();

        System.out.println("开始释放！");
        byteBuffer = null;
        
        // 垃圾回收
        System.gc();
        // 回车结束程序执行
        System.in.read();
    }
    
}
```

> 垃圾回收不会管理直接内存
>
> 那么为什么此处的垃圾回收释放了1GB的内存空间呢？

```java
import sun.misc.Unsafe;

import java.io.IOException;
import java.lang.reflect.Field;

/**
 * @Author: yuluo
 * @CreateTime: 2022-11-16  22:12
 * @Description: Unsafe是jvm内部使用的类，不推荐程序员使用
 */

public class DirectMemoryUnsafe {

    static int _1Gb = 1024 * 1024 * 1024;

    public static void main(String[] args) throws IOException {
        
        Unsafe unsafe = getUnSafe();
        
        // 分配内存
        long base = unsafe.allocateMemory(_1Gb);
        unsafe.setMemory(base, _1Gb, (byte) 0);
        
        System.in.read();
        
        // 释放内存
        unsafe.freeMemory(base);
        System.in.read();
    }
    
    public static Unsafe getUnSafe() {
        try {
            Field f = Unsafe.class.getDeclaredField("theUnsafe");
            f.setAccessible(true);
            Unsafe unsafe = (Unsafe) f.get(null);
            return unsafe;
        } catch (NoSuchFieldException | IllegalAccessException e) {
            throw new RuntimeException(e);
        }
    }
    
}
```

> 通过以上代码分析，直接内存的释放必须主动去调用`unsafe`的`freeMemory()`方法去释放(**使用了虚引用的机制**)，垃圾回收不能直接释放直接内存的内存空间。

#### 禁用显式的垃圾回收对内存的影响

> 显式垃圾回收是指：在代码中写的垃圾回收 `System.gc()`
>
> 通过jvm参数 `--XX:+DisableExplicitGC`
>
> 会造成直接内存占用过大的情况

### 垃圾回收概述

#### 如何判断对象可以回收

- 引用计数法

  > 通过计算当前对象被其他对象引用的次数，来判断是否回收当前对象。
  >
  > 当引用次数为0的时候，变为垃圾被回收掉
  >
  > 
  >
  > 弊端：存在循环引用的问题。python曾经采用过此算法

- 可达性分析算法

  > java虚拟机中使用的垃圾回收算法
  >
  > 
  >
  > 扫描堆中的对象，看是否能够沿着GC Root对象为起点的引用链找到该对象，找不到，表示可以回收此对象
  >
  > 
  >
  > 可以作为GC Root的对象：
  >
  > 使用`jps`获取到`java`进程的`id`，使用 `  jmap -dump:format=b,live,file=1.bin 21384`
  >
  > - -dump表示堆内存的运行状态转储成一个文件
  > - format=b:表示存储格式，b表示二进制
  > - live：讲存活的对象打印出来，执行此命令时会触发一次垃圾回收
  > - file=1.bin 将信息存为一个名为1.bin的文件
  > - 21384：java进程id
  >
  > 之后使用Eclipse Memory Analyzer分析保存的堆内存快照文件，获取GC Root对象

- 四种引用

  四种引用强度依次减弱

  > - 强引用（Strongly Reference）
  >
  >   平时使用的引用类型都为强引用类型。new对象的引用，GC Root的引用。**无论何种情况下，只要强引用关系依然存在时，对象就不会被回收**
  >
  > - 软引用（Soft Reference）
  >
  >   是指一些还有用，但非必须的对象。只被引用关联着的对象，在系统将要发生内存溢出前，会回收此引用类型的对象。如果内存还不够，内存溢出
  >
  >   演示软引用 `-Xmx20m -XX:+PrintGCDetails -verbose:gc`
  >
  > - 弱引用（Weak Reference）
  >
  >   也是用来描述那些非必须的对象，强度弱于软引用。当垃圾回收器开始工作时，会回收此种引用的对象。
  >
  > - 虚引用（Phantom Reference）
  >
  >   也被称为幽灵引用或者幻影引用，为一个对象设置虚引用的作用只是在当前对象被回收时收到一个系统通知消息。
  >
  > - 终结器引用（finallize Reference）
  >
  >   java为了使得c和c++程序员像java转型时做出的妥协，使用try catch finally可以做的更好！不推荐使用此方法。

#### 垃圾回收算法

- 标记清除算法（Make Sweep）

  标记和清除

  优点：速度快，记录垃圾对象的内存地址，在后续进行清除

  缺点：容易产生内存碎片，内存空间不连续。

- 标记整理算法（Make Compact）

  标记过程相同

  整理：避免产生内存碎片问题

  优点：避免产生内存碎片

  缺点：移动对象的内存空间时，需要移动对象的变量，内存块等一系列的问题。速度较慢

- 复制（Copy）

  将内存区域划分为两块大小相同的区域，一块from，一块to。to区域始终空闲。先标记，将存活的对象从from复制到to中。这样一来，from中的内存空间都是垃圾，一次全部清空from，在将from和to区域做一个交换。来完成垃圾回收

  优点：不会产生内存碎片

  缺点：占用双倍内存空间

> 三种垃圾回收算法，在jvm中都是存在的，会根据不同的场景选择不同的回收算法。具体实现为jvm中的**分代垃圾回收**

#### 分代垃圾回收

jvm代划分：**划分依据是：根据对象使用的时间长短。新生代：朝生夕死的对象，老年代：有价值的，长时间存活的对象**

```markdown
|--jvm
|----新生代
|------伊甸园（Eden）
|------幸存区（From）
|------幸存区（To）
|----老年代
|------
```

Minor GC：对象诞生的地方。内存空间不足时，进行一次Minor GC。将存活的对象拷贝到幸存区To中，存活下来的对象寿命+1，默认是0，最后交换from和to的位置。第二次Minor GC时，对象寿命再次+1，重复刚才的步骤。当对象的寿命超过一定的阈值（最大阈值寿命：15）时，jvm认定为有价值的对象，不再存放在新生代中。会被晋升到老年代去。

> 当老年代的对象足够多时？是否也会进行GC？

Full GC：当新生代的对象足够多时，老年代的对象也多时，空间不足，会尝试触发一次Full GC。对新生和拉老年都进行清理。

> 注意：minor gc会引发一次`stop the world`，只有垃圾回收线程运行，暂停用户线程。因为牵扯到对象的复制。暂停时间较短

#### 垃圾回收器

- 串行垃圾回收器

  打开串行垃圾回收器：`-XX:+UseSerialGC=Serial + SerialOld`

  单线程垃圾回收器：场景，堆内存较小的时候，适合个人电脑

- 吞吐量优先的垃圾回收器

  `-XX:+UserParallelGC`

  多线程：适合堆内存较大的场景，需要多核cpu的支持，适合服务器

  在单位时间使用的`STW`时间最短

- 响应时间优先的垃圾回收器

  `-XX:UseConcMarkSweepGC`: 基于标记清除的GC

  多线程：适合堆内存较大的场景，需要多核cpu的支持，适合服务器

  尽可能让单词垃圾回收`Stop the World STW`时间变得最短

##### G1 垃圾回收器

定义：Garbage First

- 2004论文发布
- 2009 JDK 6u14 体验
- 2012 JDK 7u4官方支持
- 2017 JDK9 默认

使用场景：

- 同时注重吞吐量（Throughput）和低延迟（Low latency），默认的暂停目标是200ms
- 超大堆内存，会将堆划分为多个大小相等的Region
- 整体上是标记+整理算法，两个区域之间是复制算法

相关jvm参数

- `-XX:+UseG1GC`
- `-XX:G1HeapRegionSize=size`
- `-XX:MaxGCPauseMillis=time`

#### 垃圾回收调优

- 掌握GC相关的VM参数，会基本的空间调整

  查看当前虚拟机的运行参数

  `-XX:+PrintFlagsFinal -version | findstr "GC"`

  ```bat
  C:\Users\14815>java -XX:+PrintFlagsFinal -version | findstr "GC
      uintx AdaptiveSizeMajorGCDecayTimeScale        = 10                                        {product} {default}
       uint ConcGCThreads                            = 3                                         {product} {ergonomic}
       bool DisableExplicitGC                        = false                                     {product} {default}
       bool ExplicitGCInvokesConcurrent              = false                                     {product} {default}
      uintx G1MixedGCCountTarget                     = 8                                         {product} {default}
      uintx G1PeriodicGCInterval                     = 0                                      {manageable} {default}
       bool G1PeriodicGCInvokesConcurrent            = true                                      {product} {default}
     double G1PeriodicGCSystemLoadThreshold          = 0.000000                               {manageable} {default}
       uint GCCardSizeInBytes                        = 512                                       {product} {default}
      uintx GCDrainStackTargetSize                   = 64                                        {product} {ergonomic}
      uintx GCHeapFreeLimit                          = 2                                         {product} {default}
      uintx GCLockerEdenExpansionPercent             = 5                                         {product} {default}
      uintx GCPauseIntervalMillis                    = 201                                       {product} {default}
      uintx GCTimeLimit                              = 98                                        {product} {default}
      uintx GCTimeRatio                              = 12                                        {product} {default}
       bool HeapDumpAfterFullGC                      = false                                  {manageable} {default}
       bool HeapDumpBeforeFullGC                     = false                                  {manageable} {default}
     size_t HeapSizePerGCThread                      = 43620760                                  {product} {default}
      uintx MaxGCMinorPauseMillis                    = 18446744073709551615                      {product} {default}
      uintx MaxGCPauseMillis                         = 200                                       {product} {default}
        int ParGCArrayScanChunk                      = 50                                        {product} {default}
      uintx ParallelGCBufferWastePct                 = 10                                        {product} {default}
       uint ParallelGCThreads                        = 10                                        {product} {default}
       bool PrintGC                                  = false                                     {product} {default}
       bool PrintGCDetails                           = false                                     {product} {default}
       bool ScavengeBeforeFullGC                     = false                                     {product} {default}
       bool UseAdaptiveSizeDecayMajorGCCost          = true                                      {product} {default}
       bool UseAdaptiveSizePolicyWithSystemGC        = false                                     {product} {default}
       bool UseDynamicNumberOfGCThreads              = true                                      {product} {default}
       bool UseG1GC                                  = true                                      {product} {ergonomic}
       bool UseGCOverheadLimit                       = true                                      {product} {default}
       bool UseMaximumCompactionOnSystemGC           = true                                      {product} {default}
       bool UseParallelGC                            = false                                     {product} {default}
       bool UseSerialGC                              = false                                     {product} {default}
       bool UseShenandoahGC                          = false                                     {product} {default}
  java version "18.0.2.1" 2022-08-18
  Java(TM) SE Runtime Environment (build 18.0.2.1+1-1)
  Java HotSpot(TM) 64-Bit Server VM (build 18.0.2.1+1-1, mixed mode, sharing)
       bool UseZGC                                   = false                                     {product} {default}
  ```

- 掌握相关工具

  jconsole jps jmap jvisualVM等

- **调优和应用，环境相关，没有放之四海而皆准的法则！**

##### 调优领域

- 内存
- 锁竞争
- cpu占用
- io

##### 确定目标

- 清楚自己的应用实现的功能，web应用还是科学计算
- 【低延迟】还是【高吞吐量】
- CMS，G1，ZGC

##### 最快的GC是不发生GC

- 查看Full GC前后的内存占用，考虑下面几个问题

  1. 加载的数据是不是太多？

     `resultSet = statement.executeQuery("select * from '大表'")`，这种情况应该使用limit限制`limit n`

     查询出的大量的冗余数据，用户登录时出查询出用户的所有数据，一般是用啥查啥。

  2. 数据表示是否太臃肿

     对象图

     对象大小：Object对象占16个字节，Integer 24, int 4个字节（能用基本类型，避免包装类型，减小对象的大小）

  3. 是否存在内存泄漏

     缓存不要使用static Map，避免内存泄漏。考虑使用redis等第三方缓存使用。

##### 新生代调优

新生代的特点：

- 所有的new操作的内存分配非常廉价
- 死亡对象的回收代价是零
- 大部分对象用过就死亡了
- Minor GC的时间远远低于Full GC

## 类加载和字节码技术

### 字节码指令

#### javap工具

Oracle提供的javap工具用来反编译class文件

```shell
javap -v helloworld.class
```

### 编译期处理



### 类加载阶段



### 类加载处理



