# java多线程

## 概述

为了提高计算机的处理速度，有了多线程！

### 进程

进程是一个应用程序

### 线程

线程是一个进程中的执行场景，进程中的执行单元

一个进程可以执行多个进程

### 特点

在java语言中

- 线程A和线程B的堆内存和方法区内存共享
- 但是栈内存独立，不共享资源，**一个线程一个栈**
- 进程A和进程B的内存独立不共享

## 对于多线程并发的理解

t1线程执行t1的

t2线程执行t2的

t1和t2线程之间互不影响，是正真的多线程并发

单核的CPU不能做到真正的多线程并发，但是可以做到一种”多线程并发“的感觉，

因为CPU的处理速度太快，给人一种并发的感觉！

## 实现线程方式

1. 编写一个类，直接继承Thread类，重写run方法

   

   ```java
   public class ThreadTest01 {
       public static void main(String[] args) {
   		// 主线程 这里的代码属于主线程，在主栈中运行
           
           // 新建一个分支线程对象
           MyThread thread = new MyThread();
           
           /*
           当编写了多线程方法之后，没有调用start()方法启动的时候，线程类任然只是一个简单的java类，run方法
           只是一个简单的jaav方法。
           */
           thread.run();
           
           // 启动线程
           /*
           start()方法的作用是启动一个分支线程，只要新的栈空间开辟出来了，这段代码的
           任务就结束了，启动成功的线程会自动调用run()方法。run()方法会在分支栈的底部
           */
           thread.start();
           
           // 这里的代码还是运行在主线程中
           for (int i = 0; i < 1000; i ++) {
               System.out.println("主线程--->" + i);
           }
       }
   }
   
   class MyThread extends Thread {
       @Override
       public void run() {
           // 编写程序，这段程序写在分支线程中
           for (int i = 0; i < 1000; i ++) {
               System.out.println("分支线程 --->" + i);
           }
       }
   }
   
   ```

​			**调用start()方法和run()方法的区别：调用run方法不会启动一个新的线程，就是单线程的状态在运行，调用了start方法之后，会自动开启一个新的线程，并发执行！最本质的区别是不能并发执行**



2. 实现线程的第二个方法，编写类继承Runnable类

   

   ```java
   public class ThreadTest02 {
   	public static void main(String[] args) {
           // 创建一个可运行的对象
           MyRunnable r = new MyRunnble();
           // 将可运行的对象封装成一个线程对象
   		Thread t = new Thread(r);
           // 启动线程
           t.start();
           
           // 主线程代码
           ……
       }
   }
   
   class MyRunnable implements Runnable {
   	@OVerride
       public void run() {
           // 分支线程代码
           ……
       }
   }
   ```



> 在以后的开发过程中，使用接口的方法更多，因为实现了一个接口还可以继承其他的类，可扩展性更高

## 匿名内部类的方式

```java
public class ThreadTest03 {
    public static void main(String[] args) {
        
        // 创建线程对象 使用匿名内部类的方式
        // Thread t = new Thread(对象); 没有名字的对象
        Thread t = new Thread(new Runnable() {
            @Override
            public void run() {
                // 分支线程代码
                for (int i = 0; i < 100; i ++) {
                    System.out.println("分支线程-->" + i);
                }
            }
        });
        
        // 启动线程
        t.start();
        
        // 主线程代码
        for (int i = 0; i < 100; i ++) {
             System.out.prinltn("分支线程-->" + i);
        }
    }
}
```

## 线程生命周期

刚new出来的对象属于新建状态，

​		| 调用start() 方法

线程处于就绪状态 (*就绪状态表示当前的线程具有抢夺CPU时间片的权利【执行权】 抢到时间片之后就开始调用run方法标志线程进入运行状态*)

​		| 通过JVM的调度

运行状态

​		| 通过JVM调度  	| 遇到阻塞事件

返回就绪状态		阻塞状态

​		| run结束

线程进入死亡状态

（*返回就绪状态 线程抢到的时间片用完 返回就绪状态 继续抢夺时间片，继续执行*）

（*阻塞状态 当一个线程遇到等待键盘输入的时候 会进入阻塞状态 会放弃之前占有的时间片 会继续回到就绪状态 抢夺时间片*）

