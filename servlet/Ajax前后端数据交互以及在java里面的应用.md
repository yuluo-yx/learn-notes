# Ajax前后端数据交互以及在java里面的应用

*****

## 介绍

AJAX = Asynchronous JavaScript and XML（异步的 **JavaScript** 和 **XML**）。

AJAX 不是新的编程语言，而是一种使用现有标准的新方法。

AJAX 最大的优点是在不重新加载整个页面的情况下，可以与服务器交换数据并更新部分网页内容。

AJAX 不需要任何浏览器插件，但需要用户允许JavaScript在浏览器上执行。

> Asynchronous JavaScript + XML（异步JavaScript和XML）, 其本身不是一种新技术，而是一个在 2005年被Jesse James Garrett提出的新术语，用来描述一种使用现有技术集合的‘新’方法，包括: [HTML](https://developer.mozilla.org/en-US/docs/Web/HTML) 或 [XHTML](https://developer.mozilla.org/en-US/docs/Glossary/XHTML),  [CSS](https://developer.mozilla.org/zh-CN/docs/Web/CSS), [JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript), [DOM](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model), [XML](https://developer.mozilla.org/en-US/docs/Web/XML), [XSLT](https://developer.mozilla.org/en-US/docs/Web/XSLT), 以及最重要的 [`XMLHttpRequest`](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)。当使用结合了这些技术的AJAX模型以后， 网页应用能够快速地将增量更新呈现在用户界面上，而不需要重载（刷新）整个页面。这使得程序能够更快地回应用户的操作。
>
> 尽管X在Ajax中代表XML, 但由于[JSON](https://developer.mozilla.org/zh-CN/docs/Glossary/JSON)的许多优势，比如更加轻量以及作为Javascript的一部分，目前JSON的使用比XML更加普遍。JSON和XML都被用于在Ajax模型中打包信息。

## 优势

```js
 传统的web应用允许用户填写表单(form)，当提交表单时就向web服务器发送一个请求。服务器接收并处理传来的表单，然后返回一个新的网页。这个做法浪费了许多带宽，因为在前后两个页面中的大部分HTML代码往往是相同的。由于每次应用的交互都需要向服务器发送请求，应用的响应时间就依赖于服务器的响应时间。这导致了用户界面的响应比本地应用慢得多。
与此不同，AJAX应用可以仅向服务器发送并取回必需的数据，它使用SOAP或其它一些基于XML的web service接口，并在客户端采用JavaScript处理来自服务器的响应。因为在服务器和浏览器之间交换的数据大量减少，结果我们就能看到响应更快的应用。同时很多的处理工作可以在发出请求的客户端机器上完成，所以Web服务器的处理时间也减少了。
```

无需刷新页面与服务器端进行通信

允许你根据用户事件来更新部分页面的内容

## 缺点：

没有浏览历史

存在跨域问题（同源）

SEO【Search Engine Optimization】（搜索引擎优化）（爬虫）不友好 因为是动态从服务器拿到数据，再从浏览器渲染，原先的HTML页面中没有数据，爬虫爬不到数据（京东商城的商品数据）。

## Ajax基本使用

###  伪造Ajax（狂神）

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>伪造Ajax</title>
</head>
<body>

    <script type="text/javascript">
        window.onload = function f() {
            var myDate = new Date();
            document.getElementById('currentTime').innerText = myDate.getTime();
        }

        function loadPage() {
            var targetURL = document.getElementById('url').value;
            console.log(targetURL);
            document.getElementById('iframePosition').src = targetURL;
        }

    </script>

    <div>
        <p>请输入要加载的地址; <span id="currentTime"></span></p>
        <p>
            <input type="text" id="url" value="https://www.csdn.net/">
            <input type="button" value="提交" onclick="loadPage()">
        </p>
    </div>

    <div>
        <h3>
            加载页面的位置
        </h3>
        <iframe style="width: 100%; height: 500px" id="iframePosition">

        </iframe>
    </div>

</body>
</html>
```

使用JQuery提供的ajax，它的本质是XMLHttpRequest    即在浏览器networ选项中看到的xhr数据格式

Ajax的核心是XMLHttpRequest对象（XHR）, xhr相服务器发送请求和解析服务器上的请求文本，HTML，XML或者json，同时把这些外部数据直接载入网页的被选元素中。

## Express的基本使用（基于node js的前端框架）【尚硅谷】

- `使用 npm init --yes 对目录进行初始化操作`   产生一个package.json文件

- 安装express框架 npm i express

- ```js
  // 1, 引入express
  const express = require('express');
  
  // 2，创建应用对象
  const app = express();
  
  // 3, 创建路由规则
  // request  是对请求报文进行封装
  // response 是对响应报文进行的封装
  app.get('/', (request, response)=>{
      // 设置响应
      response.send('Hello Express!');
  });
  
  // 4，创建端口启动服务
  app.listen(8000, ()=>{
      console.log("服务已经启动，8000端口监听中…………");
  })
  
  
  ```

- 🚀  node .\express前端框架的基本使用.js
  服务已经启动，8000端口监听中…………

- 浏览器中输入127.0.0.1：8000进行查看

## Ajax案例

### get请求

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="">
    <title> Ajax GRT 请求 </title>
    <style>
        #result{
            width:200px;
            height:100px;
            border:solid 1px #90b
        }
    </style>
<head>
<body>
    <button>点击发送请求</button>
    <div id="result"></div>

    <script>
        // 获取button元素
        const btn = document.getElementsByTagName('button')[0];
        const result = document.getElementById("result");
        // 绑定事件
        btn.onclick = function(){

            // 1,创建对象
            const xhr = new XMLHttpRequest();
            // 2, 初始化，设置请求方法和url
            xhr.open('GET', 'http://127.0.0.1:8000/server');
            // 3, 发送
            xhr.send();
            // 4, 事件绑定 处理服务器端返回的结果
            // on when 当……时候
            // readystatechange 是xhr对象中的属性，
            // 表示状态 0 1 2 3 4 分别表示1，2，3，4步骤的返回结果
            // chanege 改变
            xhr.onreadystatechange = function(){
                // 判断（服务器端返回了所有的结果）
                if (xhr.readyState === 4) {
                    // 判断响应状态码 200 404 403 401 500
                    if (xhr.status >= 200 && xhr.status < 300) {
                        // 处理结果  行 头 空行 体
                        // 响应
                        console.log(xhr.status);// 状态码
                        console.log(xhr.statusText);// 响应状态字符串
                        console.log(xhr.getAllResponseHeaders);// 所有响应头
                        console.log(xhr.response);// 响应体

                        // 设置 result 的文本
                        result.innerHTML = xhr.response;

                    } else {

                    }
                }

            }
            // console.log('test');

        }
    </script>

<body>
</html>

```

```javascript
// 1, 引入express
const express = require('express');

// 2，创建应用对象
const app = express();

// 3, 创建路由规则
// request  是对请求报文进行封装
// response 是对响应报文进行的封装
app.get('/server', (request, response)=>{
    // 设置响应头 设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*' )

    // 设置响应体
    response.send('Hello Ajax!');
});

// 4，创建端口启动服务
app.listen(8000, ()=>{
    console.log("服务已经启动，8000端口监听中…………");
})
```

###  Ajax设置 get 请求参数

- 网址之后加?name=value&name=value
- ？号跟参数，参数之间用&符号连接

### post请求

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ajax POST 请求</title>
    <style>
        #result{
            width:200px;
            height:100px;
            border:solid 1px #903
        }
    </style>
</head>
<body>
    <div>Ajax的POST请求</div>
    <div id="result"></div>

    <script>
        // 获取元素对象
        const result = document.getElementById("result");
        // 绑定事件  当鼠标移动到框内的时候触发fhuction
        result.addEventListener("mouseover", function(){
            //console.log("test");
            // 1，创建对象
            const xhr = new XMLHttpRequest();
            // 2，初始化 设置请求参数和url
            xhr.open('POST', 'http://127.0.0.1:8000/server');
            // 3，发送
            xhr.send();
            // 4，事件绑定 处理服务器端返回的结果
            xhr.onreadystatechange = function() {
                // 判断
                if (xhr.readyState === 4) {
                    if (xhr.status >= 200 && xhr.status < 300) {
                        // 处理服务端返回的结果
                        result.innerHTML = xhr.response;
                    }
                }
            }
        });
    </script>
</body>
</html>
```

```javascript
// 1, 引入express
const express = require('express');

// 2，创建应用对象
const app = express();

// 3, 创建路由规则
// request  是对请求报文进行封装
// response 是对响应报文进行的封装
app.get('/server', (request, response)=>{
    // 设置响应头 设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*' )

    // 设置响应体
    response.send('Hello Ajax!');
});

app.post('/server', (request, response)=>{
    // 设置响应头 设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*' )

    // 设置响应体
    response.send('Hello Ajax POST!');
});

// 4，创建端口启动服务
app.listen(8000, ()=>{
    console.log("服务已经启动，8000端口监听中…………");
})
```

### Ajax设置post请求参数

- post请求的参数需要在send里面设置
- 以url的参数形式进行设置

```html
// 3，发送
// 设置post请求的参数
// xhr.send('a=100&b=200&c=300');
// 可以设置任何格式的请求,前提是服务端可以处理相应的请求
// 实际开发中按照相应格式进行设置
xhr.send('a:100&b:200&c:300');
```

## Ajax设置请求头信息

```html
// 2，初始化 设置请求参数和url
xhr.open('POST', 'http://127.0.0.1:8000/server');
// 设置请求头
// Content-Type:设置请求体类型
// application/x-www-form-urlencoded
xhr.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
// 也可以自己设置请求头信息  要在后端进行相应的设置 可能会报错
//xhr.setRequestHeader('name', 'yuluo')
```

- 自己设置请求头信息时要在后端进行相应的设置  (后端开发负责)

```JS
// // all可以接受任意类型的请求
// app.all('/server', (request, response)=>{
//     // 设置响应头 设置允许跨域
//     response.setHeader('Access-Control-Allow-Origin', '*' )

        // 设置响应头
//        response.setHeader('Access-Control-Allow-Header')
//     // 设置响应体
//     response.send('Hello Ajax POST!');
// });
```

## Ajax响应json数据

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ajax JSON响应</title>
    <style>
        #result{
            width: 200px;
            height: 100px;
            border: solid 1px #232;
        }
    </style>
</head>
<body>
    <div>JSON响应</div>
    <div id="result"></div>
    <script>
        // 绑定键盘按下事件
        window.onkeydown = function() {
            // 测试
            // onsole.log("test");
            // 发送请求
            const xhr = new XMLHttpRequest();
            // 设置响应体数据的类型
            xhr.responseType = 'json';
            // 初始化 设置请求参数和url
            xhr.open('GET', 'http://127.0.0.1:8000/json-server');
            // 发送
            xhr.send()
            // 事件绑定 处理服务端返回的结果
            xhr.onreadystatechange = function() {
                if (xhr.readyState === 4) {
                    if (xhr.statusText >= 200 && xhr.status < 300) {
                    // 
                    // console.log(xhr.response);
                    // result.innerHTML = xhr.response;
                    // 对后端传过来的数据手动的进行转换
                    // let data = JSON.parse(xhr.response);
                    // result.innerHTML = data.name;

                    // 自动转换
                    console.log(xhr.response);
                    result.innerHTML = xhr.response.name;
                    }
                }
            }
        }
    </script>
</body>
</html>
```

```js
app.all('/json-server', (request, response)=>{

    response.setHeader('Access-Control-Allow-Origin', '*' )

    // 响应一个数据
    const data = {
        name: 'yuluo',
        age: '12',
        girlfriend: 'huakai'
    };

    // 对对象进行字符串转化
    let str  = JSON.stringify(data)

    // 响应体只能响应str和buffer
    // response.send('Hello Ajax JSON!');
    response.send(str);
});
```

## nodemon自动重启工具的安装

- 自动帮我们重新启动服务
- `🚀  npm install -g nodemon`

- 之后开始用nodemon server.js启动服务

## Ajax的IE缓存问题

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IE缓存问题</title>

    <style>
        #result{
            width: 100px;
            height: 100px;
            border: solid 1px #258;
        }
    </style>
