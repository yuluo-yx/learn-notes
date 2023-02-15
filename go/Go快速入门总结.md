# Go快速入门总结

## 常量const与iota

```go
const length int = 10;

// const常量 不允许被修改
```

const 通常用来定义枚举类型

```go
const (
    // 每行的iota都会累加1，第一行的iota的默认值是0
	BEIJING = 10 * iota // iota = 0
    SHANGHAI 			// iota = 1
    SHENZHEN			// iota = 2
)

const {
    // iota = 0, a = iota + 1 = 1, b = iota + 1 = 2
    a, b = iota + 1, iota + 2
    // iota = 1, c = iota + 1 = 2, d = iota + 1 = 3
    c, d
    // iota = 2, e = iota + 1 = 3, f = iota + 1 = 4
    e, f
    
    // iota = 3, g = iota * 2 = 6, h = iota * 3 = 9
    g, h = iota * 2, iota * 3
    // iota = 4， i = iota * 2 = 8, k = iota * 3 = 12
    i, k
}
```

## 函数的多返回值的三种写法

```go
package main

import "fmt"

/*
函数的返回值是int，并且只有一个返回值
*/
func fun1(a string, b int) int {
   fmt.Println("---fun1---")
   fmt.Println(a)
   fmt.Println(b)

   c := 100
   return c
}

/*
返回值是多个，且返回值是匿名
*/
func fun2(a string, b int) (int, int) {
   fmt.Println("---fun2---")
   fmt.Println(a)
   fmt.Println(b)

   return 666, 777
}

/*
返回值是多个，且返回值形参有名字
*/
func fun3(a string, b int) (r1 int, r2 int) {
   fmt.Println("---fun3---")
   fmt.Println(a)
   fmt.Println(b)

   r1 = 1000
   r2 = 2000

   return r1, r2
}

/*
返回值是多个，且返回值形参有名字
*/
func fun4(a string, b int) (r1, r2 int) {
   fmt.Println("---fun4---")
   fmt.Println(a)
   fmt.Println(b)

   r1 = 100
   r2 = 200

   return r1, r2
}

func main() {
   c := fun1("yuluo", 22)
   fmt.Printf("fun1函数的返回值：%v\n", c)

   res1, res2 := fun2("yuluo", 20)
   fmt.Printf("fun2函数的返回值：%v\n", res1)
   fmt.Printf("fun2函数的返回值：%v\n", res2)

   r1, r2 := fun3("yuluo", 20)
   fmt.Printf("fun3函数的返回值：%v\n", r1)
   fmt.Printf("fun3函数的返回值：%v\n", r2)

   r1, r2 = fun4("huakai", 20)
   fmt.Printf("fun4函数的返回值：%v\n", r1)
   fmt.Printf("fun4函数的返回值：%v\n", r2)

}
```

## init方法调用流程与import导包路径

> init函数的执行流程是早于main函数的，如果想在main函数执行前做一些事情，可以在init函数中去完成

```go
package main

import (
   "fmt"
)

/*
常用来初始化一些配置文件等
 */
func init() {
   fmt.Printf("main的init函数被执行了！\n")
}

func main() {
   fmt.Printf("main函数执行了！")
}
```

> import 导包

```go
import (
	"fmt",
    // .表示 导入当前包中的所有方法到当前的包中，直接使用API调用 不要轻易使用。避免方法名重复 无法识别
    . "html/template",
    // _表示 导入并不使用 但是会执行当前包的init方法
    _ "text/template",
    // myhttp 作为"net/http"包的别名
	myhttp "net/http"
)
```

## 指针

>  和C语言类型

**交换数字值**

```go
package main

import "fmt"

func swap(pa *int, pb *int) {
   var temp int
   temp = *pa // temp = main::a
   *pa = *pb  // main::a = main::b
   *pb = temp // main::b = temp
}

func main() {
   var a int = 10
   var b int = 20

   fmt.Println("a = ", a, "b = ", b)

   // swap
   swap(&a, &b)

   fmt.Println("a = ", a, "b = ", b)
}
```

## defer语句调用顺序

表示一个函数在执行之后，结束之前执行的时机

```go
package main

import "fmt"

func main() {

   defer fmt.Printf("main函数执行结束了！")

   fmt.Println("main go 1")
   fmt.Println("main go 2")

}
```

输出

```mark
PS E:\Go\go-project\go_grammer\defer> .\main.exe
main go 1
main go 2
main函数执行结束了！
```

> defer和return
>
> - defer会后执行
> - return先执行

## 数组和动态数组

