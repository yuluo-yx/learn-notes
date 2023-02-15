# Go Web开发

## Go web demo示例

> 使用net/http标准库完成

```go
package main

import (
   "fmt"
   "net/http"
)

/*
ResponseWriter 给浏览器的反馈信息
Request 响应
*/
func sayHello(w http.ResponseWriter, r *http.Request) {
   // 表示接受返回值，对返回值不做任何处理
   _, _ = fmt.Fprintln(w, "Hello Go web!")
}

func main() {
   // 请求处理
   http.HandleFunc("/hello", sayHello)

   // 启动服务之后，监听端口
   err := http.ListenAndServe(":9090", nil)
   // 错误处理
   if err != nil {
      fmt.Printf("http server failed, err:%v\n", err)
   }
}
```

> 访问出现，Hello Go web!字样

## Gin框架初始

### 介绍

Gin是一个用Go语言编写的web框架，是一个类似于martini的，但拥有更好性能的API框架，由于使用httprouter，速度提高了近40倍。如果你是性能和高效的追求者。推荐使用Gin

### 安装

```mark
go get -u github.com/gin-gonic/gin
```

> 如果安装速度太慢的话，需要配置go proxy
>
> ```mark
> go env -w GOPROXY=https://goproxy.cn,direct
> ```
>
> 重新启动golang即可

### 开始示例

```go
package main

import (
   "github.com/gin-gonic/gin"
)

func sayHello(c *gin.Context) {
   // 返回一个json字符串
   c.JSON(200, gin.H{
      "message": "Hello Go Web by yuluo!",
   })
}

func main() {
   // 返回默认的路由引擎
   r := gin.Default()
   // 用户访问此路径，执行sayHello函数  并且指定用户使用GET请求访问/hello 路径
   r.GET("/hello", sayHello)

   // 运行
   err := r.Run()
   if err != nil {
      return
   }
}
```

### 注意

> 如果出现依赖找不到的情况
>
> ***需要在ide的settings中的go modules设置Environment设置https://goproxy.io 将依赖交给go mod进行管理***

## RESTful API

用不同的http请求代表不动的动作

| 请求方式 | 动作 |
| -------- | ---- |
| get      | 查询 |
| post     | 添加 |
| put      | 修改 |
| delete   | 删除 |

## template模板

### HTML渲染

> Go http/template用于在go中输出HTML的场景，可以防止代码注入

### Go语言模板引擎

- 模板文件通常定义为.tmp或者.tpl为后缀（也可以使用其他的后缀名）必须使用UTF-8编码
- 模板文件使用`{{}}`包裹和表示需要传入的数据
- 传给模板这样的数据可以通过`.`号来访问，如果数据是复杂性的数据，可以通过`{{.FiledName}}`来访问它的字段
- 除`{{}}`外，其他内容均不做修改输出

### 模板引擎使用

- 定义模板文件

  > 按照相关的语法规则去编写

- 解析模板文件

  > 使用下面这种方式去解析模板文件，得到模板对象
  >
  > ```go
  > func (t *Template) Parse(stc string) (*Template error)
  > func ParseFiles(filenames ...string) (*Template error)
  > func ParseGlob(pattern string) (*Template error)
  > ```
  >
  > 也可以使用 `func New(name string) *Template`函数创建一个名为name的模板，然后对其调用上面的方法去解析模板字符串或者模板文件

- 模板渲染

  > 模板渲染就是使用数据填充模板，实际上可能会复杂很多
  >
  > ```go
  > func (t *Template) Execute(er io.Writer, data interface{} error)
  > func (t *Template) ExecuteTemplate(er io.Writer, name string, data interface{} error)
  > ```

### 基本示例

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <h1>
        <span>hello go template by yuluo</span>
        <br>
        {{/*点表示替换变量值 实质就是字符串替换 */}}
        {{.}}
    </h1>
</body>
</html>
```

```go
package main

import (
   "fmt"
   "html/template"
   "net/http"
)

func sayHello(w http.ResponseWriter, r *http.Request) {
   // 定义模板文件

   // 解析模板文件
   t, err := template.ParseFiles("./template.tmpl")
   if err != nil {
      fmt.Printf("Parse Template failed, rr:%v\n", err)
      return
   }

   // 渲染模板数据
   name := "yuluo"
   err = t.Execute(w, name)
   if err != nil {
      fmt.Printf("render template failed, err%v\n", err)
      return
   }
}

func main() {
   http.HandleFunc("/hello", sayHello)

   err := http.ListenAndServe(":9000", nil)
   if err != nil {
      fmt.Printf("Http Server start failed, err:%v\n", err)
      return
   }
}
```

### 模板引擎语法

#### `{{.}}`

表示传入的数据

### 注释

`{{/*content*/}}`

### pipeline

指产生数据的操作。比如`{{.}}、{{.Name}}`等，Go的模板语法中支持使用管道符号 `|`链接多个命令。用法和linux下的管道命令相似。**前面的命令会将运算结果（或返回值）传递给后一个命令的最后一个位置**

### 变量

```go
自定义变量
{{$v1 := 100}}
使用传递过来的变量
{{$age := .m1.Age}}
```

### 移除空格

```go
去除两边的空格
{{- .u1.Age -}}
```

### if语句

```go
{{if $v1}}
{{$v1}}
{{else}}
啥都没有
{{end}}
```

```go
<hr>
{{/*lt 比较大小*/}}
{{if lt .m1.Age 22}}
好好上学
{{else}}
好好工作
{{end}}
```

### range循环

range遍历map的时候，第一个参数是key

切片第一个参数是index

```go
<hr>
{{/*切片*/}}
{{range $index, $hobby := .hobby}}
<p>{{$index}} - {{$hobby}}</p>
{{end}}
```

### 比较函数

了解

### 模板嵌套

在当前的模板中在嵌入一个template

```go
func demo(w http.ResponseWriter, r *http.Request) {
   // 定义模板
   // 解析模板  渲染嵌套模板时，需要将被包含的模板写在后面
   t, err := template.ParseFiles("./t.tmpl", "./ul.tmpl")
   if err != nil {
      fmt.Printf("%v\n", err)
      return
   }
   // 渲染模板
   t.Execute(w, "yuluo")
}

func main() {

   // 测试模板嵌套
   http.HandleFunc("/temp", demo)

   err := http.ListenAndServe(":9090", nil)

   if err != nil {
      fmt.Printf("Http Server start failed, err%v\n", err)
   }
}
```

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    {{/*引入另外一个单独的模板*/}}
    {{template "ul.tmpl"}}

    <div>
        你好！ {{.}}
    </div>

    {{/*嵌套另外一个定义的模板*/}}
    {{template "ol.tmpl"}}

    {{/*通过define定义模板*/}}
    {{define "ol.tmpl"}}
    <ol>
        <li>吃饭</li>
        <li>睡觉</li>
    </ol>
    {{end}}
</body>
</html>
```

```html
<ul>
    <li>测试</li>
    <li>日志</li>
    <li>debug</li>
</ul>
```

### 模板继承

模板继承的理解：

> 建立一个根模板，在所有的子模版编写时，都继承母版
>
> **网页中重复出现的部分，导航栏，版权部分等**就可以分离出来作为母板

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div>
        {{block "content" .}}{{end}}
    </div>
</body>
</html>
```

```html
{{继承根模板,点用来继承数据}}
{{template "base.tmpl" .}}
{{/*重新定义块模板*/}}
{{define "content"}}
<h1>这是index页面</h1>
    <P>Hello! {{.}}</P>
{{end}}
```

需要注意的是：在解析模板时

```go
// 解析模板  根模板在前
template.ParseFiles("./templates/base.tmpl", "./templates/index.tmpl")
// 渲染模板 指定最终渲染的模板 
template.ExcuteTemplate(w, "index.tmpl", "yuluo")
```

### 模板补充

#### 修改模板引擎的标识符

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div>
        {[/*使用自定义的符号表示*/]}
        <p>hello ! {[ . ]}</p>
    </div>
</body>
</html>
```