</head>
<body>
    <!-- /浏览器会缓存Ajax的请求内容，造成刷新的时候不是服务器最新的数据 -->
    <div>IE缓存问题</div>
    <button>点击发送请求</button>
    <div id="result"></div>

    <script>
        const btn = document.getElementsByTagName('button')[0];
        const result = document.querySelector('#result');

        btn.addEventListener('click', function(){
            // console.log('test');
            const xhr = new XMLHttpRequest();
            xhr.open('GET', 'http://127.0.0.1:8000/ie')
            xhr.send();
            xhr.onreadystatechange = function() {
                if (xhr.readyState === 4) {
                    if (xhr.status >= 200 && xhr.status < 300) {
                        result.innerHTML = xhr.response;
                    }
                }
            }
        })
    </script>
</body>
</html>
```

```js
// 针对 ie 缓存
app.get('/ie', (request, response)=>{
    // 设置响应头 设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*' )

    // 设置响应体
    response.send('Hello ie cache -2test!');
});
```

- 解决方案就是加上时间戳，每次都是不同的请求

`  xhr.open('GET', 'http://127.0.0.1:8000/ie?t='+Date.now())`

## Ajax请求超时与网络异常处理

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>请求超时与网络异常处理</title>
    <style>
        #result{
            width:200px;
            height:100px;
            border: solid 1px #90b;
        }
    </style>

</head>
<body>
    <button>点击发送请求</button>
    <div id="result"></div>

    <script>
        const btn = document.getElementsByTagName('button')[0];
        const result = document.querySelector('#result');

        btn.addEventListener('click', function(){
            
            const xhr = new XMLHttpRequest();
            
            // 设置超时两秒取消请求
            xhr.timeout = 2000;
            // 超时回调
            xhr.ontimeout = function() {
                alert("网络异常,请稍后重试!")
            }
            // 网络异常回调设置
            xhr.onerror = function() {
                alert("你的网络似乎出了点问题,请检查网络设置")
            }

            xhr.open("GET", 'http://127.0.0.1:8000/delay')
            xhr.send();
            xhr.onreadystatechange = function() {
                if(xhr.readyState === 4) {
                    if (xhr.status >= 200 && xhr.status < 300) {
                        result.innerHTML = xhr.response;
                    }
                }
            }

        })       
    </script>

</body>
</html>
```