```go
package main

import "fmt"

/**
打印数组
这里参数必须写整体数组的数据类型 包括数组长度
值传递
*/
func printArray(myArray [4]int) {
   for index, value := range myArray {
      fmt.Println("index = ", index, "value = ", value)
   }
}

/*
上述这种方式，只能是传递固定数组长度的数组，
不符合实际的场景需求，因此需要动态数组
此时是引用传递，不是值传递
*/
func printArrayDymatic(array []int) {
   fmt.Println("动态数组 切片输出值")
   // _ 表示忽略这个值
   for _, value := range array {
      fmt.Println("value = ", value)
   }
}

func main() {

   // 定义固定长度的数组 默认值为0
   var array1 [10]int
   for i := 0; i < 10; i++ {
      array1[i] = i
   }

   // 定义数组的同时赋值
   array2 := [5]int{1, 2, 3, 4, 5}
   fmt.Println(array2)

   // 根据数组赋值的结果动态的申请内存大小  动态数组 切片
   arraydynamic := []int{1, 2, 4, 5, 6}
   fmt.Printf("arrayDymatic type is %T\n", arraydynamic)
   printArrayDymatic(arraydynamic)

   // 普通遍历
   for i := 0; i < len(array1); i++ {
      fmt.Print(array1[i])
   }

   fmt.Println()

   // range循环
   for index, value := range array1 {
      fmt.Println("index = ", index, "value = ", value)
   }

   // 查看数组的数据类型
   fmt.Printf("array1 type = %T\n", array1)

   //fmt.Println(array1)

}
```

## 切片中声明定义方式

```go
package main

import "fmt"

func main() {

   // 声明slice是一个切片，并且初始化，默认值是1，2，3. 长度len是3
   // slice1 := []int{1, 2, 3}

   // 声明slice是一个切片，但是并没有给slice分配空间
   //var slice1 []int

   // 通过make 给slice1开辟3个int的空间
   //slice1 = make([]int, 3)

   // 自己推导结果
   slice1 := make([]int, 3)

   // 追加元素4
   slice1 = append(slice1, 4)

   // 切片的截取

   // 切片容量追加，切片的截取
   fmt.Printf("len 长度 = %d, cap 容量 = %d, slice1 切片 = %v\n", len(slice1), cap(slice1), slice1)

   fmt.Printf("len = %d, slice = %v\n", len(slice1), slice1)

   // 切片截取
   s1 := []int{1, 2, 3}
   fmt.Printf("原本的切片%v\n", s1)

   // 截取
   s2 := s1[0:2] // 相当于从0-1 不包含后一个
   fmt.Println(s2)
}
```

## map的使用

```go
package main

import (
   "fmt"
   _ "fmt"
)

func main() {
   // 声明key是string，value也是string
   var map1 map[string]string

   if map1 == nil {
      fmt.Println("map1是空的")
   }

   // 在使用map之前需要先为map开辟空间
   map1 = make(map[string]string, 10)

   // map1[键] = 值
   map1["one"] = "java"
   map1["two"] = "c++"
   map1["three"] = "python"

   fmt.Println("map1：", map1)

   fmt.Printf("------------------------\n")

   // 第二种声明方式 默认会自己分配一定的容量
   map2 := make(map[string]string)
   map2["one"] = "java"
   map2["two"] = "c++"
   map2["three"] = "python"
   fmt.Println("map2：", map2)

   fmt.Printf("------------------------\n")

   // 第三种 声明的时候就赋值
   map3 := map[string]string{
      "one":   "java",
      "two":   "c++",
      "three": "python",
   }
   fmt.Println("map3：", map3)
}
```

> **map的删除需要使用delete函数**
>
> 遍历
>
> ```go
> for key, value := range map1 {
>     fmt.Println("key = ", key)
>  	fmt.Println("value = ", value)   
> }
> ```

## 面向对象 

### 结构体

```go
package main

import "fmt"

// Book 表示定义一个结构体 将多个简单类型封装成为一个复杂的数据类型
type Book struct {
   title string
   auth  string
   price int
}

// 这里传递的是book的一个副本 原来值不会改变
func changeBook(book Book) {
   book.title = "huakai"
}

// 修改值
func changeBookByRefer(book *Book) {
   book.title = "huakai"
}

func main() {

   //var book Book
   //book.title = "go入门"
   //book.auth = "yuluo"
   //book.price = 20

   book := Book{
      title: "java入门",
      auth:  "yuluo",
      price: 20,
   }
   fmt.Println("原本值：", book)

   changeBook(book)
   fmt.Println("更改之后的值：", book)

   changeBookByRefer(&book)

   fmt.Println("传引用更改之后的值：", book)

}
```

### class

#### 面向对象表示与封装

```go
package main

import "fmt"

/*
如果类名首字母大写，表示其他包也能够访问 否则只能在类的内部访问
同样的，方法首字母大写，表示可以在其他包中访问，否则只能在本类中访问
go语言的封装特性
 */
type Book struct {
   Title string
   Auth  string
   Price int
}

func (this Book) show() {
   fmt.Println("使用show方法输出的 Book 对象", this)
}

func (this *Book) GetAuth() string {
   return this.Auth
}

func (this *Book) SetAuth(newName string) {
   // 这里的this对象是一个副本，并不能真的修改book对象的值
   // 改入传递指针之后才能修改值
   this.Auth = newName
}

func main() {

   // 定义book对象
   book := Book{Title: "java入门", Auth: "yuluo", Price: 100}
   fmt.Println("原本值：", book)

   auth := book.GetAuth()
   fmt.Println("使用get方法", auth)

   book.SetAuth("huakai")
   fmt.Println("调用set方法之后的值：", book)

   book.show()

}
```