```go
package main

import (
   "fmt"
   "html/template"
   "net/http"
)

func sayHello(w http.ResponseWriter, r *http.Request) {
   // 定义模板

   // 解析模板
   t, err := template.New("index.tmpl").
      Delims("{[", "]}").
      ParseFiles("./index.tmpl")
   if err != nil {
      fmt.Printf("%v\n", err)
   }
   // 渲染模板
   err = t.Execute(w, "yuluo")
   if err != nil {
      fmt.Printf("%V\n", err)
   }
}

func main() {

   http.HandleFunc("/", sayHello)
   err := http.ListenAndServe(":9090", nil)
   if err != nil {
      fmt.Printf("Http Server start failed err:%v\n", err)
      return
   }
}
```

#### text/template和html/template的区别

开发网站时：***永远不要相信你的用户提交的内容***

> html/template针对需要返回html内容的场景，在模板渲染的过程中，会对一些有风险的内容进行转义，来避免跨站脚本的攻击。

## gin框架模板渲染

### 渲染单个模板

```go
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
   "net/http"
)

func main() {

   // 创建默认的gin路由引擎
   r := gin.Default()

   // 加载，解析html模板
   r.LoadHTMLFiles("templates/index.tmpl")

   // 使用匿名函数方式
   r.GET("/index", func(c *gin.Context) {
      // http请求  gin.H {} 本质是一个map
      // 模板渲染
      c.HTML(http.StatusOK, "index.tmpl", gin.H{
         "title": "yuluo.com",
      })
   })

   // 启动
   err := r.Run(":9090")
   if err != nil {
      fmt.Printf("Http Server start failed %v\n", err)
      return
   }

}
```

index.tmpl

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>posts/index</title>
</head>
<body>
{{.title}}
</body>
</html>
```

### 渲染多个模板

首先定义一个存放模板文件的templates文件夹，然后在其内部按照业务分别定义一个posts文件夹和一个users文件夹

posts/index.tmpl文件

```html
{{define "posts/index.tmpl"}}
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>posts/index</title>
</head>
<body>
{{.title}}
</body>
</html>
{{end}}
```

user/index.tmpl

> 通过define起一个名字辨别两个index.tmpl文件

```html
{{define "user/index.tmpl"}}
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>user/index</title>
</head>
<body>

</body>
</html>
{{end}}
```

main.go

> 使用`r.LoadHTMLGlob("templates/**/*")`同时加载多个模板文件

```go
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
   "net/http"
)

func main() {

   // 创建默认的gin路由引擎
   r := gin.Default()

   // 加载，解析html模板
   //r.LoadHTMLFiles("templates/index.tmpl")

   // 接受多个tmpl文件  加载templates下的所有目录的所有文件
   r.LoadHTMLGlob("templates/**/*")

   // 使用匿名函数方式
   // posts下面的index.tmpl的模板
   r.GET("/posts/index", func(c *gin.Context) {
      // http请求  gin.H {} 本质是一个map
      // 模板渲染
      c.HTML(http.StatusOK, "posts/index.tmpl", gin.H{
         "title": "posts:yuluo.com",
      })
   })

   // user下面的index.tmpl的模板
   r.GET("/user/index", func(c *gin.Context) {
      // http请求  gin.H {} 本质是一个map
      // 模板渲染
      c.HTML(http.StatusOK, "user/index.tmpl", gin.H{
         "title": "user:yuluo.com",
      })
   })

   // 启动
   err := r.Run(":9090")
   if err != nil {
      fmt.Printf("Http Server start failed %v\n", err)
      return
   }

}
```

### 自定义模板函数

> 我们需要在网页上展示一个超链接，html标签
>
> gin框架会自己帮我们转移html标签位普通的字符，此时需要我们自定义模板函数

```html
{{define "user/index.tmpl"}}
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>user/index</title>
</head>
<body>
{{.title | safe}}
</body>
</html>
{{end}}
```

此时，运行代码会出现safe函数未定义

我们需要在main.go中加入模板函数 

**注意：在模板解析之前加入函数**

```go
// 在gin框架中 加入自定义的模板函数
r.SetFuncMap(template.FuncMap{
   // 匿名函数
   "safe": func(str string) template.HTML {
      return template.HTML(str)
   },
})
```

### 静态文件处理

main.go

```go
// 加载静态文件  当文件中有img1标识的，就会指向后面的这个本地链接
// 相当于路径映射
r.Static("/img", "./static/img")
```

tmpl文件

```html
<body>
{{.title | safe}}
<img src="/img/1.jpg">
</body>
```

### 模板继承

不支持`html/template`的`block`用法，需要使用一个额外的第三方库

## Gin框架返回json

### 方法1 使用map

```go
package main

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"net/http"
)

func main() {
	r := gin.Default()

	r.GET("/json", func(c *gin.Context) {

		// 方法1.1：使用map
		//data := map[string]interface{}{
		//	"name":    "yuluo",
		//	"message": "hello !",
		//	"age":     "20",
		//}

		// 方法1.2 gin框架提前为我们准备了这种map的数据类型 gin.H
		dataGin := gin.H{
			"name":    "yuluo",
			"message": "hello !",
			"age":     "20",
		}

		c.JSON(http.StatusOK, dataGin)
	})

	err := r.Run(":9090")
	if err != nil {
		fmt.Printf("Gin Web Server Start Failed %v\n", err)
		return
	}
}

```

### 方法2 使用结构体

```go
// 方法2 结构体
type msg struct {
   Name    string
   Age     int
   Message string
}

r.GET("/jsonByStruct", func(c *gin.Context) {
   data := msg{
      "小王子",
      20,
      "hello !",
   }
   c.JSON(http.StatusOK, data)
})
```

**结构体中的属性名不能小写，小写默认是不可导出（不公开）的，大写是公开的**

如果非要是小写的，可以用结构体的tag（实现对结构体的定制化操作）来表明

```go
type msg struct {
   // 当用json包的反射对结构体这个字段做操作时，他的名字就是name。
   name    string `json:"name"`
   Age     int
   Message string
}
```

## gin获取querystring参数

get请求`?`号之后的querystring获取

> - 通过从context对象中的Query方法去获取
> - 通过c.DefaultQuery() 如果有值就使用，没有就使用默认值

```go
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
   "net/http"
)

/**
querystring 参数的获取
*/
func main() {

   router := gin.Default()

   router.GET("/web", func(c *gin.Context) {
      // querystring参数就是url后面的键值形式的参数
      // http://localhost:9090?query=yuluo
      // name := c.Query("query") // 通过content中的Query获取
      
      // name := c.DefaultQuery("query", "default value")
      
      //name, ok := c.GetQuery("query")  // 返回值有两个，一个参数和一个布尔值 取不到值返回一个空串和false
      //if !ok {
      // // 取不到值
      // name = "default value"
      //}
      
      // 多个参数获取
      name := c.Query("query")
      age := c.Query("age")
      
      fmt.Printf("parmeter query is %v\n , age is %v\n", name, age)

      c.JSON(http.StatusOK, "ok"+name)
   })

   err := router.Run(":9090")
   if err != nil {
      fmt.Printf("Gin Web Server start Failed err %v\n", err)
      return
   }
}
```

## gin获取form参数

loin.html

```html
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>login/form</title>
</head>
<body>
<form action="/form" method="post">
    <label for="username">username</label>
    <input type="text" name="username" id="username">
    <br>
    <label for="age">age</label>
    <input type="text" name="age" id="age">
    <br>
    <input type="submit" value="登录">