```js
// 延时响应 
app.get('/delay', (request, response)=>{
    // 设置响应头 设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*' )

    // 设置延时3秒响应
    setTimeout(() =>{
        response.send('延时响应');
    }, 3000)
});
```

## Ajax取消请求

```html
// 手动取消Ajax的请求
// 需要借助abort方法
btn[1].onclick = function() {
	x.abort()
}
```

## Ajax重复请求问题

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>重复请求问题</title>

</head>
<body>
    <button>发送请求</button>

    <script>
        const btn = document.querySelectorAll('button');
        let x = null;

        // 标识变量
        // false表示是否正在发送AJAX请求
        let isSending = false;

        btn[0].onclick = function(){
            // 判断标示变量
            if (isSending) {
                // 如果正在发送,则取消该请求,创建一个新的请求
                x.abort();
            }
            x = new XMLHttpRequest()
            // 修改标识变量的值
            isSending = true;
            x.open("GET", 'http://127.0.0.1:8000/delay')
            x.send();
            
            x.onreadystatechange = function() {
                if (x.readyState === 4) {
                    isSending = false;
                }
            }
        }

    </script>

</body>
</html>
```

## jQuery发送Ajax请求

- 前端

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>jQurey 发送 AJAX 请求</title>
    <link href="https://cdn.bootcdn.net/ajax/libs/animate.css/4.1.1/animate.min.css" rel="stylesheet">
    <!-- <script src="https://cdn.bootcdn.net./ajax/libs/jquery/3.5.1/jquery.min.js"></script> -->
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.6.0/jquery.js"></script>
</head>
<body>
    <div class="container">
        <h2 class="page-header">jQuery发送Ajax请求 </h2>
        <button class="btn btn-primary">GET</button>
        <button class="btn btn-danger">POST</button>
        <button class="btn btn-info">通用型方法发送Ajax请求</button>
    </div>

    <!-- net::ERR_CONNECTION_REFUSED 后台的服务器没有开导致的 -->
    <script>
        $('button').eq(0).click(function(){
            $.get('http://127.0.0.1:8000/jquery-ajax', {a:100, b:200}, function(data){
                console.log(data)
            }, 'json');
        })
        $('button').eq(1).click(function(){
            $.get('http://127.0.0.1:8000/jquery-ajax', {a:100, b:200}, function(data){
                console.log(data);
            });
        })
        // 通用方法发送ajax请求
        $('button').eq(2).click(function(){
            $.ajax({
                // url
                //url: 'http://127.0.0.1:8000/jquery-server',
                // 为了测试失败回调，将连接改到delay
                url: 'http://127.0.0.1:8000/delay',
               // 参数
               data: {a:100, b:200},
               //  请求类型
               type: 'GET',
               // 响应体结果类型
               dataType: 'json',
               // 成功之后的回调
               success: function(data) {
                   console.log(data);
               },
               // 超时时间
               timeout: 2000,
               // 失败的回调
               error: function() {
                   console.log("出错了");
               },
               // 头信息
               headers: {
                   c:300,
                   d:400
               }
            });
        });
    </script>

</body>
</html>
```