#### 面向对象的继承

```go
package main

import "fmt"

// Human 父类
type Human struct {
   Sex  string
   name string
}

func (this *Human) Eat() {
   fmt.Println("Human.Eat()...")
}

func (this *Human) Walk() {
   fmt.Println("Human.walk()...")
}

type Teacher struct {
   Human // 表示继承了Human类中的方法

   // 在Human上扩展出来的工作属性
   work string
}

// Teacher Eat 重定义父类的方法
func (this *Teacher) Eat() {
   fmt.Println("Teacher.Eat()...")
}

func (this *Teacher) Teach() {
   fmt.Println("Teacher.Teach()...")
}

func main() {

   h := Human{"男", "lisi"}

   h.Eat()
   h.Walk()

   // 定义子类对象
   //t := Teacher{h, "教师"}
   t := Teacher{Human{"男", "yuluo"}, "程序员"}
   // 调用子类方法
   t.Eat()
   t.Teach()
   // 调用父类方法
   t.Walk()

}
```

#### 面向对象的多态实现 

```go
package main

import "fmt"

// 本质是一个指针
type Animal interface {
   Sleep()
   GetColor() string // 获取动物的颜色
   GetType() string  // 获取动物的种类
}

// 具体的类
type Cat struct {
   color string // 猫的颜色
}

func (this *Cat) Sleep() {
   fmt.Println("Cat is sleeping……")
}

func (this *Cat) GetColor() string {
   return this.color
}

func (this *Cat) GetType() string {
   return "Cat"
}

// 具体的类
type Dog struct {
   color string // 猫的颜色
}

func (this *Dog) Sleep() {
   fmt.Println("Dog is sleeping……")
}

func (this *Dog) GetColor() string {
   return this.color
}

func (this *Dog) GetType() string {
   return "Cat"
}

func main() {
   // 接口的数据类型， 父类指针
   var animal Animal

   animal = &Cat{"yellow"}

   // 调用的是Cat的Sleep方法
   animal.Sleep()

   animal = &Dog{"black"}
   // 调用的是Dog的Sleep方法 产生多态现象
   animal.Sleep()
}
```

> 多态的基本要素：
>
> - 有一个父类（接口）
> - 有子类（实现了父类全部接口中的全部方法）
> - 父类类型的变量（指针）指向子类的具体数据变量

#### 空接口的含义

interface：`通用的万能类型 类似与java中的Object`

```go
// 表示map的key是string类型，value是go中的任意数据类型
map1 := map[string]interface{}
```



## 反射

### pair

> pair：go中的变量包含type和value这样的键值对，称为`pair`对

```go
package main

import (
   "fmt"
   "os"
)

func main() {
   var str string
   // 实际上的str变量内部包含
   // pair<type:string, value:"yuluo">
   str = "yuluo"
   fmt.Printf("%v\n", str)

   // 不管怎么样，pair都会连续传递
   // pair<type:string, value:"yuluo">
   var allType interface{}
   allType = str
   fmt.Printf("%v\n", allType)

   // 这样就可以用断言找到具体的类型和value
   val, _ := allType.(string)
   fmt.Printf("value：%v\n", val)

   // pair<type:*os.File, value:"/dev/tty"文件描述符>
   tty, err := os.OpenFile("/dev/tty", os.O_RDWR, 0)
   if err != nil {
      fmt.Println("open file error: ", err)
      return
   }
   fmt.Printf("%v\n", tty)
}
```

### 反射用法

```go
package main

import (
   "fmt"
   "reflect"
)

/*
通过反射获取基本类型num的类型和值
*/
func reflectNum(arg interface{}) {
   fmt.Println("类型type : ", reflect.TypeOf(arg))
   fmt.Println("值value : ", reflect.ValueOf(arg))

}

type User struct {
   Id   int
   Name string
   Age  int
}

// Call User 对象方法
func (this User) Call() {
   fmt.Println("user is called ..")
   fmt.Printf("%v\n", this)
}

func DoFiledAndMethod(input interface{}) {
   // 获取input的type
   inputType := reflect.TypeOf(input)
   fmt.Println("inputType is : ", inputType.Name())
   // 获取input的value
   inputValue := reflect.ValueOf(input)
   fmt.Println("inputValue is : ", inputValue)
   // 通过type获取里面的字段
   // 1.通过interface的reflect.Type, 通过Type得到NumField，进行遍历
   // 2.通过每个field，得到数据类型
   // 3.通过filed有一个interface()方法得到对应的inputValue
   for i := 0; i < inputType.NumField(); i++ {
      field := inputType.Field(i)
      value := inputValue.Field(i).Interface()
      fmt.Printf("%s: %v = %v\n", field.Name, field.Type, value)
   }

   fmt.Println("-------------------")

   // 通过type 获取里面的方法，调用
   for i := 0; i < inputType.NumMethod(); i++ {
      m := inputType.Method(i)
      fmt.Printf("%s: %v\n", m.Name, m.Type)
   }
}

func main() {
   var num float64 = 1.2345
   reflectNum(num)

   fmt.Println("----------------------------------")

   user := User{1, "yuluo", 20}

   /*
      Call 反射复杂数据类型
   */
   DoFiledAndMethod(user)
}
```