</form>
</body>
</html>
```

index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>home</title>
</head>
<body>
  <h1>登录成功!</h1>
  {{.Name}}
  {{.Age}}
</body>
</html>
```

main.go

```go
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
   "net/http"
)

/*
获取form表单的参数
*/
func main() {
   r := gin.Default()

   r.LoadHTMLFiles("./login.html", "./index.html")

   r.GET("/form", func(c *gin.Context) {
      c.HTML(http.StatusOK, "login.html", nil)
   })
   // login post
   r.POST("/form", func(c *gin.Context) {
      username := c.PostForm("username")
      age := c.PostForm("age")

      c.HTML(http.StatusOK, "index.html", gin.H{
         "Name": username,
         "Age":  age,
      })

      fmt.Printf("username is %v\n. age is %v\n", username, age)
   })

   err := r.Run(":9090")
   if err != nil {
      return
   }

   r.Run(":9090")
}
```

> 也可以使用`c.DefaultPostForm`用法和上面类似
>
> `username := c.DefaultPostForm("username", "***")`

**web开发时，一个请求对应一个响应 login第一次请求是获取登录页面，点击登录之后处理的登录请求**

## gin获取json参数（请求体参数）

```go
r.POST("/json", func(c *gin.Context) {
	// 注意：下面为了举例子方便，暂时忽略了错误处理
	b, _ := c.GetRawData()  // 从c.Request.Body读取请求数据
	// 定义map或结构体
	var m map[string]interface{}
	// 反序列化
	_ = json.Unmarshal(b, &m)

	c.JSON(http.StatusOK, m)
})
```

## gin获取URL路径参数

url: `http://localhost:9090/yuluo/age`

```go
func main() {
	//Default返回一个默认的路由引擎
	r := gin.Default()
	r.GET("/:username/:age", func(c *gin.Context) {
		username := c.Param("username")
		age := c.Param("age")
		//输出json结果给调用方
		c.JSON(http.StatusOK, gin.H{
			"message":  "ok",
			"username": username,
			"age":  age,
		})
	})

	r.Run(":8080")
}
```

## gin参数绑定

```go
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
   "net/http"
)

type UserInfo struct {
   Username string
   Password string
}

func main() {
   r := gin.Default()

   r.GET("/user", func(c *gin.Context) {
      username := c.Query("username")
      password := c.Query("password")

      u := UserInfo{
         username: username,
         password: password,
      }
      fmt.Printf("%#v\n", u)
      c.JSON(http.StatusOK, gin.H{
         "message": "ok",
      })
   })

   r.Run(":9090")
}
```

当请求的参数过多时，不好手动处理。我们可以采取gin框架参数绑定的方式

**类似与spring mvc的参数自动绑定**

```go
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
   "net/http"
)

type UserInfo struct {
	// 公开 用户传过来的值和结构体对应上，所以要写tag
	// tag是别人通过反射获取结构体的字段的时候，能够一一对应上
	Username string `form:"username" json:"username"`
	Password string `form:"password" json:"password"`
}

func main() {
   r := gin.Default()

   r.GET("/user", func(c *gin.Context) {
      //username := c.Query("username")
      //password := c.Query("password")
      //
      //u := UserInfo{
      // username: username,
      // password: password,
      //}

      // 声明一个userInfo类型的变量是一个结构体
      var u UserInfo
      err := c.ShouldBind(&u) // 自己完成参数绑定 需要修改u的值，所以要转指针类型 底层用到了反射
      if err != nil {
         c.JSON(http.StatusBadRequest, gin.H{
            "error": err.Error(),
         })
      } else {
         c.JSON(http.StatusOK, gin.H{
            "status": "ok",
         })
      }

      fmt.Printf("%#v\n", u)
      //c.JSON(http.StatusOK, gin.H{
      // "message": "ok",
      //})
   })

   r.Run(":9090")
}
```

> 同样适用于post请求

## gin文件上传

### 单文件

index.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>index</title>
</head>
<body>
<!--表示以二进制的方式传输一些东西-->
<form action="/upload" method="post" enctype="multipart/form-data">
    <input type="file" name="f1">
    <input type="submit" value="提交">
</form>
</body>
</html>
```

main.go

```go
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
   "net/http"
   "path"
)

func main() {

   r := gin.Default()

   // gin 解析模板文件
   r.LoadHTMLFiles("./index.html")

   r.GET("/upload", func(c *gin.Context) {
      c.HTML(http.StatusOK, "index.html", nil)
   })

   r.POST("/upload", func(c *gin.Context) {

      /*
         因为multipart-form提交文件时默认的内存限制是32MB
         可以通过以下的处理方式修改
         r.MAxMultipartMemory = 8 << 20 // 8MB
      */

      // 从请求中读取文件
      fileReader, err := c.FormFile("f1")
      if err != nil {
         fmt.Printf("file upload failed error: %v\n", err)

         c.JSON(http.StatusBadRequest, gin.H{
            "error": err.Error(),
         })
      } else {
         // 将读取到的文件保存到服务端的本地
         //filePath := fmt.Sprintf("../%s", fileReader.Filename)  拼接路径
         filePath := path.Join("./", fileReader.Filename) // path是go的一个标准库
         err = c.SaveUploadedFile(fileReader, filePath)
         if err != nil {
            fmt.Printf("file save failed err %v\n", err)
         }
         c.JSON(http.StatusOK, gin.H{
            "status": "ok",
         })
      }
   })

   r.Run(":9090")

}
```

### 多文件

```go
func main() {
	router := gin.Default()
	// 处理multipart forms提交文件时默认的内存限制是32 MiB
	// 可以通过下面的方式修改
	// router.MaxMultipartMemory = 8 << 20  // 8 MiB
	router.POST("/upload", func(c *gin.Context) {
		// Multipart form
		form, _ := c.MultipartForm()
		files := form.File["file"]

		for index, file := range files {
			log.Println(file.Filename)
			dst := fmt.Sprintf("C:/tmp/%s_%d", file.Filename, index)
			// 上传文件到指定的目录
			c.SaveUploadedFile(file, dst)
		}
		c.JSON(http.StatusOK, gin.H{
			"message": fmt.Sprintf("%d files uploaded!", len(files)),
		})
	})
	router.Run()
}
```

## gin请求重定向

### HTTP重定向

```go
r.GET("/index", func(c *gin.Context) {
    c.Redirect(http.StatusMovedPermanently, "https://www.baidu.com")
})
```

### 路由重定向

```go
r.GET("/a", func(c *gin.Context) {
    // 跳转到 /b 对应的路由处理函数
    c.Request.URL.Path = "/b" // 修改请求的url
    r.HandlerContext(c) // 继续后续的处理
})

r.GET("/b", func(c *gin.Context) {
    c.JSON(http.StatusOk, gin.H {
        "message": "b",
    })
})
```

## gin路由和路由组

### 普通路由

```go
// 普通路由 常见的四个请求方式
// GET 表示get请求 访问/index的GET请求会走这个处理逻辑
// 获取数据等
r.GET("/index", func(c *gin.Context) {
   c.JSON(http.StatusOK, gin.H{
      "method": "GET",
   })
})
// 访问/index的POST请求会走这个处理逻辑
// 添加数据
r.POST("/index", func(c *gin.Context) {
   c.JSON(http.StatusOK, gin.H{
      "method": "POST",
   })
})
// delete表示删除某个数据时使用的请求
r.DELETE("/index", func(c *gin.Context) {
   c.JSON(http.StatusOK, gin.H{
      "method": "DELETE",
   })
})
// put请求表示更新某个数据
r.PUT("/index", func(c *gin.Context) {
   c.JSON(http.StatusOK, gin.H{
      "method": "PUT",
   })
})
```

### any

```go
// any请求 包含所有的请求方法
r.Any("/anyIndex", func(c *gin.Context) {

   switch c.Request.Method {
   // case可以自己手写，同时http库也定义好了处理常量
   case "GET":
      c.JSON(http.StatusOK, gin.H{
         "message": "any -> get",
      })

   case http.MethodPost:
      c.JSON(http.StatusOK, gin.H{
         "message": "any -> post",
      })
   }
   // case ……
})
```

### 没有路由响应

```go
// 没有对应的路由关系映射时的处理方式 404
// 默认的404提示是： 404 page not found
r.NoRoute(func(c *gin.Context) {
   c.HTML(http.StatusNotFound, "views/404.html", nil)
})
```

### 路由组

```go
// 路由组 将路由分组管理
//r.GET("/book/index", func(c *gin.Context) {
// c.JSON(http.StatusOK, gin.H{
//    "message": "书籍首页",
// })
//})
//r.GET("/book/detail", func(c *gin.Context) {
// c.JSON(http.StatusOK, gin.H{
//    "message": "书籍详情页",
// })
//})

