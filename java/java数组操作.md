# java数组操作

## main方法中的参数数组`String[] args`

```java
package indi.exer.yuluo.collection.arrays;

import java.util.Arrays;

/**
 * @author: yuluo
 * @createTime: 2022/2/3 16:56
 * @File : test1.java
 * @Software : IntelliJ IDEA
 * @Description:
 *
 *          jvm在调用main方法的时候会自动传一个String数组的参数过来   输出为空，长度为0  []
 *          String[] args 是留给用户的。用户可以在控制台上输入参数，这个参数会被自动转换为这个"String[] args"
 *          模拟一个系统，要用户输入相应的参数才能使用系统，则可以输入到String[] args数组中
 *          参数的设置方法在 Edit Configuration -> Build and run -> Program arguments
 */

public class test1 {

    public static void main(String[] args) {
        // main方法的String[] args 参数的作用
        /*System.out.println(Arrays.toString(args));*/

        // 假设用户名是admin，密码是123 要求用户输入正确的密码才能继续使用
        String username = args[0];
        String password = args[1];

        if (username.equals("admin") && password.equals("123")) {
            System.out.println("登陆成功，欢迎 [" + username +"]回来");
            System.out.println("继续使用此系统……");
        } else {
            System.out.println("输入的用户名或密码不正确，请重新输入");
        }

        // 输出args数组的内容和长度
        System.out.println("args数组的长度：" + args.length + "args数组的内容：" + Arrays.toString(args));

    }

}
```

## 数组的扩容和拷贝

```java
package indi.exer.yuluo.collection.arrays;

/**
 * @author: yuluo
 * @createTime: 2022/2/3 20:31
 * @File : test2.java
 * @Software : IntelliJ IDEA
 * @Description: 数组的扩容和拷贝
 *
 *      数组的扩容：数组的扩容要把原先的数据拷贝到新的数组当中，效率底下，在编程过程中不建议使用
 *
 */

public class test2 {

    public static void main(String[] args) {

        // 拷贝源
        int[] src = {1, 2, 3, 4, 5};

        // 拷贝目标
        int[] array = new int[10];

        // 拷贝方法
        /**
         *     public static native void arraycopy(Object src,  int  srcPos,
         *                                         Object dest, int destPos,
         *                                         int length);
         *     底层采用c++实现，有5个参数
         *     分别是 原数组 原数组的起始位置 目标数组 从目标数组的某个位置开始插入 插入元素的长度
         */
        System.arraycopy(src, 1, array, 4, 3);   // 预估输出结果 0 0 0 0 2 3 4 0 0 0

        for (int i = 0; i < array.length; i++) {
            System.out.print(array[i] + "\t");
        }

    }

}
```

## Array的常用方法

使用了apache的commons lang3工具类

```java
package indi.exer.yuluo.collection.arrays;

import com.sun.deploy.util.ArrayUtil;
import org.apache.commons.lang3.ArrayUtils;

import java.util.ArrayList;
import java.util.Arrays;

/**
 * @author: yuluo
 * @createTime: 2022/2/4 14:33
 * @File: test3.java
 * @Software: IntelliJ IDEA
 * @Description: Arrays工具类的常用方法
 */

public class test3 {

    public static void main(String[] args) {

        // 动态初始化
        int[] arrays = new int[10];

        int[] array = {1, 4, 6, 3, 8, 2, 10, 19};
        int[] array1 = {20, 23, 28};

        // sort排序方法
        Arrays.sort(array);

        // Arrays的toString输出数组
        System.out.println(Arrays.toString(array));

        // 根据数组创建Arraylist
        String[] stringArray = {"yuluo", "huakai"};
        ArrayList<String> str = new ArrayList<>(Arrays.asList(stringArray));
        /*for (String s : str) {
            System.out.println("s = " + s);
        }*/
        str.forEach(System.out::println);

        // 检查数组是否包含某个值
        System.out.println(Arrays.asList(stringArray).contains("yuluo"));

        //将字符串数组转换为字符串
        String s = ArrayUtil.arrayToString(stringArray);
        System.out.println(s);

        // 数组合并 借助apache的工具库 commons lang3
        int[] ints = ArrayUtils.addAll(array, array1);
        for (int i = 0; i < ints.length; i++) {
            System.out.print(ints[i] + "\t");
        }

        // 数组元素的反转
        ArrayUtils.reverse(stringArray);
        for (int i = 0; i < stringArray.length; i++) {
            System.out.println("stringArray[i] = " + stringArray[i]);
        }

    }

}
```