结果：

```mark
类型type :  float64
值value :  1.2345
----------------------------------
inputType is :  User
inputValue is :  {1 yuluo 20}
Id: int = 1
Name: string = yuluo
Age: int = 20
-------------------
Call: func(main.User)
```

### 反射解析结构体中标签Tag

**主要作用是解释说明，在不同的包中有不同的作用**

```go
type User struct {
    Id   int		`info:id doc:"myId"`
    Name string		`info:Name`
    Age  int
}
```

### 在json中的应用

在将下列结构体数据转化为json数据时，json字符串的名字

```go
package main

import (
	"encoding/json"
	"fmt"
)

type Movie struct {
	Title  string   `json:"title"`
	Year   int      `json:"year"`
	Price  int      `json:"rmb"`
	Actors []string `json:"actors"`
}

func main() {
	movie := Movie{"功夫", 2008, 100, []string{"李连杰", "周星驰"}}

	// 编码过程 结构体 ——> json
	jsonStr, err := json.Marshal(movie)
	if err != nil {
		fmt.Printf("To json string failed error: %v\n", jsonStr)
		return
	}
	fmt.Printf("json string is: %s\n", jsonStr)

	// 将json转为结构体
	myMovie := Movie{}
	err = json.Unmarshal(jsonStr, &myMovie)
	if err != nil {
		fmt.Printf("json string to struct failed error: %v\n", err)
	}
	fmt.Printf("struct is: %v\n", myMovie)

}
```

结果：

```mark	
json string is: {"title":"功夫","year":2008,"rmb":100,"actors":["李连杰","周星驰"]}
struct is: {功夫 2008 100 [李连杰 周星驰]}
```

## goroutine

### 背景分析

单进程时代存在的两个问题

- 单一执行流程、计算机只能一个任务一个任务处理
- 进程阻塞所带来的cpu浪费时间

能不能在宏观上多个任务同时执行？

- CPU调度（时间片轮转算法等）
- 给人一种同时执行的感觉（并发执行）

### 协程

协程不是系统级线程，很多时候协程被称为“轻量级线程”、“微线程”、“纤程(fiber)”等。简单来说可以认为协程是线程里不同的函数，这些函数之间可以相互**快速**切换

协程和用户态线程非常接近，用户态线程之间的切换不需要陷入内核，但部分操作系统中用户态线程的切换需要内核态线程的辅助

协程是编程语言（或者 lib）提供的特性（协程之间的切换方式与过程可以由编程人员确定），是用户态操作。协程适用于 IO 密集型的任务。常见提供原生协程支持的语言有：c++20、golang、python 等，其他语言以库的形式提供协程功能，比如 C++20 之前腾讯的 fiber 和 libco 等等

### 例子

```go
package main

import (
   "fmt"
   "time"
)

// 子 routine
func newTask() {
   i := 0
   for {
      i++
      fmt.Printf("new Groutine : i = %d\n", i)
   }
}

// 主 goroutine
func main() {
   // 创建一个goroutine 取执行newTask进程
   go newTask()

   i := 0
   for {
      i++
      fmt.Printf("main goroutine : i = %d\n", i)
      time.Sleep(time.Duration(i))
   }
}
```

```go
func main() {

   // 用go创建承载一个形参为空，返回值为空的一个函数
   go func() {
      defer fmt.Println("A.defer")

      func() {
         defer fmt.Println("B.defer")

         // 退出当前的两个函数
         runtime.Goexit()

         fmt.Println("B")
         // （）表示在创建了一个匿名函数之后，调用此匿名函数
      }()

      fmt.Println("A")
   }()

   // go一个有参数的函数
   go func(a int, b int) {
      fmt.Println(a, b)
      // 调用此匿名函数并传参
   }(1, 2)

   // 死循环
   for {
      time.Sleep(1 * time.Second)
   }

}
```

### channel

go routine之间两个线程通信的方式

```go
package main

import "fmt"

func main() {

   // 定义一个channel  channel同时也会保证go routine的执行顺序
   c := make(chan int)

   // 创建两个go routine
   go func() {
      defer fmt.Println("go routine 结束")
      fmt.Println("go routine 正在运行……")

      // 给chan中存值 将666发送给c
      c <- 666
   }()

   // 从C中取出值 并赋值给num
   num := <-c

   fmt.Println("channel中的值：", num)
   fmt.Println("main routine 结束")

}
```

如果chann中没有值的话，线程会一直阻塞，直到有值之后，在拿出来值。

保证了go routine的执行顺序

> channel的有缓冲和无缓冲问题
>
> - 当channel已经满了，在向里面写值，就会阻塞
> - 如果channel为空，从里面取值，也会阻塞
>
> channel和range混合使用
>
> ```go
> for data := range c {
>     fmt.Println(data)
> }
> ```
>
> channer和select混合使用
>
> - 单流程下一个go只能监控一个channel的执行状态，select可以完成监控多个channel的运行状态
>
> ```go
> select {
>     case <- chan1:
>     	// 如果chan1成功读到数据，则进行该操作
>     case chan2 <- 1:
>     	// 如果chan2写入数据成功，则进行该操作
>     default:
>     	// 如果上面都没成功，执行此操作
> }
> ```
>