// 参数：提取出公用的前缀，
bookGroup := r.Group("/book")
bookGroup.GET("/index", func(c *gin.Context) {
   c.JSON(http.StatusOK, gin.H{
      "message": "书籍首页",
   })
})
bookGroup.GET("/detail", func(c *gin.Context) {
   c.JSON(http.StatusOK, gin.H{
      "message": "书籍详情页",
   })
})
```

**同时，路由组支持嵌套**

## 中间件

> gin框架允许开发者在处理请求的过程中，加入用户自己的钩子（hook）函数。这个钩子函数就叫做中间件。中间件适合处理一些公共的业务逻辑。
>
> 比如：
>
> - 登录认证，权限校验，数据分页，记录日志，耗时统计等。

### 简单例子

参数类型要是`*gin.Context`

```go
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
   "net/http"
)

// handlerFunc
func indexHandler(c *gin.Context) {
   fmt.Println("indexHandler in ……")
   c.JSON(http.StatusOK, gin.H{
      "message": "yuluo",
   })
}

// 定义一个中间件
func m1(c *gin.Context) {
   fmt.Printf("m1 in ... \n")
}

func main() {
   r := gin.Default()

   r.GET("/index", m1, indexHandler)

   r.Run(":9090")

}
```

### 统计耗时

```go
package main

import (
   "fmt"
   "github.com/gin-gonic/gin"
   "net/http"
   "time"
)

// handlerFunc
func indexHandler(c *gin.Context) {
   fmt.Println("indexHandler in ……")
   c.JSON(http.StatusOK, gin.H{
      "message": "yuluo",
   })
}

// 定义一个中间件 统计耗时
func m1(c *gin.Context) {
   fmt.Printf("m1 in ... \n")
   // 实现一个计时功能
   startTime := time.Now()
   // 调用后续的处理函数
   c.Next()
   // 组织调用后续的处理函数
   c.Abort()
   costTime := time.Since(startTime)
   fmt.Printf("程序执行花费的时间：%v\n", costTime)
   fmt.Printf("m1 out ... \n")
}

func main() {
   r := gin.Default()

   r.GET("/index", m1, indexHandler)

   r.Run(":9090")

}
```

## gorm

### ORM简介

> Object Relational Mapping
>
> 对象关系映射
>
> - 对象：程序中的实例，go中的结构体，java中的javaBean
> - 关系：关系数据库，mysql

GORM框架：go语言的orm框架，类似于java的mybatis

orm映射关系

| go     | 数据库     |
| ------ | ---------- |
| 数据表 | 结构体     |
| 数据行 | 结构体实例 |
| 字段   | 结构体字段 |

优缺点：

- 优点：提高开发效率
- 缺点：牺牲执行性能
- 缺点：牺牲灵活性
- 缺点：弱化了sql能力

### GORM安装

`go get -u github.com/jinzhu/gorm`

### 连接数据库示例 mysql

```go
package main

import (
   "fmt"
   "github.com/jinzhu/gorm"
   _ "github.com/jinzhu/gorm"
   _ "github.com/jinzhu/gorm/dialects/mysql"
)

// Student 结构体对应的是数据库中的student表
type gormStudent struct {
   Id    int
   Name  string
   Email string
   Age   int
}

func main() {

   // 连接数据库
   // parseTime=True 解析数据库字段中的时间类型
   // loc=local 本地时间
   db, err := gorm.Open("mysql", "root:082916@(127.0.0.1:3306)/mtdatabases?charset=utf8mb4&parseTime=True&loc=Local")
   if err != nil {
      fmt.Printf("mysql server connect failed error: %v\n", err)
      return
   }

   // 最后一定要关闭数据库连接 defer关键字后在最后执行
   defer func(db *gorm.DB) {
      err := db.Close()
      if err != nil {
         fmt.Printf("mysql server close failed error:%v\n", err)
         return
      }
   }(db)

   // 创建表，自动迁移（将结构体和数据表进行对应）
   db.AutoMigrate(&gormStudent{})
   // 创建数据行
   s1 := gormStudent{1, "yuluo", "yuluo@qq.com", 21}
   db.Create(&s1)
    
	// 查询
	var s gormStudent
	// 查询表中的第一条数据
	db.First(&s)
	fmt.Printf("从数据库中查出来的值 s：%v\n", s)

	// 更新
	db.Model(&s).Update("age", 22)
	
	// 删除 s
	db.Delete(&s)
}
```

### gorm数据模型

gorm自定义的内部模型

```go
type Model struct {
    ID 			uiny `gorm:"primary_key"`
    CreateAt	time.Time
    UpdateAt	time.Time
    DeleteAt	*time.Time
}
```

我们可以将他嵌入到我们自己的模型中

```go
type User struct {
    gorm.Model
    Name	string
}
```

也可以自己定义

```go
type User struct {
    ID 		int
    Name	string
}
```

复杂数据模型示例

```go
type User struct {
  gorm.Model
  Name         string
  Age          sql.NullInt64
  Birthday     *time.Time
  Email        string  `gorm:"type:varchar(100);unique_index"`
  Role         string  `gorm:"size:255"` // 设置字段大小为255
  MemberNumber *string `gorm:"unique;not null"` // 设置会员号（member number）唯一并且不为空
  Num          int     `gorm:"AUTO_INCREMENT"` // 设置 num 为自增类型
  Address      string  `gorm:"index:addr"` // 给address字段创建名为addr的索引
  IgnoreMe     int     `gorm:"-"` // 忽略本字段
}
```

#### 主键

```go
type User struct {
  ID   string // 名为`ID`的字段会默认作为表的主键
  Name string
}

