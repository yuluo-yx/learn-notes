# Node

## 简介

```markdown
- Node是对ES标准一个实现，Node也是一个JS引擎
- 通过Node可以使js代码在服务器端执行
- Node仅仅对ES标准进行了实现，所以在Node中不包含DOM 和 BOM	
- Node采用google开发的v8引擎运行js代码。
- Node中可以使用所有的内建对象
	String Number Boolean Math Date RegExp Function Object Array
	而BOM和DOM都不能使用
		但是可以使用 console 也可以使用定时器（setTimeout() setInterval()）
		
- Node可以在后台来编写服务器
	Node编写服务器都是单线程的服务器
	- 进程
		- 进程就是一个一个的工作计划（工厂中的车间）
	- 线程
		- 线程是计算机最小的运算单位（工厂中的工人）
			线程是干活的
			
- 传统的服务器都是多线程的
	- 每进来一个请求，就创建一个线程去处理请求
	
- Node的服务器单线程的
	- Node处理请求时是单线程，但是在后台拥有一个I/O线程池
```

## node执行js文件

`node + node文件名.js`

## 模块化简介

ECMAScript的缺陷：

- 没有模块系统

  > 模块化：降低代码之间的耦合，提高复用性
  >
  > - 在Node中，一个js文件就是一个模块
  >
  >   - 在Node中，每一个js文件中的js代码都是独立运行在一个函数中，而不是全局作用域，所以一个模块的中的变量和函数在其他模块中无法访问

- 标准库较少

- 没有标准接口

- 缺乏管理系统

CommonJS规范

- CommonJS规范的提出，主要是为了弥补当前JavaScript没有标准的缺陷

- 为js指定了一个美好的愿景，希望js能够在任何地方运行

- 对模块的定义十分简单

  1. 模块引用

     ```node
     在node中，通过require()函数来引入外部的模块
     	在require()中，可以传递一个文件的路径作为参数，node自动读入
     	这里的路径如果使用相对路径，必须以./..开头
     	使用reqiure引入模块之后，该函数会返回一个对象，这个对象代表的是引入的模块
     	
     	每个js模块之间，都是闭包关系，都是独立运行在一个函数中，想要访问模块内的属性或者方法，
     	必须要使用 exports 向外部暴露，
     	exports.x = "我是模块中的x"
     	exports.fn = function () {
     		// 函数体
     	}
     ```

  2. 模块定义

  3. 模块标识
  
     > 使用require引入外部模块时，使用的就是模块标识，可以通过模块标识来找到对应的模块
     >
     > - 模块分类
     >
     >   - 核心模块：由node引擎提供的模块，核心模块的标识就是模块名字
     >
     >     `var fs = require('fs')`
     >
     >   - 文件模块：用户自己定义的模块,文件模块的标识就是文件的路径（绝对路径、相对路径【使用./..开头】）
  
  在node中有一个全局对象 global，作用和网页中的window类似，在全局中创建的变量和函数都会作为global的属性和方法保存。
  
  在node js中声明全局变量的方法，不加变量名直接写字面量  `a = 10`
  
  在node中执行js的时候其实是运行在一个function中的，可以使用`console.log(arguments.callee + "")`打印函数
  
  在代码中加入
  
  ```js
  function (exports, require, module, _filename, _dirname) {
  
  	// 函数体
  
  }
  
  /*
  	exports: 该对象用来将变量或函数暴露到外部
  	require: 用来引入外部的模块
  	module: 代表的是当前module本身，exports是module的属性 `exports.`和`module.exports`效果是相同的
  	_filename: 当前模块的完整路径
  	_dirname：当前文件所在的文件夹路径
  */
  ```
  
  所以在js模块中写的变量都是局部变量
  
- `exports`和`module.exports`的区别

  > exports往外暴露的时候，只能通过exports.变量名的形式暴露
  >
  > module.exports可以直接赋值暴露出去，也可以通过.的方式
  >
  > ​	`module.exports.xxx = xxx`
  >
  > ​	`module.exports = {}`

## 包简介

包就是一个增强版的模块，对模块进行封装。

包实际上就是一个压缩文件，解压之后还原目录，符合规范的目录应该包含以下文件：

> - package.json  描述文件
>
>   > 对当前包的详细描述，开发依赖，作者，git仓库……
>   >
>   > 是json格式的文件，在包的根目录下，**json文件中不能出现注释**
>
> - bin    可执行二进制文件
>
> - lib   js代码
>
> - doc   文档
>
> - test   单元测试

包可以将一组相关的模块组合到一起，形成一组完整的工具。

CommonJS的包规范由两部分组成：包结构和包描述文件

## npm简介

NPM: node package manager，帮助完成第三方模块的发布，安装和依赖等，之间形成一个完整的生态系统。

> CommonJS包规范是理论，npm是其中的一种规范

npm命令

> npm -v  查看npm版本
>
> npm version  
>
> npm -search 包名		搜索模块包
>
> npm install 包名  安装模块包   安装时根据package.json安装，否则可能安装错位置
>
> npm init 创建package.json文件
>
> npm install 包名 --save   安装包并且添加到依赖中
>
> npm install 包名 -g  全局安装包（一般都是安装一些工具包）
>
> npm install   下载当前项目所依赖的包
>
> npm remove 包名 删除包

## cnpm配置

淘宝npm镜像

```bash
$ npm install -g cnpm --registry=https://registry.npmmirror.com
```

## node搜索包的流程

通过npm下载的包，引用的时候通过`var math = require("math")` 包名引入即可

node在使用模块名字来引入模块时，会首先在当前目录的node_modules中寻找是否含有该模块

如果由直接使用，没有去上一级目录中找，直到找到磁盘的根目录为止，如果都没有找到，则报错。