## GOPATH工作模式的弊端

- 无版本控制的概念

  无法直到自己使用的依赖版本等

- 无法同步一致第三方库

- 无法指定当前项目引用的依赖版本

## GoModules

### 常用命令

| 命令            | 作用                           |
| --------------- | ------------------------------ |
| go mod int      | 生成go.mod文件                 |
| go mod download | 下载go.mod文件中指明的所有依赖 |
| go mod  tidy    | 整理现有的依赖                 |
| go mod  graph   | 查看现有的依赖结构             |
| go mod edit     | 编辑go.mod文件                 |
| go mod vendor   | 导出项目所有的依赖到vendor目录 |
| go mod verify   | 检验一个模块是否被篡改过       |
| go mod why      | 查看为什么需要依赖某模块       |

### Go111MODULE

Go语言提供了GO111MOULE这个环境变量来作为Go module的开关，允许设置以下值

- auto：只要项目包含这个go.mod文件的话才启用了Go Modules，目前在Go1.11 至Go1.14中任然是默认值
- on：启用Go Modules，推荐设置，将会是未来版本中的默认值
- off：禁用Go Module，不推荐设置

`go env -w GO111MODULE=on`

```ma
C:\Users\14815>go env
set GO111MODULE=on
set GOARCH=amd64
set GOBIN=
set GOCACHE=C:\Users\14815\AppData\Local\go-build
set GOENV=C:\Users\14815\AppData\Roaming\go\env
set GOEXE=.exe
```

## GOPROXY

这个环境变量主要是用户设置Go模块代理，作用是用于使GO在后续拉取模块版本时直接通过镜像站点快速拉取。

默认值是：https://proxy.golang.org,direct

默认的代理国内访问不了，需要设置国内的代理

- 阿里云

  ​	https://mirrors.aliyun.com/goproxy

- 七牛云

  https://goproxy.cn,direct

> direct: 如果在改镜像找不到这个包，会去原本的源拉取

### GOSUMDB

全称是：go checksum database

检测代码是否被篡改过，若被篡改过，会立即终止

## 使用go module创建项目

1. 确保go111modules的开关是on `go env -w GO111MODULE=on`

2. go mod init + project_name 

   `project_name 决定今后别人在导你的包时怎么写`

3.  会多出来一个go.mod文件, 文件内容如下

   ```go
   module github.com/yuluo/project_demo
   
   go 1.17
   ```

4. 创建main.go文件

   `touch main.go`

   文件内容

   ```go
   package main
   
   import (
      "github.com/gin-gonic/gin"
      "net/http"
   )
   
   func main() {
      r := gin.Default()
      r.GET("/index", func(c *gin.Context) {
         c.JSON(http.StatusOK, gin.H{
            "msg": "yuluo",
         })
      })
   }
   ```

5. go.mod文件

   ```go
   module github.com/yuluo/project_demo
   
   go 1.17
   
   require github.com/gin-gonic/gin v1.8.1
   
   // 下面是当前项目用到的依赖
   // indirect表示的是间接依赖
   require (
   	github.com/gin-contrib/sse v0.1.0 // indirect
   	github.com/go-playground/locales v0.14.0 // indirect
   	github.com/go-playground/universal-translator v0.18.0 // indirect
   	github.com/go-playground/validator/v10 v10.10.0 // indirect
   	github.com/goccy/go-json v0.9.7 // indirect
   	github.com/json-iterator/go v1.1.12 // indirect
   	github.com/leodido/go-urn v1.2.1 // indirect
   	github.com/mattn/go-isatty v0.0.14 // indirect
   	github.com/modern-go/concurrent v0.0.0-20180228061459-e0a39a4cb421 // indirect
   	github.com/modern-go/reflect2 v1.0.2 // indirect
   	github.com/pelletier/go-toml/v2 v2.0.1 // indirect
   	github.com/ugorji/go/codec v1.2.7 // indirect
   	golang.org/x/crypto v0.0.0-20210711020723-a769d52b0f97 // indirect
   	golang.org/x/net v0.0.0-20210226172049-e18ecbb05110 // indirect
   	golang.org/x/sys v0.0.0-20210806184541-e5e7981a1069 // indirect
   	golang.org/x/text v0.3.6 // indirect
   	google.golang.org/protobuf v1.28.0 // indirect
   	gopkg.in/yaml.v2 v2.4.0 // indirect
   )
   ```