// 使用`AnimalID`作为主键
type Animal struct {
  AnimalID int64 `gorm:"primary_key"`
  Name     string
  Age      int64
```

#### 表名

表名默认就是结构体名称的复数

```go
type User struct {} // 默认表名是 `users`

// 将 User 的表名设置为 `profiles`
func (User) TableName() string {
  return "profiles"
}

func (u User) TableName() string {
  if u.Role == "admin" {
    return "admin_users"
  } else {
    return "users"
  }
}

// 禁用默认表名的复数形式，如果置为 true，则 `User` 的默认表名是 `user`
db.SingularTable(true)
```

也可以通过`Table()`指定表名：

```go
// 使用User结构体创建名为`deleted_users`的表
db.Table("deleted_users").CreateTable(&User{})

var deleted_users []User
db.Table("deleted_users").Find(&deleted_users)
//// SELECT * FROM deleted_users;

db.Table("deleted_users").Where("name = ?", "jinzhu").Delete()
//// DELETE FROM deleted_users WHERE name = 'jinzhu';
```

GORM还支持更改默认表名称规则：

```go
gorm.DefaultTableNameHandler = func (db *gorm.DB, defaultTableName string) string  {
  return "prefix_" + defaultTableName;
}
```

#### 列名

列名由字段名称进行下划线分割来生成

```go
type User struct {
  ID        uint      // column name is `id`
  Name      string    // column name is `name`
  Birthday  time.Time // column name is `birthday`
  CreatedAt time.Time // column name is `created_at`
}
```

可以使用结构体tag指定列名：

```go
type Animal struct {
  AnimalId    int64     `gorm:"column:beast_id"`         // set column name to `beast_id`
  Birthday    time.Time `gorm:"column:day_of_the_beast"` // set column name to `day_of_the_beast`
  Age         int64     `gorm:"column:age_of_the_beast"` // set column name to `age_of_the_beast`
}
```

#### 时间戳跟踪

#### CreatedAt

如果模型有 `CreatedAt`字段，该字段的值将会是初次创建记录的时间。

```go
db.Create(&user) // `CreatedAt`将会是当前时间

// 可以使用`Update`方法来改变`CreateAt`的值
db.Model(&user).Update("CreatedAt", time.Now())
```

#### UpdatedAt

如果模型有`UpdatedAt`字段，该字段的值将会是每次更新记录的时间。

```go
db.Save(&user) // `UpdatedAt`将会是当前时间

db.Model(&user).Update("name", "jinzhu") // `UpdatedAt`将会是当前时间
```

#### DeletedAt

如果模型有`DeletedAt`字段，调用`Delete`删除该记录时，将会设置`DeletedAt`字段为当前时间，而不是直接将记录从数据库中删除。

### 增

中文文档：**https://gorm.io/zh_CN/docs/**

数据模型

```go
type User struct {
	ID           int64
	Name         string
	Age          int64
}
```

使用使用`NewRecord()`查询主键是否存在，主键为空使用`Create()`创建记录：

```go
user := User{Name: "q1mi", Age: 18}

db.NewRecord(user) // 主键为空返回`true`
db.Create(&user)   // 创建user
db.NewRecord(user) // 创建`user`后返回`false`
```

### 默认值

可以通过 tag 定义字段的默认值，比如：

```go
type User struct {
  ID   int64
  Name string `gorm:"default:'小王子'"`
  Age  int64
}
```

**注意：**通过tag定义字段的默认值，在创建记录时候生成的 SQL 语句会排除没有值或值为 零值 的字段。 在将记录插入到数据库后，Gorm会从数据库加载那些字段的默认值。

举个例子：

```go
var user = User{Name: "", Age: 99}
db.Create(&user)
```

上面代码实际执行的SQL语句是

`INSERT INTO users("age") values('99');`，

排除了零值字段`Name`，而在数据库中这一条数据会使用设置的默认值`小王子`作为Name字段的值。

> **注意：**所有字段的零值, 比如`0`, `""`,`false`或者其它`零值`，都不会保存到数据库内，但会使用他们的默认值。 如果你想避免这种情况，可以考虑使用指针或实现 `Scanner/Valuer`接口，比如：

#### 使用指针方式实现零值存入数据库

```go
// 使用指针
type User struct {
  ID   int64
  Name *string `gorm:"default:'小王子'"`
  Age  int64
}
user := User{Name: new(string), Age: 18))}
db.Create(&user)  // 此时数据库中该条记录name字段的值就是''
```

#### 使用Scanner/Valuer接口方式实现零值存入数据库

```go
// 使用 Scanner/Valuer
type User struct {
	ID int64
	Name sql.NullString `gorm:"default:'小王子'"` // sql.NullString 实现了Scanner/Valuer接口
	Age  int64
}
user := User{Name: sql.NullString{"", true}, Age:18}
db.Create(&user)  // 此时数据库中该条记录name字段的值就是''
```

### 扩展创建选项

例如`PostgreSQL`数据库中可以使用下面的方式实现合并插入, 有则更新, 无则插入。

```go
// 为Instert语句添加扩展SQL选项
db.Set("gorm:insert_option", "ON CONFLICT").Create(&product)
// INSERT INTO products (name, code) VALUES ("name", "code") ON CONFLICT;
```

### 删

### 删除记录

**警告** 删除记录时，请确保主键字段有值，GORM 会通过主键去删除记录，如果主键为空，GORM 会删除该 model 的所有记录。

```go
// 删除现有记录
db.Delete(&email)
//// DELETE from emails where id=10;

// 为删除 SQL 添加额外的 SQL 操作
db.Set("gorm:delete_option", "OPTION (OPTIMIZE FOR UNKNOWN)").Delete(&email)
//// DELETE from emails where id=10 OPTION (OPTIMIZE FOR UNKNOWN);
```

### 批量删除

删除全部匹配的记录

```go
db.Where("email LIKE ?", "%jinzhu%").Delete(Email{})
//// DELETE from emails where email LIKE "%jinzhu%";

db.Delete(Email{}, "email LIKE ?", "%jinzhu%")
//// DELETE from emails where email LIKE "%jinzhu%";
```

### 软删除

**相对于java中的逻辑删除。**

如果一个 model 有 `DeletedAt` 字段，他将自动获得软删除的功能！ 当调用 `Delete` 方法时， 记录不会真正的从数据库中被删除， 只会将`DeletedAt` 字段的值会被设置为当前时间

```go
db.Delete(&user)
//// UPDATE users SET deleted_at="2013-10-29 10:23" WHERE id = 111;

// 批量删除
db.Where("age = ?", 20).Delete(&User{})
//// UPDATE users SET deleted_at="2013-10-29 10:23" WHERE age = 20;

// 查询记录时会忽略被软删除的记录
db.Where("age = 20").Find(&user)
//// SELECT * FROM users WHERE age = 20 AND deleted_at IS NULL;

// Unscoped 方法可以查询被软删除的记录
db.Unscoped().Where("age = 20").Find(&users)
//// SELECT * FROM users WHERE age = 20;
```

### 物理删除

```go
// Unscoped 方法可以物理删除记录
db.Unscoped().Delete(&order)
//// DELETE FROM orders WHERE id=10;
```

### 改

### 更新所有字段

`Save()`默认会更新该对象的所有字段，即使你没有赋值。

```go
db.First(&user)

user.Name = "七米"
user.Age = 99
db.Save(&user)

////  UPDATE `users` SET `created_at` = '2020-02-16 12:52:20', `updated_at` = '2020-02-16 12:54:55', `deleted_at` = NULL, `name` = '七米', `age` = 99, `active` = true  WHERE `users`.`deleted_at` IS NULL AND `users`.`id` = 1
```

### 更新修改字段

如果你只希望更新指定字段，可以使用`Update`或者`Updates`

```go
// 更新单个属性，如果它有变化
db.Model(&user).Update("name", "hello")
//// UPDATE users SET name='hello', updated_at='2013-11-17 21:34:10' WHERE id=111;

// 根据给定的条件更新单个属性
db.Model(&user).Where("active = ?", true).Update("name", "hello")
//// UPDATE users SET name='hello', updated_at='2013-11-17 21:34:10' WHERE id=111 AND active=true;

// 使用 map 更新多个属性，只会更新其中有变化的属性
db.Model(&user).Updates(map[string]interface{}{"name": "hello", "age": 18, "active": false})
//// UPDATE users SET name='hello', age=18, active=false, updated_at='2013-11-17 21:34:10' WHERE id=111;

// 使用 struct 更新多个属性，只会更新其中有变化且为非零值的字段
db.Model(&user).Updates(User{Name: "hello", Age: 18})
//// UPDATE users SET name='hello', age=18, updated_at = '2013-11-17 21:34:10' WHERE id = 111;

