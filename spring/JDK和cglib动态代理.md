# JDK和cglib动态代理

## JDK动态代理开发

```java
package indi.exer.yuluo.jdk;

import indi.exer.yuluo.proxy.User;
import indi.exer.yuluo.proxy.UserService;
import indi.exer.yuluo.proxy.UserServiceImpl;

import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;

/**
 * @author: yuluo
 * @createTime: 2021/11/23 20:53
 * @Description: JDK动态代理分析
 */

public class TestJDKProxy {

    public static void main(String[] args) throws Exception {
        // 创建原始对象
        
        /*
        1. 借用类加载器  TestJDKProxy
                       UserServiceImpl
        2. JDK8.x前

            final UserService userService = new UserServiceImpl();
    	 */
        
        final UserService userService = new UserServiceImpl();

        // jdk创建动态代理
        /*
         三个参数：
            1 classloader

            2 interfaces
                原始对象 所实现的接口

                interface：
                    userService.getClass.getInterfaces();

                类加载器的作用：
                    User --> user
                    User类的Class对象 --> new User() --> user
                    1 通过类加载器把对应的字节码文件加入到JVM
                    2 通过类加载器创建类的Class对象，进而创建这个类的对象

                 如何获得类加载器
                    每一个类的.class文件自动分配与之对应的ClassLoader

            3 invocationHandler
                 中有一个invoke方法
                 作用是 用于书写额外功能，额外功能运行在原始方法执行前，执行后，前后 抛出异常
                 object 原始方法的返回值
                 参数 Proxy 忽略掉 代表的是代理对象
                     Method 额外功能，所增加给那个原始方法
                     Object[] 原始方法的参数
                     Object invoke(Object proxy, Method method, Object[] args) {

                        Object ret = method.invoke(userService, args);

                        return ret;

                     }
         */

        InvocationHandler handler = new InvocationHandler() {
            @Override
            public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {

                // 额外功能的添加
                System.out.println("--------proxy log---------");

                // 原始方法运行
                Object ret = (Object) method.invoke(userService, args);

                return ret;
            }
        };

        UserService userServiceProxy = (UserService) Proxy.newProxyInstance(TestJDKProxy.class.
                getClassLoader(), userService.getClass().getInterfaces(), handler);

        userServiceProxy.login("suns", "123456");
        userServiceProxy.register(new User());

    }
}
```

## CGlib的动态代理

CGlib创建动态代理的原理：父子继承关系创建代理对象，原始类作为父类，代理类作为子类，这样既可以保证2者方法一致，同时在代理类中提供新的实现(额外功能+原始方法)

```java
package indi.exer.yuluo.CGlib;

import indi.exer.yuluo.proxy.User;

/**
 * @author: yuluo
 * @createTime: 2021/11/24 12:10
 * @Description: CGlib动态代理  继承父类  
 */

public class UserService {

    public void login(String name, String passwd) {
        System.out.println(" test UserService.login");
    }

    public void register(User user) {
        System.out.println("test UserService.register");
    }

}
```

```java
package indi.exer.yuluo.CGlib;

import indi.exer.yuluo.proxy.User;
import org.springframework.cglib.proxy.Enhancer;
import org.springframework.cglib.proxy.MethodInterceptor;
import org.springframework.cglib.proxy.MethodProxy;

import java.lang.reflect.Method;

/**
 * @author: yuluo
 * @createTime: 2021/11/24 12:12
 * @Description: 测试类
 */

public class TestCGlib {

    public static void main(String[] args) {
        // 1 创建原始对象
        final UserService userService = new UserService();

        /*
        2 通过CGlib的方式创建动态代理的对象

           Proxy.new ProxyInstance(classloader, interface, invocationHandler)

           Enhancer.setClassLoader();  --> 类加载器
           Enhancer.setSuperClass();  --> 指定父类
           Enhancer.setCallback();  --> MethodInterceptor(cglib) 设定额外功能
          (增强)Enhancer.create() --> 创建代理

         */
        Enhancer enhancer = new Enhancer();

        // 借用类加载器
        enhancer.setClassLoader(TestCGlib.class.getClassLoader());
        // 指定父类
        enhancer.setSuperclass(userService.getClass());

        MethodInterceptor interceptor = new MethodInterceptor() {
            // 内部类实现intercept方法
            // 等同于 InvocationHandler --> invoke
            @Override
            public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {

                // 额外功能的添加
                System.out.println("------cglib log-------");

                Object ret = method.invoke(userService, args);

                return ret;
            }
        };

        enhancer.setCallback(interceptor);

        UserService userServiceProxy = (UserService) enhancer.create();

        userServiceProxy.login("yuluo", "123456");
        userServiceProxy.register(new User());
    }

}

```