- 后端

```js
// 1, 引入express
const express = require('express');

// 2，创建应用对象
const app = express();

// jquery发送ajax请求
app.all('/jquery-ajax', (request, response)=>{
    // 设置响应头 设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*' )

    let data = {name: 'yuluo'};

    // let str = JSON.stringify(data) 将json对象转换成为str send只发送send和buffer
    response.send(JSON.stringify(data));

});

// post和get以及通用方法发送ajax请求
app.all('/jquery-server', (request, response)=>{
    // 设置响应头 设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*' )

    let data = {name: 'huakai'};

    // let str = JSON.stringify(data) 将json对象转换成为str send只发送send和buffer
    response.send(JSON.stringify(data));

});

// 延时响应 
app.all('/delay', (request, response)=>{
    // 设置响应头 设置允许跨域
    response.setHeader('Access-Control-Allow-Origin', '*' )

    // 设置延时3秒响应
    setTimeout(() =>{
        response.send('jquery ajax');
    // }, 3000)
    }, 1000)
});

// 4，创建端口启动服务
app.listen(8000, ()=>{
    console.log("服务已经启动，8000端口监听中…………");
})



```

## Axios发送Ajax请求

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Axios发送ajax请求</title>
    <!-- 引入axios -->
    <script crossorigin="anonymous" src="https://cdn.bootcdn.net/ajax/libs/axios/0.24.0/axios.js"></script>
