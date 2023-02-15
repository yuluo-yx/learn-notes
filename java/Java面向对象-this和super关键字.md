# Java面向对象-this和super关键字以及jvm内存管理

## this关键字

- this关键字最大的作用是让类中的一个方法访问类中的其他方法和实例变量

```java
public class test{
    
    public static void main(String[] argv)  {
        Dog dog = new Dog();
        dog.eat();
    }
    
}

class Dog {
    public void eat() {
        // 狗在吃狗粮之前看到了主人拿过来了狗粮
        this.look();
        // 上述这种方式其实和下面这种方式相同
        Dog dog = new dog();
        dog.look();
        
        // 也可以这样写
        look();
        
        System.out.println("狗正在吃狗粮……")
    }
    
    public void look() {
        system.out.println("狗看到主人拿着狗粮走过来了……")
    }
}
```

> 当this关键字替代该类的实例调用本类中的非静态方法时，它的类型是确定的，只能是Dog类，但是它所代表的对象是不确定的，只有当这个方法被调用时，才能确定。

- 大部分时候，在访问一个方法中的其他方法和成员变量时，加不加this关键字的效果是一样的。
- 对于static修饰的方法和成员变量时，statis的执行时机是在类加载的时候，此时还没有完成非静态代码的执行，所以不能使用this关键字，无法指向合适的对象。因此在java种规定***静态成员不能直接访问非静态成员，需要通过实例化类的方式进行访问***



this局部变量

this局部变量的使用：与普通方法类似，大部分时候，在构造器中使用的成员变量和方法都可以省略this关键字，但是如果构造器中有一个和成员变量同名的局部变量，又必须在构造器中访问这个被覆盖的成员变量，则必须使用this关键字

```java
public class demo {
    
    // 定义一个成员变量并初始化
    private int num = 9;
    
    // 类的构造方法
    public demo() {
        // 定义一个和成员变量同名的局部变量
        int num = 10;
        // 使用this关键字
        this.num = 12;
    }
    
    public static void main(String[] argv) {
        demo d = new demo();
        System.out.println(d.num);   // 输出10
        // 在demo类实例化的时候调用this关键字完成对局部变量的初始化
    }
    
}
```



this关键字作为返回值

```java
public class TestThis {
    
    public int age;
    public TestThis grow() {
        age ++;
        // return this 返回调用这个方法的对象
        return this;
    }
    
    public static void main(String[] argv) {
        
        // 实例化TestThis类
        TestThis t = new TestThis();
        // 调用grow方法
        t.grow();
        // 再次调用grow方法  输出调用grow方法的对象
        System.out.println(t.grow());
        // 输出成员变量的值
        // indi.exer.yuluo.oop.TestThis@14ae5a5
        // 2
        System.out.println(t.age);
        
        // 新建一个对象输出调用grow方法对象和成员变量  	
        // indi.exer.yuluo.oop.TestThis@7f31245a
		// 0
        System.out.println(new TestThis() + "\n" +new TestThis().age);
    }
}


```

## super关键字

***明确两个概念：方法重写：发生在java类之间，***

​								 ***方法重载：在一个java类中有多个同名方法，只要形参列表不同即可***



如果需要在子类方法中调用父类被覆盖的实例方法，则可使用super关键字限定来调用父类中被覆盖的实例方法

```java
public class test3 {

    public static void main(String[] args) {
        Bird bird = new Bird();
        bird.fly();
        bird.run();
        bird.eat();
        System.out.println(bird.name);

        // 向上转型
        Animals birds = new Bird();/
        System.out.println(birds.name);
        // birds不能调用eat方法()
        // birds.eat();
    }

}

class Animal {

    public String name = "jack";
    
    public Animal() {}
    
    public void fly() {
        System.out.println("飞")；
    }
    
    public void run() {
        System.out.println("跑");
    }
    
}

// 继承Animal类
class Bird extends Animal{
    
    // 覆盖Animal方法中的fly()方法
    public void fly() {
        
    	// 调用Animal类中的fly方法
    	super.fly();
   
        // 引用父类中的成员变量
        System.out.println(super.name);
    }
    
    // 对动物类的扩展
    public void eat() {
        System.out.println("捕食");
    } 

}
```

super关键字额总结：

- super关键字只能出现在实例方法和构造方法中
- 语法是：`super.`， `super()`
  - `super.属性名   【访问父类的属性】`
  - `super.方法名(实参)  【访问父类的方法】`
  - `super(实参)  【调用父类的构造方法】`
- super不能使用在静态方法中
- super大部分情况下是可以省略的
- 只能出现在构造方法的第一行，通过当前的构造方法去调用”父类“中的构造方法，先初始化父类型特征
- super不能省略的情况
  - 父类和子类有同名属性，或者有同样的方法
  - 想在子类中访问父类的方法，不能省略`super.`

## jvm内存分配

```md
方法区：类加载器classLoader，将硬盘上的xxx.class文件加载到JVM的时候，会将字节码文件存放到方法区中，也就是说方法区中存储的是代码片段，因为类需要加载，所以方法区中最先有的是数据

栈：在方法被调用的时候，该方法需要的内存空间在栈中会被分配出来，存储方法的局部变量

堆：凡是通过new运算符创建的对象，都存储在堆内存中，new运算符的作用是在堆中开辟出一块空间。堆内存中存储对象，以及对象的实例变量
```