6. gosum文件是当前项目的模块名和版本号信息

   `模块名 版本号 h1 哈希值判断文件是否被更改过`

   ```mar
   github.com/creack/pty v1.1.9/go.mod h1:oKZEueFk5CKHvIhNR5MUki03XCEU+Q6VDXinZuGJ33E=
   github.com/davecgh/go-spew v1.1.0/go.mod h1:J7Y8YcW2NihsgmVo/mv3lAwl/skON4iLHjSsI+c5H38=
   github.com/davecgh/go-spew v1.1.1 h1:vj9j/u1bqnvCEfJOwUhtlOARqs3+rkHYY13jYWTU97c=
   github.com/davecgh/go-spew v1.1.1/go.mod h1:J7Y8YcW2NihsgmVo/mv3lAwl/skON4iLHjSsI+c5H38=
   github.com/gin-contrib/sse v0.1.0 h1:Y/yl/+YNO8GZSjAhjMsSuLt29uWRFHdHYUb5lYOV9qE=
   github.com/gin-contrib/sse v0.1.0/go.mod h1:RHrZQHXnP2xjPF+u1gW/2HnVO7nvIa9PG3Gm+fLHvGI=
   github.com/gin-gonic/gin v1.8.1 h1:4+fr/el88TOO3ewCmQr8cx/CtZ/umlIRIs5M4NTNjf8=
   github.com/gin-gonic/gin v1.8.1/go.mod h1:ji8BvRH1azfM+SYow9zQ6SZMvR8qOMZHmsCuWR9tTTk=
   github.com/go-playground/assert/v2 v2.0.1 h1:MsBgLAaY856+nPRTKrp3/OZK38U/wa0CcBYNjji3q3A=
   github.com/go-playground/assert/v2 v2.0.1/go.mod h1:VDjEfimB/XKnb+ZQfWdccd7VUvScMdVu0Titje2rxJ4=
   github.com/go-playground/locales v0.14.0 h1:u50s323jtVGugKlcYeyzC0etD1HifMjqmJqb8WugfUU=
   github.com/go-playground/locales v0.14.0/go.mod h1:sawfccIbzZTqEDETgFXqTho0QybSa7l++s0DH+LDiLs=
   github.com/go-playground/universal-translator v0.18.0 h1:82dyy6p4OuJq4/CByFNOn/jYrnRPArHwAcmLoJZxyho=
   github.com/go-playground/universal-translator v0.18.0/go.mod h1:UvRDBj+xPUEGrFYl+lu/H90nyDXpg0fqeB/AQUGNTVA=
   github.com/go-playground/validator/v10 v10.10.0 h1:I7mrTYv78z8k8VXa/qJlOlEXn/nBh+BF8dHX5nt/dr0=
   github.com/go-playground/validator/v10 v10.10.0/go.mod h1:74x4gJWsvQexRdW8Pn3dXSGrTK4nAUsbPlLADvpJkos=
   github.com/goccy/go-json v0.9.7 h1:IcB+Aqpx/iMHu5Yooh7jEzJk1JZ7Pjtmys2ukPr7EeM=
   github.com/goccy/go-json v0.9.7/go.mod h1:6MelG93GURQebXPDq3khkgXZkazVtN9CRI+MGFi0w8I=
   github.com/golang/protobuf v1.5.0/go.mod h1:FsONVRAS9T7sI+LIUmWTfcYkHO4aIWwzhcaSAoJOfIk=
   github.com/google/go-cmp v0.5.5 h1:Khx7svrCpmxxtHBq5j2mp/xVjsi8hQMfNLvJFAlrGgU=
   github.com/google/go-cmp v0.5.5/go.mod h1:v8dTdLbMG2kIc/vJvl+f65V22dbkXbowE6jgT/gNBxE=
   github.com/google/gofuzz v1.0.0/go.mod h1:dBl0BpW6vV/+mYPU4Po3pmUjxk6FQPldtuIdl/M65Eg=
   github.com/json-iterator/go v1.1.12 h1:PV8peI4a0ysnczrg+LtxykD8LfKY9ML6u2jnxaEnrnM=
   github.com/json-iterator/go v1.1.12/go.mod h1:e30LSqwooZae/UwlEbR2852Gd8hjQvJoHmT4TnhNGBo=
   github.com/kr/pretty v0.1.0/go.mod h1:dAy3ld7l9f0ibDNOQOHHMYYIIbhfbHSm3C4ZsoJORNo=
   github.com/kr/pretty v0.2.1/go.mod h1:ipq/a2n7PKx3OHsz4KJII5eveXtPO4qwEXGdVfWzfnI=
   github.com/kr/pretty v0.3.0 h1:WgNl7dwNpEZ6jJ9k1snq4pZsg7DOEN8hP9Xw0Tsjwk0=
   github.com/kr/pretty v0.3.0/go.mod h1:640gp4NfQd8pI5XOwp5fnNeVWj67G7CFk/SaSQn7NBk=
   github.com/kr/pty v1.1.1/go.mod h1:pFQYn66WHrOpPYNljwOMqo10TkYh1fy3cYio2l3bCsQ=
   github.com/kr/text v0.1.0/go.mod h1:4Jbv+DJW3UT/LiOwJeYQe1efqtUx/iVham/4vfdArNI=
   github.com/kr/text v0.2.0 h1:5Nx0Ya0ZqY2ygV366QzturHI13Jq95ApcVaJBhpS+AY=
   github.com/kr/text v0.2.0/go.mod h1:eLer722TekiGuMkidMxC/pM04lWEeraHUUmBw8l2grE=
   github.com/leodido/go-urn v1.2.1 h1:BqpAaACuzVSgi/VLzGZIobT2z4v53pjosyNd9Yv6n/w=
   github.com/leodido/go-urn v1.2.1/go.mod h1:zt4jvISO2HfUBqxjfIshjdMTYS56ZS/qv49ictyFfxY=
   github.com/mattn/go-isatty v0.0.14 h1:yVuAays6BHfxijgZPzw+3Zlu5yQgKGP2/hcQbHb7S9Y=
   github.com/mattn/go-isatty v0.0.14/go.mod h1:7GGIvUiUoEMVVmxf/4nioHXj79iQHKdU27kJ6hsGG94=
   github.com/modern-go/concurrent v0.0.0-20180228061459-e0a39a4cb421 h1:ZqeYNhU3OHLH3mGKHDcjJRFFRrJa6eAM5H+CtDdOsPc=
   github.com/modern-go/concurrent v0.0.0-20180228061459-e0a39a4cb421/go.mod h1:6dJC0mAP4ikYIbvyc7fijjWJddQyLn8Ig3JB5CqoB9Q=
   github.com/modern-go/reflect2 v1.0.2 h1:xBagoLtFs94CBntxluKeaWgTMpvLxC4ur3nMaC9Gz0M=
   github.com/modern-go/reflect2 v1.0.2/go.mod h1:yWuevngMOJpCy52FWWMvUC8ws7m/LJsjYzDa0/r8luk=
   github.com/pelletier/go-toml/v2 v2.0.1 h1:8e3L2cCQzLFi2CR4g7vGFuFxX7Jl1kKX8gW+iV0GUKU=
   github.com/pelletier/go-toml/v2 v2.0.1/go.mod h1:r9LEWfGN8R5k0VXJ+0BkIe7MYkRdwZOjgMj2KwnJFUo=
   github.com/pkg/diff v0.0.0-20210226163009-20ebb0f2a09e/go.mod h1:pJLUxLENpZxwdsKMEsNbx1VGcRFpLqf3715MtcvvzbA=
   github.com/pmezard/go-difflib v1.0.0 h1:4DBwDE0NGyQoBHbLQYPwSUPoCMWR5BEzIk/f1lZbAQM=
   github.com/pmezard/go-difflib v1.0.0/go.mod h1:iKH77koFhYxTK1pcRnkKkqfTogsbg7gZNVY4sRDYZ/4=
   github.com/rogpeppe/go-internal v1.6.1/go.mod h1:xXDCJY+GAPziupqXw64V24skbSoqbTEfhy4qGm1nDQc=
   github.com/rogpeppe/go-internal v1.8.0 h1:FCbCCtXNOY3UtUuHUYaghJg4y7Fd14rXifAYUAtL9R8=
   github.com/rogpeppe/go-internal v1.8.0/go.mod h1:WmiCO8CzOY8rg0OYDC4/i/2WRWAB6poM+XZ2dLUbcbE=
   github.com/stretchr/objx v0.1.0/go.mod h1:HFkY916IF+rwdDfMAkV7OtwuqBVzrE8GR6GFx+wExME=
   github.com/stretchr/testify v1.3.0/go.mod h1:M5WIy9Dh21IEIfnGCwXGc5bZfKNJtfHm1UVUgZn+9EI=
   github.com/stretchr/testify v1.6.1/go.mod h1:6Fq8oRcR53rry900zMqJjRRixrwX3KX962/h/Wwjteg=
   github.com/stretchr/testify v1.7.0/go.mod h1:6Fq8oRcR53rry900zMqJjRRixrwX3KX962/h/Wwjteg=
   github.com/stretchr/testify v1.7.1 h1:5TQK59W5E3v0r2duFAb7P95B6hEeOyEnHRa8MjYSMTY=
   github.com/stretchr/testify v1.7.1/go.mod h1:6Fq8oRcR53rry900zMqJjRRixrwX3KX962/h/Wwjteg=
   github.com/ugorji/go v1.2.7 h1:qYhyWUUd6WbiM+C6JZAUkIJt/1WrjzNHY9+KCIjVqTo=
   github.com/ugorji/go v1.2.7/go.mod h1:nF9osbDWLy6bDVv/Rtoh6QgnvNDpmCalQV5urGCCS6M=
   github.com/ugorji/go/codec v1.2.7 h1:YPXUKf7fYbp/y8xloBqZOw2qaVggbfwMlI8WM3wZUJ0=
   github.com/ugorji/go/codec v1.2.7/go.mod h1:WGN1fab3R1fzQlVQTkfxVtIBhWDRqOviHU95kRgeqEY=
   golang.org/x/crypto v0.0.0-20210711020723-a769d52b0f97 h1:/UOmuWzQfxxo9UtlXMwuQU8CMgg1eZXqTRwkSQJWKOI=
   golang.org/x/crypto v0.0.0-20210711020723-a769d52b0f97/go.mod h1:GvvjBRRGRdwPK5ydBHafDWAxML/pGHZbMvKqRZ5+Abc=
   golang.org/x/net v0.0.0-20210226172049-e18ecbb05110 h1:qWPm9rbaAMKs8Bq/9LRpbMqxWRVUAQwMI9fVrssnTfw=
   golang.org/x/net v0.0.0-20210226172049-e18ecbb05110/go.mod h1:m0MpNAwzfU5UDzcl9v0D8zg8gWTRqZa9RBIspLL5mdg=
   golang.org/x/sys v0.0.0-20201119102817-f84b799fce68/go.mod h1:h1NjWce9XRLGQEsW7wpKNCjG9DtNlClVuFLEZdDNbEs=
   golang.org/x/sys v0.0.0-20210615035016-665e8c7367d1/go.mod h1:oPkhp1MJrh7nUepCBck5+mAzfO9JrbApNNgaTdGDITg=
   golang.org/x/sys v0.0.0-20210630005230-0f9fa26af87c/go.mod h1:oPkhp1MJrh7nUepCBck5+mAzfO9JrbApNNgaTdGDITg=
   golang.org/x/sys v0.0.0-20210806184541-e5e7981a1069 h1:siQdpVirKtzPhKl3lZWozZraCFObP8S1v6PRp0bLrtU=
   golang.org/x/sys v0.0.0-20210806184541-e5e7981a1069/go.mod h1:oPkhp1MJrh7nUepCBck5+mAzfO9JrbApNNgaTdGDITg=
   golang.org/x/term v0.0.0-20201126162022-7de9c90e9dd1/go.mod h1:bj7SfCRtBDWHUb9snDiAeCFNEtKQo2Wmx5Cou7ajbmo=
   golang.org/x/text v0.3.3/go.mod h1:5Zoc/QRtKVWzQhOtBMvqHzDpF6irO9z98xDceosuGiQ=
   golang.org/x/text v0.3.6 h1:aRYxNxv6iGQlyVaZmk6ZgYEDa+Jg18DxebPSrd6bg1M=
   golang.org/x/text v0.3.6/go.mod h1:5Zoc/QRtKVWzQhOtBMvqHzDpF6irO9z98xDceosuGiQ=
   golang.org/x/tools v0.0.0-20180917221912-90fa682c2a6e/go.mod h1:n7NCudcB/nEzxVGmLbDWY5pfWTLqBcC2KZ6jyYvM4mQ=
   golang.org/x/xerrors v0.0.0-20191204190536-9bdfabe68543 h1:E7g+9GITq07hpfrRu66IVDexMakfv52eLZ2CXBWiKr4=
   golang.org/x/xerrors v0.0.0-20191204190536-9bdfabe68543/go.mod h1:I/5z698sn9Ka8TeJc9MKroUUfqBBauWjQqLJ2OPfmY0=
   google.golang.org/protobuf v1.26.0-rc.1/go.mod h1:jlhhOSvTdKEhbULTjvd4ARK9grFBp09yW+WbY/TyQbw=
   google.golang.org/protobuf v1.28.0 h1:w43yiav+6bVFTBQFZX0r7ipe9JQ1QsbMgHwbBziscLw=
   google.golang.org/protobuf v1.28.0/go.mod h1:HV8QOd/L58Z+nl8r43ehVNZIU/HEI6OcFqwMG9pJV4I=
   gopkg.in/check.v1 v0.0.0-20161208181325-20d25e280405/go.mod h1:Co6ibVJAznAaIkqp8huTwlJQCZ016jof/cbN4VW5Yz0=
   gopkg.in/check.v1 v1.0.0-20180628173108-788fd7840127/go.mod h1:Co6ibVJAznAaIkqp8huTwlJQCZ016jof/cbN4VW5Yz0=
   gopkg.in/check.v1 v1.0.0-20201130134442-10cb98267c6c h1:Hei/4ADfdWqJk1ZMxUNpqntNwaWcugrBjAiHlqqRiVk=
   gopkg.in/check.v1 v1.0.0-20201130134442-10cb98267c6c/go.mod h1:JHkPIbrfpd72SG/EVd6muEfDQjcINNoR0C8j2r3qZ4Q=
   gopkg.in/errgo.v2 v2.1.0/go.mod h1:hNsd1EY+bozCKY1Ytp96fpM3vjJbqLJn88ws8XvfDNI=
   gopkg.in/yaml.v2 v2.4.0 h1:D8xgwECY7CYvx+Y2n4sBz93Jn9JRvxdiyyo8CTfuKaY=
   gopkg.in/yaml.v2 v2.4.0/go.mod h1:RDklbk79AGWmwhnvt/jBztapEOGDOx6ZbXqjP6csGnQ=
   gopkg.in/yaml.v3 v3.0.0-20200313102051-9f266ea9e77c/go.mod h1:K4uyk7z7BCEPqu6E+C64Yfv1cQ7kz7rIZviUmN+EgEM=
   gopkg.in/yaml.v3 v3.0.0-20210107192922-496545a6307b h1:h8qDotaEPuJATrMmW04NCwg7v22aHH28wwpauUhK9Oo=
   gopkg.in/yaml.v3 v3.0.0-20210107192922-496545a6307b/go.mod h1:K4uyk7z7BCEPqu6E+C64Yfv1cQ7kz7rIZviUmN+EgEM=
   
   ```

7. 使用 go modules创建项目成功！

## 完结!