</head>
<body>
    <button>GET</button>
    <button>POST</button>    
    <button>通用方发送Ajax请求</button>

    <script>
        // 获取三个按钮
        const btns = document.querySelectorAll('button');

        // 配置baseUrl
        axios.defaults.baseURL = 'http://127.0.0.1:8000';

        btns[0].onclick = function() {
            // GET请求
            // axios.get('http:127.0.0.1:8000/axios-server',{
                // 配置了baseURL之后就可可以不用加了
                axios.get('/axios-server',{
                // url参数
                params: {
                    id: 100,
                    vip: 7
                },
                // 设置请求头信息
                headers: {
                    name: 'yuluo',
                    age: '20'
                }
            // 回调
            }).then(value => {
                console.log(value);
            });
        }

        btns[1].onclick = function() {
            axios.post('/axios-server', {
                // 请求体
                username: 'admin',
                password: 'admin'
                }, {
                // url
                params: {
                    id: 200,
                    vip: 9
                },
                // 请求头参数
                headers: {
                    height: 180,
                    weight: 200
                }, 
            })
        }

        // 通用方式发送Ajaxq请求
        btns[2].onclick() = function() {
            axios({
                // url
                url: '/axios-server',
                // url参数
                params: {
                    vip: 10,
                    level: 30
                },
                // 头信息
                headers: {
                    a: 100,
                    b: 200
                },
                //请求体参数
                data: {
                    usernemr: 'admin',
                    password: 'admin'
                }
            }).then(response=> {
                // 响应
                console.log(respone);
                // 响应状态码
                console.log(response.status);
                // 响应体
                console.log(response.statusText)
            })
        }


    </script>