## 获取 当前线程对象 线程对象名字  修改当前线程名字

```java
// 创建线程对象
MyThread t = new Mythred();

// 设置线程名字
t.setName("thread name");

// 获取线程名字
String name = t.getName();  （super.getName()）

// 获取当前线程 出现在那获取的就是那个线程名字
thread t = currentThread();
```

## 线程的sleep方法

```java
static void sleep(long millis);
// 让当前线程进入睡眠（阻塞）状态，放弃占有的时间片

/* 可以间隔特定的事件执行相同的代码 */

```

- 唤醒正在睡眠的线程

  ```java
  // 唤醒线程  靠的是java异常的处理机制
  t.interrupt();
  ```

  

## 强行中止线程

```java
// 已过时，不建议使用
// 方法的缺点 容易丢失数据
t.stop();

// 合理的终止一个线程的执行
public class MyThread{
    
    public static void main(String[] argc) {
        
        MyThread t = new MyThread();
        
        Thread r = new Thread(t);
        
        r.start();
        
        // 合理停止线程
        t.run = false;
        
    }
 
}

class MyThread implements Runnable{
    
    // 打上boolean标记
    boolean run = true;
    
    for (int i= 0; i < 10; i ++) {
        if (run) {
        	System.out.println(Thread.currentThread().getName() + "--->" + i);
            try {
                Thread,sleep(1000);
            } catch (InterruptedExpection e) {
                e.printStackTrace();
            } 
        } else {
            // 终止当前线程
            // return 就结束了，可以在这处理没有处理完d
            return;
        }      
    }
    
}
```



## 线程调度方法

和线程调度有关的方法

```java
// 设置线程的优先级
void setPriority(int new Priority) 
    
// 获取线程优先级
/*
优先级等级
	10
	1
	5
*/
int getPriority()
    
// 线程让位方法 暂停当前正在执行的线程对象，并执行其他线程 不是阻塞方法
// 从运行状态转到就绪状态
static void yield()
    
// 合并线程  多线程编程单线程
void join()

class MyThread1 extends Thread {
    
    public void dosome() {
        MyThread2 t = new MyThread2();
        // 当前线程进入阻塞，t线程执行 知道t线程结束，当前线程才能继续执行  
        t.join();
    }
    
} 

class MyThread2 extends Thread{
    
}
```

## 关于多线程安全环境下，数据的安全问题

- 服务器支持多线程并发环境

> **不安全情况**
>
> 1. 多线程并发
> 2. 有共享数据
> 3. 共享数据有修改的行为

- 线程安全问题的解决办法

> 线程排队执行（不能并发）
>
> 称为线程同步机制
>
> *缺点： 会牺牲一部分效率，但是没有解决办法，数据安全第一位*

- 线程同步术语

> 线程同步术语：
>
> ​	异步编程模型：线程t1和t2各自执行各自的，互不干扰（多线程并发 效率高）
>
> ​	同步编程模型：线程t1和线程t2，在线程t1执行的时候，必须等待其完成才能执行下一个线程

## 同步代码块Synchronized

```java
// 线程同步机制的语法
// 以下几个线程是线程排队的，不能并发
// 一个线程把这里的代码全部执行结束之后，另一个线程才能进来
synchronized() {

    // 线程同步代码块
    
}
 
// synchronized后面小括号中的“数据”是相当关键的，这个数据必须是多线程共享的数
// 才能达到多线程排队

/* 括号中的内容
		假设有t1-t5五个线程
		只希望t2， t3， t4排队，t1 和 t5不需要排队，怎么办
		此时，一定要在（）中写t2， t3， t4的共享的对象
		而这个对象不是t1， t5共享的
*/	
```

也可以在实例方法中使用synchroized，但是出现在实例方法上，一定锁的是**this**

- 缺点：synchronized整个方法都需要同步，可能会无故扩大代码同步的范围，拉低代码的执行效率

- 优点：代码量少，节俭了

在静态方法中使用synchronized

- 表示找类锁
- 保证静态变量的安全

## java中三大变量？

实例变量：在堆中

静态变量：在方法区

局部变量：在栈中 

**以上的三大变量中：**