// 警告：当使用 struct 更新时，GORM只会更新那些非零值的字段
// 对于下面的操作，不会发生任何更新，"", 0, false 都是其类型的零值
db.Model(&user).Updates(User{Name: "", Age: 0, Active: false})
```

### 更新选定字段

如果你想更新或忽略某些字段，你可以使用 `Select`，`Omit`

```go
db.Model(&user).Select("name").Updates(map[string]interface{}{"name": "hello", "age": 18, "active": false})
//// UPDATE users SET name='hello', updated_at='2013-11-17 21:34:10' WHERE id=111;

db.Model(&user).Omit("name").Updates(map[string]interface{}{"name": "hello", "age": 18, "active": false})
//// UPDATE users SET age=18, active=false, updated_at='2013-11-17 21:34:10' WHERE id=111;
```

### 无Hooks更新

上面的更新操作会自动运行 model 的 `BeforeUpdate`, `AfterUpdate` 方法，更新 `UpdatedAt` 时间戳, 在更新时保存其 `Associations`, 如果你不想调用这些方法，你可以使用 `UpdateColumn`， `UpdateColumns`

```go
// 更新单个属性，类似于 `Update`
db.Model(&user).UpdateColumn("name", "hello")
//// UPDATE users SET name='hello' WHERE id = 111;

// 更新多个属性，类似于 `Updates`
db.Model(&user).UpdateColumns(User{Name: "hello", Age: 18})
//// UPDATE users SET name='hello', age=18 WHERE id = 111;
```

### 批量更新

批量更新时`Hooks（钩子函数）`不会运行。

```go
db.Table("users").Where("id IN (?)", []int{10, 11}).Updates(map[string]interface{}{"name": "hello", "age": 18})
//// UPDATE users SET name='hello', age=18 WHERE id IN (10, 11);

// 使用 struct 更新时，只会更新非零值字段，若想更新所有字段，请使用map[string]interface{}
db.Model(User{}).Updates(User{Name: "hello", Age: 18})
//// UPDATE users SET name='hello', age=18;

// 使用 `RowsAffected` 获取更新记录总数
db.Model(User{}).Updates(User{Name: "hello", Age: 18}).RowsAffected
```

### 使用SQL表达式更新

先查询表中的第一条数据保存至user变量。

```go
var user User
db.First(&user)
db.Model(&user).Update("age", gorm.Expr("age * ? + ?", 2, 100))
//// UPDATE `users` SET `age` = age * 2 + 100, `updated_at` = '2020-02-16 13:10:20'  WHERE `users`.`id` = 1;

db.Model(&user).Updates(map[string]interface{}{"age": gorm.Expr("age * ? + ?", 2, 100)})
//// UPDATE "users" SET "age" = age * '2' + '100', "updated_at" = '2020-02-16 13:05:51' WHERE `users`.`id` = 1;

db.Model(&user).UpdateColumn("age", gorm.Expr("age - ?", 1))
//// UPDATE "users" SET "age" = age - 1 WHERE "id" = '1';

db.Model(&user).Where("age > 10").UpdateColumn("age", gorm.Expr("age - ?", 1))
//// UPDATE "users" SET "age" = age - 1 WHERE "id" = '1' AND quantity > 10;
```

### 修改Hooks中的值

如果你想修改 `BeforeUpdate`, `BeforeSave` 等 Hooks 中更新的值，你可以使用 `scope.SetColumn`, 例如：

```go
func (user *User) BeforeSave(scope *gorm.Scope) (err error) {
  if pw, err := bcrypt.GenerateFromPassword(user.Password, 0); err == nil {
    scope.SetColumn("EncryptedPassword", pw)
  }
}
```

### 其它更新选项

```go
// 为 update SQL 添加其它的 SQL
db.Model(&user).Set("gorm:update_option", "OPTION (OPTIMIZE FOR UNKNOWN)").Update("name", "hello")
//// UPDATE users SET name='hello', updated_at = '2013-11-17 21:34:10' WHERE id=111 OPTION (OPTIMIZE FOR UNKNOWN);
```

### 查

### 一般查询

```go
// 根据主键查询第一条记录
db.First(&user)
//// SELECT * FROM users ORDER BY id LIMIT 1;

// 随机获取一条记录
db.Take(&user)
//// SELECT * FROM users LIMIT 1;

// 根据主键查询最后一条记录
db.Last(&user)
//// SELECT * FROM users ORDER BY id DESC LIMIT 1;

// 查询所有的记录
db.Find(&users)
//// SELECT * FROM users;

// 查询指定的某条记录(仅当主键为整型时可用)
db.First(&user, 10)
//// SELECT * FROM users WHERE id = 10;
```

### Where 条件

#### 普通SQL查询

```go
// Get first matched record
db.Where("name = ?", "jinzhu").First(&user)
//// SELECT * FROM users WHERE name = 'jinzhu' limit 1;

// Get all matched records
db.Where("name = ?", "jinzhu").Find(&users)
//// SELECT * FROM users WHERE name = 'jinzhu';

// <>
db.Where("name <> ?", "jinzhu").Find(&users)
//// SELECT * FROM users WHERE name <> 'jinzhu';

// IN
db.Where("name IN (?)", []string{"jinzhu", "jinzhu 2"}).Find(&users)
//// SELECT * FROM users WHERE name in ('jinzhu','jinzhu 2');

// LIKE
db.Where("name LIKE ?", "%jin%").Find(&users)
//// SELECT * FROM users WHERE name LIKE '%jin%';

// AND
db.Where("name = ? AND age >= ?", "jinzhu", "22").Find(&users)
//// SELECT * FROM users WHERE name = 'jinzhu' AND age >= 22;

// Time
db.Where("updated_at > ?", lastWeek).Find(&users)
//// SELECT * FROM users WHERE updated_at > '2000-01-01 00:00:00';

// BETWEEN
db.Where("created_at BETWEEN ? AND ?", lastWeek, today).Find(&users)
//// SELECT * FROM users WHERE created_at BETWEEN '2000-01-01 00:00:00' AND '2000-01-08 00:00:00';
```

#### Struct & Map查询

```go
// Struct
db.Where(&User{Name: "jinzhu", Age: 20}).First(&user)
//// SELECT * FROM users WHERE name = "jinzhu" AND age = 20 LIMIT 1;

// Map
db.Where(map[string]interface{}{"name": "jinzhu", "age": 20}).Find(&users)
//// SELECT * FROM users WHERE name = "jinzhu" AND age = 20;

// 主键的切片
db.Where([]int64{20, 21, 22}).Find(&users)
//// SELECT * FROM users WHERE id IN (20, 21, 22);
```

**提示：**当通过结构体进行查询时，GORM将会只通过非零值字段查询，这意味着如果你的字段值为`0`，`''`，`false`或者其他`零值`时，将不会被用于构建查询条件，例如：

```go
db.Where(&User{Name: "jinzhu", Age: 0}).Find(&users)
//// SELECT * FROM users WHERE name = "jinzhu";
```

你可以使用指针或实现 Scanner/Valuer 接口来避免这个问题.

```go
// 使用指针
type User struct {
  gorm.Model
  Name string
  Age  *int
}

// 使用 Scanner/Valuer
type User struct {
  gorm.Model
  Name string
  Age  sql.NullInt64  // sql.NullInt64 实现了 Scanner/Valuer 接口
}
```

### Not 条件

作用与 Where 类似的情形如下：

```go
db.Not("name", "jinzhu").First(&user)
//// SELECT * FROM users WHERE name <> "jinzhu" LIMIT 1;

// Not In
db.Not("name", []string{"jinzhu", "jinzhu 2"}).Find(&users)
//// SELECT * FROM users WHERE name NOT IN ("jinzhu", "jinzhu 2");

// Not In slice of primary keys
db.Not([]int64{1,2,3}).First(&user)
//// SELECT * FROM users WHERE id NOT IN (1,2,3);

