# Typescript

## 介绍

为了弥补js在某方面的不足，解决js的缺点而发明的语言。由微软公司开发

> 在js的基础上构建，是js的超集。
>
> 根据typescript名字，扩展了js，最主要的就是弥补js类型上的缺点。
>
> 可以在任何支持js的编译器上面运行，但是不能被js解析器直接执行，需要做一步编译，编译成为js。
>
> 相比js更加灵活。

ts相比js增加了什么？

1. 类型
2. 支持ES的新特性
3. 添加了ES不具备的新特性，装饰器，泛型……
4. 丰富的选项配置，语言检查等级……

## 开发环境

1. 下载node.js

2. 全局安装typescript

   ```node
   npm i -g typescript
   ```

3. 使用tsc对ts文件进行编译

   ```node
   tsc xxx.ts
   ```

4. 代码

   ```ts
   console.log("Hello TS")
   ```

5. 编译

   ```node
   tsc 01-hello.ts
   ```

   编译完成之后会在源目录下生成一个js文件，文件名和ts文件名相同

   tsc可以编译成为任何版本的js文件，默认为es3

## TS类型声明

```ts
// ts类型

// 声明一个ts变量，类型为number，以后a只能是number类型
let a: number

a = 22;
a = 10;

// 这样在js写允许的，ts就会提供类型检查，会报错
// a="ts"

let b: string

b = "hello"
// b = 23

let flag: boolean = true

let c = false;
// c = 12  会报错，ts编译器会自己推断类型

// 变量类型的作用在变量声明不常用，常用的是在function中
// ts 加入参数类型判断 在返回值声明 尽量写小写类型
function sum(a: number, b: number): number {

    return a + b
}

console.log(sum(123, 456));

// 也可以使用从字面量进行类型声明 e的值不可改变，类似于常量
let e: 10

// 联合类型  可以使用|来连接两个类型
let d: boolean | string
c = true
c = "hello"

// 声明sex的值只能为male和female
let sex: "male" | "female"

// 显式any类型  f可以赋值任意类型
let f: any

// 隐式的any类型
let g

// 表示未知类型的值  any 可以赋值给任意类型的数据，unknown类型的值不能赋值给别的类型的值（实际就是一个类型安全的any），如果想赋值，可以进行类型转换
let h: unknown

// void类型，用于函数返回值 表示没有返回值
function fn(): boolean {
    return true
}

function fn1(): void {
    // return null;
    return undefined
}

// never表示永远不会有返回值  代码报错了就不会有返回值
function fn2(): never {
    throw new Error("报错了")
}
```

**ts中可用的类型**

![image-20220915083747192](D:\information\Typora笔记\TypeScript\Typescript.assets\image-20220915083747192.png)

```ts
// object表示一个js对象
let a: object
a = {}

a = function () {
}

// {}用来指定对象中可以包含那些属性
// 语法：{属性名：属性值}  在属性名后面加上？表示此参数是可选的
let b： {name: string, age?: number}
// 对象值
b = {name: "yuluo", age: 18}

// [propName: string]: any 表示属性名为string的，属性值为any的任意类型参数
let c: {name: string, [propName: string]: any}
let c = {name: 'yuluo', age: 18, gender: "男"}

// 声明d为一个函数，两个参数a b
// 类似于箭头函数，
// 语法：（形参：类型……）：返回值
let d: (a: number, b: number) => number;
d = function (n1, n2): number {
    return n1 + n2
}
        
// 数组
// 表示字符串数据        
let e: string[]

let g: Array<string>
    
/**
*	tuple: 元组就是固定长度的数组
*/
let h [string, string]
// 只能有两个
h = ['hello', 'yuluo']

// enum：枚举 列举所有可能出现的情况
enum Gender {
    Male,
    Female
}        
        
// let i: {name: string, gender: "male" | "female"}        
let i: {name: string, gender: Gender}    
i = {
    name: 'yuluo',
    // 对于可选值，可以直接在类型中声明可选值
    gender: '男' // "male"
    gender: Gender.Male
}
        
// 类型别名
type myType = string
// 以后使用string就可以用myType代替
let j: string === let j: myType
```

> ***如果不把文件当作模块使用的话typescript会认为所有文件里的代码都是在一个作用域里的，所以即使在不同文件也不能申明同名变量***

## TS编译选项

- 可以使用tsc编译所有的ts文件，要在有tsconfig.json配置文件的情况下

- tsc -w：监控所有的ts文件，在更改之后自动编译