</body>
</html>
```

## fetch函数发送Ajax请求

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <button>Ajax请求</button>
    <script>
        const btn = document.querySelector('button');

        btn.onclick = function() {
            fetch('http://127.0.0.1:8000/fetch-server',{
                // 请求方法
                method: 'POST',
                // 请求头
                headers: {
                    name: 'yuluo'
                },
                // 请求体
                body: 'username=admin&password=admin'
            }).then(Response=>{
                // 获得响应体中的文本
                // 获得json对象
                // console.log(respone.json())
                console.log(Response.text())
            });
        }
    </script>
</body>
</html>
```

## 跨域之Ajax同源策略

- 同源策略是浏览器的一种安全策略
- **同源：协议，域名，端口号 必须完全相同**
- 违背同源策略就是**跨域**

> a.com 向 b.com发送请求  
>
> 不同端口之间发送请求  300 --> 8000
>
> 属于跨域操作

## 如何解决跨域问题

### jsonp

- jsonp （json with padding ），是一个非官方的跨域解决方案，纯粹凭借程序员的只会开发出来的，**只支持get请求**

- 在网页中一些标签天生居于跨域的能力 比如: im link iframe script
- jsonp就是利用script标签的跨域能力来进行发送请求的

### JSONP的使用

- 动态的创建一个script标签
  - var script = douument.createElement("script");
- 设置 script 的 src ， 设置回调函数
  - script.src = "http://localhost:8000/textAJAX?callback=abc";
- 返回的是一个js函数的执行结果或者js代码

```HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>原理演示</title>

    <style>
        #result{
            width: 300px;
            height: 100px;
            border: solid 1px #90b;
        }
    </style>

</head>
<body>
    
    <div id="result"></div>

    <script>  
    // 对数据的处理也可以放在前端      
    function handle(data) {
        // 获取result元素
        const result = document.getElementById('result');
        result.innerHTML = data.name;
    }
    </script>

    <!-- <script src="http://127.0.0.1:5500/%E8%B7%A8%E5%9F%9F/app.js"></script> -->
    <script src="http://127.0.0.1:8000/jsonp-server"></script>
</body>
</html>
```

```JS
let data = {
    name: 'yuluo'
};

// console.log(data);

// 处理数据
// function handle(data) {
//     // 获取result元素
//     const result = document.getElementById('result');
//     result.innerHTML = data.name;
// }

handle(data);
```

```JS
// jsonp服务响应
app.all('/jsonp-server', (request, response) =>{
    response.send(console.log('hello jsonp-server'))
});
```

### 原生jsonp的实践 