db.Not([]int64{}).First(&user)
//// SELECT * FROM users;

// Plain SQL
db.Not("name = ?", "jinzhu").First(&user)
//// SELECT * FROM users WHERE NOT(name = "jinzhu");

// Struct
db.Not(User{Name: "jinzhu"}).First(&user)
//// SELECT * FROM users WHERE name <> "jinzhu";
```

### Or条件

```go
db.Where("role = ?", "admin").Or("role = ?", "super_admin").Find(&users)
//// SELECT * FROM users WHERE role = 'admin' OR role = 'super_admin';

// Struct
db.Where("name = 'jinzhu'").Or(User{Name: "jinzhu 2"}).Find(&users)
//// SELECT * FROM users WHERE name = 'jinzhu' OR name = 'jinzhu 2';

// Map
db.Where("name = 'jinzhu'").Or(map[string]interface{}{"name": "jinzhu 2"}).Find(&users)
//// SELECT * FROM users WHERE name = 'jinzhu' OR name = 'jinzhu 2';
```

### 内联条件

作用与`Where`查询类似，当内联条件与多个[立即执行方法](https://liwenzhou.com/posts/Go/gorm_crud/#autoid-1-3-1)一起使用时, 内联条件不会传递给后面的立即执行方法。

```go
// 根据主键获取记录 (只适用于整形主键)
db.First(&user, 23)
//// SELECT * FROM users WHERE id = 23 LIMIT 1;
// 根据主键获取记录, 如果它是一个非整形主键
db.First(&user, "id = ?", "string_primary_key")
//// SELECT * FROM users WHERE id = 'string_primary_key' LIMIT 1;

// Plain SQL
db.Find(&user, "name = ?", "jinzhu")
//// SELECT * FROM users WHERE name = "jinzhu";

db.Find(&users, "name <> ? AND age > ?", "jinzhu", 20)
//// SELECT * FROM users WHERE name <> "jinzhu" AND age > 20;

// Struct
db.Find(&users, User{Age: 20})
//// SELECT * FROM users WHERE age = 20;

// Map
db.Find(&users, map[string]interface{}{"age": 20})
//// SELECT * FROM users WHERE age = 20;
```

### 额外查询选项

```go
// 为查询 SQL 添加额外的 SQL 操作
db.Set("gorm:query_option", "FOR UPDATE").First(&user, 10)
//// SELECT * FROM users WHERE id = 10 FOR UPDATE;
```

### FirstOrInit

获取匹配的第一条记录，否则根据给定的条件初始化一个新的对象 (仅支持 struct 和 map 条件)

```go
// 未找到
db.FirstOrInit(&user, User{Name: "non_existing"})
//// user -> User{Name: "non_existing"}

// 找到
db.Where(User{Name: "Jinzhu"}).FirstOrInit(&user)
//// user -> User{Id: 111, Name: "Jinzhu", Age: 20}
db.FirstOrInit(&user, map[string]interface{}{"name": "jinzhu"})
//// user -> User{Id: 111, Name: "Jinzhu", Age: 20}
```

#### Attrs

如果记录未找到，将使用参数初始化 struct.

```go
// 未找到
db.Where(User{Name: "non_existing"}).Attrs(User{Age: 20}).FirstOrInit(&user)
//// SELECT * FROM USERS WHERE name = 'non_existing';
//// user -> User{Name: "non_existing", Age: 20}

db.Where(User{Name: "non_existing"}).Attrs("age", 20).FirstOrInit(&user)
//// SELECT * FROM USERS WHERE name = 'non_existing';
//// user -> User{Name: "non_existing", Age: 20}

// 找到
db.Where(User{Name: "Jinzhu"}).Attrs(User{Age: 30}).FirstOrInit(&user)
//// SELECT * FROM USERS WHERE name = jinzhu';
//// user -> User{Id: 111, Name: "Jinzhu", Age: 20}
```

#### Assign

不管记录是否找到，都将参数赋值给 struct.

```go
// 未找到
db.Where(User{Name: "non_existing"}).Assign(User{Age: 20}).FirstOrInit(&user)
//// user -> User{Name: "non_existing", Age: 20}

// 找到
db.Where(User{Name: "Jinzhu"}).Assign(User{Age: 30}).FirstOrInit(&user)
//// SELECT * FROM USERS WHERE name = jinzhu';
//// user -> User{Id: 111, Name: "Jinzhu", Age: 30}
```

### FirstOrCreate

获取匹配的第一条记录, 否则根据给定的条件创建一个新的记录 (仅支持 struct 和 map 条件)

```go
// 未找到
db.FirstOrCreate(&user, User{Name: "non_existing"})
//// INSERT INTO "users" (name) VALUES ("non_existing");
//// user -> User{Id: 112, Name: "non_existing"}

// 找到
db.Where(User{Name: "Jinzhu"}).FirstOrCreate(&user)
//// user -> User{Id: 111, Name: "Jinzhu"}
```

#### Attrs

如果记录未找到，将使用参数创建 struct 和记录.

```go
 // 未找到
db.Where(User{Name: "non_existing"}).Attrs(User{Age: 20}).FirstOrCreate(&user)
//// SELECT * FROM users WHERE name = 'non_existing';
//// INSERT INTO "users" (name, age) VALUES ("non_existing", 20);
//// user -> User{Id: 112, Name: "non_existing", Age: 20}

// 找到
db.Where(User{Name: "jinzhu"}).Attrs(User{Age: 30}).FirstOrCreate(&user)
//// SELECT * FROM users WHERE name = 'jinzhu';
//// user -> User{Id: 111, Name: "jinzhu", Age: 20}
```

#### Assign

不管记录是否找到，都将参数赋值给 struct 并保存至数据库.

```go
// 未找到
db.Where(User{Name: "non_existing"}).Assign(User{Age: 20}).FirstOrCreate(&user)
//// SELECT * FROM users WHERE name = 'non_existing';
//// INSERT INTO "users" (name, age) VALUES ("non_existing", 20);
//// user -> User{Id: 112, Name: "non_existing", Age: 20}

// 找到
db.Where(User{Name: "jinzhu"}).Assign(User{Age: 30}).FirstOrCreate(&user)
//// SELECT * FROM users WHERE name = 'jinzhu';
//// UPDATE users SET age=30 WHERE id = 111;
//// user -> User{Id: 111, Name: "jinzhu", Age: 30}
```

### 高级查询

#### 子查询

基于 `*gorm.expr` 的子查询

```go
db.Where("amount > ?", db.Table("orders").Select("AVG(amount)").Where("state = ?", "paid").SubQuery()).Find(&orders)
// SELECT * FROM "orders"  WHERE "orders"."deleted_at" IS NULL AND (amount > (SELECT AVG(amount) FROM "orders"  WHERE (state = 'paid')));
```

#### 选择字段

Select，指定你想从数据库中检索出的字段，默认会选择全部字段。

```go
db.Select("name, age").Find(&users)
//// SELECT name, age FROM users;

db.Select([]string{"name", "age"}).Find(&users)
//// SELECT name, age FROM users;

db.Table("users").Select("COALESCE(age,?)", 42).Rows()
//// SELECT COALESCE(age,'42') FROM users;
```

#### 排序

Order，指定从数据库中检索出记录的顺序。设置第二个参数 reorder 为 `true` ，可以覆盖前面定义的排序条件。

```go
db.Order("age desc, name").Find(&users)
//// SELECT * FROM users ORDER BY age desc, name;

// 多字段排序
db.Order("age desc").Order("name").Find(&users)
//// SELECT * FROM users ORDER BY age desc, name;