- tsconfig.json的作用

  ```json
  {
      // tsconfig.json是ts编译器的配置文件，ts编译器可以根据它的信息来对代码进行编译
      
      /*
          "include" 用来指定那些ts文件需要被编译
          ** 任意文件夹
          * 任意文件
          "exclude" 指定那些ts文件不需要编译
      */
      "include": [
          // 表示编译src任意文件夹的任意文件
          "./src/**/*"
      ],
      "exclude": [
          // hello内的文件不需要被编译，一般不需要编辑，存在默认值
          "./src/hello/**/*"
      ],
      // 定义被继承的配置文件
      // "extends": "./config/base"
  
      // 编译器的配置  推荐使用es6（es2015）
      "compilerOptions": {
          // target：用来指定ts被编译为的es的版本
          "target": "ES2015",
          // 指定要使用的模块的规范
          "module": "ES6",
          // 指定项目中使用的库 一般情况下不需要更改
          "lib": [],
          // 编译完成输出的js文件位置
          "outDir": "./dist",
          // 输出的文件合并为一个文件, 将编译之后的js合并到app.js中
          "outFile": "./dist/app.js",
  
          // 是否对js文件进行编译
          "allowJs": false,
          // 检查js代码格式
          "checkJs": false,
          // 是否移除注释
          "removeComments": false,
  
          // 不生成编译后的js文件
          "noEmit": false,
  
          // 有错误时不生成编译文件
          "noEmitOnError": false,
  
          // 严格模式的总开关 true 以下的配置都为true
          "strict": true,
  
          // 编译后的js文件是否使用严格模式
          "alwaysStrict": false,
  
          // 不允许隐式的any类型
          "noImplicitAny": false,
  
          // 不允许不明确类型的this
          "noImplicitThis": false,
  
          // 严格检查空值
          "strictNullChecks": false
      }
  }
  ```
  

## 面向对象

### 类

#### 定义类

```ts
// class关键字定义类
// 语法
class 类名 {
    // 定义实例属性 new之后才能访问
	属性名: 类型；
	
    // 静态属性通过static关键字定义
    static 属性名: 类型;
    
    // 只读属性定义
    readonly email: string = "2334@163.com"
    
	constr
}

// Person类
class Person{
    // 通过创建实例对象之后访问 可以修改和读取值
    name: string = 'yuluo',
    age: numer = 18,
    
    // 静态属性通过static关键字定义，直接使用类名访问
    static sex: string = "male",
    
    readonly email: string = "2334@163.com"
    
    // static和readonly可以连用，static在前，readonly在后
    
	// 定义方法
    // 如果方法用static修饰，就是静态方法，不用实例对象访问，用的不多
    sqyHello () {
        console.log("hello")
    }
}
const person = new Person()
console.log(person.name, person.age)

// 访问静态属性
console.log(Person.sex)

// 可以访问只能属性，但是不能改成属性值
console.log(person.email)
```

构造函数

```ts
class Dog {
    // 这样写在创建dog对象时，name和age是固定的，不符合现实情况
    // name = "旺财",
    // age = 3,
    
    name: string,
    age: number
    
    // 采用构造函数初始化值
    constructor (name: string, age: number) {
        // 构造函数中的this表示当前对象实例
        // console.log(this)
        this.name = name,
        this.age = age
    }
    
    bark() {
        alert("汪汪汪")
        // this就表示的是当前调用方法的对象
        console.log(this.name)
    }
}

// const dog = new Dog()
const dog1 = new Dog("小黑", 12)
```

#### 继承

```ts
// 继承语法
// Dog子类，Animal父类 子类会继承所有父类的属性和方法
// 抽离出对象的共同行为和属性
class Dog extends Animal {
    
}

// super关键字 父类也叫做超类
class Dog extends Animal {
    sayHello () {
        // 在子类中调用父类的方法和属性
        super.sayHello();
    }
}
```

#### 接口

```ts
// 描述一个对象的类型
type myType = {
    name: string,
    age: number
};

/**
* 接口用来定义一个类结构
* 用来定义一个类中应该包含那些属性和方法，
* 同时接口也可以当成类型声明去使用
* 接口可以同时声明
* 接口中所有的方法都为抽象方法
*/
interface myInterface {
    name: string,
    age: number,
    
    sayHello(): void
}

const obj: myType = {
    name: "yuluo",
    age: 111t
};

const obj: myInterface = {
    name: "yuluo",
    age: 111
};
```

#### 属性的封装

可以属性前添加属性的修饰符

```ts
public
private

类中的属性可以直接设置在构造函数中
class c {
    constructor (public name: string, pulic age: number) {
        
    }
}

const c = new c("xxx", 111)
```

#### 泛型

```ts
// 在定义函数或者类时，如果遇到类型不明确可以使用泛型
function fn<T>(a: T): T {
    return a;
}

// 可以直接调用具有泛型的函数， ts会自动推断数据类型
fn(10)

// 指定泛型类型为string
fn<string>("hello")

// 两个泛型的函数
function fn1<T, K>(a: T, b: K): T {
    console.log(b);
    return a
}

// 希望出现指定的数据类型
interface Inter{
    length: number;
}

// T这个类型必须是Inter的实现类（子类）
function fn2<T extends Inter>(a: T): number {
    return a.length;
}
```