- 在前端输入用户名，后端进行验证，不存在返回用户名不存在并且输入框变红

- ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>原生jsonp的实践</title>
  </head>
  <body>
      用户名: <input type="text" id="username">
      <!-- 显示后端传送过来的数据 -->
      <p></p>
      <script>
          // 获取 input 元素
          const input = document.querySelector('input');
          // 获取 p 元素
          const p = document.querySelector('p')
  
          // 数据处理函数 声明handle函数
          function handle(data) {
              input.style.border = "solid 1px #f00";
              // 修改p标签的提示文本
              p.innerHTML = data.msg;
          }
  
          // 绑定事件
          input.onblur = function() {
              // 获取用户的输入值
              let username = this.value;
              // 向服务端发送请求，检测用户名是否存在
              // 1， 创建scritp标签
              const script =document.createElement('script');
              // 2, 设置标签的src属性
              script.src = 'http://127.0.0.1:8000/check-username';
              // 3，将script 插入到文档中
              document.body.appendChild(script);
          }
      </script>
  </body>
  </html>
  ```

- ```js
  // 1, 引入express
  const express = require('express');
  
  // 2，创建应用对象
  const app = express();
  
  // 3, 创建路由规则
  // request  是对请求报文进行封装
  // response 是对响应报文进行的封装
  app.get('/check-username', (request, response)=>{
      // 设置响应
      const data = {
          exist: 1,
          msg: '用户名已经存在'
      };
      // 将数据转换为字符串
      let str = JSON.stringify(data);
      // 返回结果
      response.end(`handle(${str})`);
  });
  
  // 4，创建端口启动服务
  app.listen(8000, ()=>{
      console.log("原生jsonp的实践的服务已经启动，8000端口监听中…………");
  })
  ```

## CORS官方跨域请求

- CORS：跨域资源共享

- 特点：不不要再客户端进行任何操作，完全在服务端处理 支持get和post请求

- 工作原理：通过设置一个响应头来告诉浏览器，该请求允许跨域，浏览器收到该响应后就会对该响应放行

- ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
      <meta charset="UTF-8">
      <meta http-equiv="X-UA-Compatible" content="IE=edge">
      <meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>cors跨域请求</title>
      <style>
          #result{
              width: 500px;
              height: 300px;
              border: solid 1px #124
          }
      </style>
  </head>
  <body>
      <button>发送请求</button>
      <div id="result"></div>
  
      <Script>
          const btn = document.querySelector('button');
  
          btn.onclick = function() {
              // 1，创建对象
              const xhr = new XMLHttpRequest();
              // 2，初始化设置
              xhr.open("GEt", "http://127.0.0.1:8000/cors-server");
              // 3，发送
              xhr.send();
              // 4，处理服务器返回的结果
              xhr.onreadystatechange = function() {
                  if (xhr.readyState === 4) {
                      if (xhr.status >= 200 && xhr.status < 300) {
                          // 输出响应体
                          console.log(xhr.response);
                      }
                  }
              }
          }
      </Script>
  
  </body>
  </html>
  ```

- ```js
  // 1, 引入express
  const express = require('express');
  
  // 2，创建应用对象
  const app = express();
  
  // 3, 创建路由规则
  app.all('/cors-server', (request, response)=>{
      // 设置响应头
      // * 处是指定可以跨域请求的响应头 * 表示通配
      response.setHeader("Access-Control-Allow-Origin", "*")
      // 请求头
      response.setHeader("Access-Control-Allow-Headers", "*")
      // 请求方法
      response.setHeader("Access-Control-Allow-Method", "*")
      // 这样写只有5500这个端口可以发送请求
      // response.setHeader("Access-Control-Allow-Origin", "http://127.0.0.1:5500")
  
      response.send('hello CORS server');
  });
  
  // 4，创建端口启动服务
  app.listen(8000, ()=>{
      console.log("原生jsonp的实践的服务已经启动，8000端口监听中…………");
  })
  
  
  ```

  