// 覆盖排序
db.Order("age desc").Find(&users1).Order("age", true).Find(&users2)
//// SELECT * FROM users ORDER BY age desc; (users1)
//// SELECT * FROM users ORDER BY age; (users2)
```

#### 数量

Limit，指定从数据库检索出的最大记录数。

```go
db.Limit(3).Find(&users)
//// SELECT * FROM users LIMIT 3;

// -1 取消 Limit 条件
db.Limit(10).Find(&users1).Limit(-1).Find(&users2)
//// SELECT * FROM users LIMIT 10; (users1)
//// SELECT * FROM users; (users2)
```

#### 偏移

Offset，指定开始返回记录前要跳过的记录数。

```go
db.Offset(3).Find(&users)
//// SELECT * FROM users OFFSET 3;

// -1 取消 Offset 条件
db.Offset(10).Find(&users1).Offset(-1).Find(&users2)
//// SELECT * FROM users OFFSET 10; (users1)
//// SELECT * FROM users; (users2)
```

#### 总数

Count，该 model 能获取的记录总数。

```go
db.Where("name = ?", "jinzhu").Or("name = ?", "jinzhu 2").Find(&users).Count(&count)
//// SELECT * from USERS WHERE name = 'jinzhu' OR name = 'jinzhu 2'; (users)
//// SELECT count(*) FROM users WHERE name = 'jinzhu' OR name = 'jinzhu 2'; (count)

db.Model(&User{}).Where("name = ?", "jinzhu").Count(&count)
//// SELECT count(*) FROM users WHERE name = 'jinzhu'; (count)

db.Table("deleted_users").Count(&count)
//// SELECT count(*) FROM deleted_users;

db.Table("deleted_users").Select("count(distinct(name))").Count(&count)
//// SELECT count( distinct(name) ) FROM deleted_users; (count)
```

**注意** `Count` 必须是链式查询的最后一个操作 ，因为它会覆盖前面的 `SELECT`，但如果里面使用了 `count` 时不会覆盖

#### Group & Having

```go
rows, err := db.Table("orders").Select("date(created_at) as date, sum(amount) as total").Group("date(created_at)").Rows()
for rows.Next() {
  ...
}

// 使用Scan将多条结果扫描进事先准备好的结构体切片中
type Result struct {
	Date time.Time
	Total int
}
var rets []Result
db.Table("users").Select("date(created_at) as date, sum(age) as total").Group("date(created_at)").Scan(&rets)

rows, err := db.Table("orders").Select("date(created_at) as date, sum(amount) as total").Group("date(created_at)").Having("sum(amount) > ?", 100).Rows()
for rows.Next() {
  ...
}

type Result struct {
  Date  time.Time
  Total int64
}
db.Table("orders").Select("date(created_at) as date, sum(amount) as total").Group("date(created_at)").Having("sum(amount) > ?", 100).Scan(&results)
```

#### 连接

Joins，指定连接条件

```go
rows, err := db.Table("users").Select("users.name, emails.email").Joins("left join emails on emails.user_id = users.id").Rows()
for rows.Next() {
  ...
}

db.Table("users").Select("users.name, emails.email").Joins("left join emails on emails.user_id = users.id").Scan(&results)

// 多连接及参数
db.Joins("JOIN emails ON emails.user_id = users.id AND emails.email = ?", "jinzhu@example.org").Joins("JOIN credit_cards ON credit_cards.user_id = users.id").Where("credit_cards.number = ?", "411111111111").Find(&user)
```

### Pluck

Pluck，查询 model 中的一个列作为切片，如果您想要查询多个列，您应该使用 [`Scan`](https://liwenzhou.com/posts/Go/gorm_crud/#Scan)

```go
var ages []int64
db.Find(&users).Pluck("age", &ages)

var names []string
db.Model(&User{}).Pluck("name", &names)

db.Table("deleted_users").Pluck("name", &names)

// 想查询多个字段？ 这样做：
db.Select("name, age").Find(&users)
```

### 扫描

Scan，扫描结果至一个 struct.

```go
type Result struct {
  Name string
  Age  int
}

var result Result
db.Table("users").Select("name, age").Where("name = ?", "Antonio").Scan(&result)

var results []Result
db.Table("users").Select("name, age").Where("id > ?", 0).Scan(&results)

// 原生 SQL
db.Raw("SELECT name, age FROM users WHERE name = ?", "Antonio").Scan(&result)
```

## 链式操作相关

### 链式操作

Method Chaining，Gorm 实现了链式操作接口，所以你可以把代码写成这样：

```go
// 创建一个查询
tx := db.Where("name = ?", "jinzhu")

// 添加更多条件
if someCondition {
  tx = tx.Where("age = ?", 20)
} else {
  tx = tx.Where("age = ?", 30)
}

if yetAnotherCondition {
  tx = tx.Where("active = ?", 1)
}
```

在调用立即执行方法前不会生成`Query`语句，借助这个特性你可以创建一个函数来处理一些通用逻辑。

### 立即执行方法

Immediate methods ，立即执行方法是指那些会立即生成`SQL`语句并发送到数据库的方法, 他们一般是`CRUD`方法，比如：

`Create`, `First`, `Find`, `Take`, `Save`, `UpdateXXX`, `Delete`, `Scan`, `Row`, `Rows`…

这有一个基于上面链式方法代码的立即执行方法的例子：

```go
tx.Find(&user)
```

生成的SQL语句如下：

```sql
SELECT * FROM users where name = 'jinzhu' AND age = 30 AND active = 1;
```

### 范围

`Scopes`，Scope是建立在链式操作的基础之上的。

基于它，你可以抽取一些通用逻辑，写出更多可重用的函数库。

```go
func AmountGreaterThan1000(db *gorm.DB) *gorm.DB {
  return db.Where("amount > ?", 1000)
}

func PaidWithCreditCard(db *gorm.DB) *gorm.DB {
  return db.Where("pay_mode_sign = ?", "C")
}

func PaidWithCod(db *gorm.DB) *gorm.DB {
  return db.Where("pay_mode_sign = ?", "C")
}

func OrderStatus(status []string) func (db *gorm.DB) *gorm.DB {
  return func (db *gorm.DB) *gorm.DB {
    return db.Scopes(AmountGreaterThan1000).Where("status IN (?)", status)
  }
}

db.Scopes(AmountGreaterThan1000, PaidWithCreditCard).Find(&orders)
// 查找所有金额大于 1000 的信用卡订单

db.Scopes(AmountGreaterThan1000, PaidWithCod).Find(&orders)
// 查找所有金额大于 1000 的 COD 订单

db.Scopes(AmountGreaterThan1000, OrderStatus([]string{"paid", "shipped"})).Find(&orders)
// 查找所有金额大于 1000 且已付款或者已发货的订单
```

### 多个立即执行方法

Multiple Immediate Methods，在 GORM 中使用多个立即执行方法时，后一个立即执行方法会复用前一个**立即执行方法**的条件 (不包括内联条件) 。

```go
db.Where("name LIKE ?", "jinzhu%").Find(&users, "id IN (?)", []int{1, 2, 3}).Count(&count)
```

生成的 Sql

```sql
SELECT * FROM users WHERE name LIKE 'jinzhu%' AND id IN (1, 2, 3)

SELECT count(*) FROM users WHERE name LIKE 'jinzhu%'
```

## 企业级项目结构拆分

根据模块的不同功能划分到同一个包中

```mark	
go_project
|-- 
|---controller   			控制器
|-----controller.go
|---static					静态文件
|-----img/css/js
|---templates				模板文件
|-----index.html……
|---dao 					数据库连接
|-----mysql.go/redis.go
|---models					模型。相当于java中的实体
|---login					业务逻辑层
|----增删改查操作 数据整合 逻辑判断等
|--main.go					启动文件
```

> url -> controller -> login -> model
>
> 请求  -> 控制器 -> 业务逻辑 -> 模型层的增删改查