> ​	局部变量永远东不会存在线程安全问题 （选择StringBuilder） 效率高
>
> ​	因为局部变量不共享，（一个线程一个块）在栈中
>
> ​	实例变量在堆中，堆只有一个
>
> ​	静态变量在方法区中，方法区只有一个

堆和方法区都是多线程共享的，所以可能存在线程安全问题

*常量没有线程安全问题*

## 死锁概述

**死锁很难调式，在开发中要特别注意**

在开发中最好不要嵌套使用

```java
public class Main{
    
    public static void main(String[] args) {
		Object o1 = new Object();
    	Object o2 = new Object();
        
        // t1和t2线程共享o1, o2
        Thread t1 = new MyThread(o1, o2);
        Thread t2 = new MyThread(o1, o2);
        
        t1.start();
        t2.start();
        
    }
    
}

class MyThread1 extends thread{
	Object o1;
    Object o2;
    
    public MyThread1(Object o1, Object o2) {
        this.o1 = o1;
        this.o2 = o2;
    }
    
    public void run() {
        // 实现死锁
        synchronized(o1) {
            // 睡眠  一定会出问题
            try {
                Thread.sleep(1000);
            } catch (InterruptException e) {
                e.printStackTrace();
            }
            synchronized(o2){
                
            }
        }
    }

}

class MyThread2 extends thread{
	Object o1;
    Object o2;
    
    public MyThread2(Object o1, Object o2) {
        this.o1 = o1;
        this.o2 = o2;
    }
    
    public void run() {
        // 实现死锁
        synchronized(o2) {
            try {
                Thread.sleep(1000);
            } catch (InterruptException e) {
                e.printStackTrace();
            }
            synchronized(o1){
                
            }
        }
    }

}
```

## 在开发中怎么解决线程的安全问题

- 在开发的过程中不要开始就选择synchronized关键字 会降低用户吞吐量 用户体验差 在不得已的情况下在选择

### 解决办法 1

尽量使用局部变量代替“实例变量和静态变量”

### 解决办法 2 

如果必须是实例变量，那么可以考虑创建多个变量，这样实例变量的内存就不会共享了

### 解决办法3

1，2都不能使用时，只能选择使用synchonized关键字来解决了

## 守护线程概述

java线程的分类：

- 用户线程 主线程main方法是一个用户线程
- 守护线程 【后台线程】 （垃圾回收线程）

守护线程的特点：

​	一般守护线程是一个死循环，所有的用户线程执完了之后 守护线程自动结束

实现方式：

```java
// 守护线程
// 在线程启动之前将线程设置为守护线程即可
t.setDaemon(true);

// 启动线程
t.start()


```

## 计时器

间隔特定的时间执行特定的程序

实现方式：

- sleep方法（最原始的定时器）
- java.util.Timer, 很多高级框架都是用这个来实现定时任务的
- spring框架提供的SpringTask框架

Timer定时器的实现

```java
Timer timer = new Timer();

// Timer timer = new Timer(true) 守护线程的方式

// 指定定时任务
timer.schedule(定时任务，第一次执行时间，间隔多久执行一次)
```

## 实现线程的第三种方式（JDK8的新特性）

这种方式可以获取到线程的返回值

```java
public class ThreadTest{
    
    public static void main(String[] args) {
        // 第一步，创建一个”未来任务类”对象
        FutureTask task = new FutureTask(new Callable() {
            @Override
            public Object call() throws Exception {
                // 线程执行一个任务，执行之后可能会有一个返回结果
                // call()方法就相当于run方法，只不过这个有返回值
                Thread.sleep(1000;
				 int a = 100;
                int b = 100;
                return a + b;
            }
        });
                
        // 创建线程对象
        Thread t = new Thread();
                
        // 启动线程        
		t.start();
		
        // main方法的结束必须等待get方法执行结束，为了拿到另一个线程的返回结果，
        // 可能会导致线程阻塞
        Obejct obj = task.get
    }
    
}
```

## wait和notify方法

不是线程对象的方法，是所有java对象都有的方法

wait(方法作用

```java
Object obj = new Object();
// 让正在obj对象上活动的方法进入无期限睡眠，直到被唤醒
o.wait();
```

notify()方法作用

```java
// 唤醒因为调用wait方法在obj对象对待的线程
obj.notify();
```

