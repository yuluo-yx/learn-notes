# JavaScript进阶web APIs

作用：使用JS去操作html和浏览器

分为DOM【文档对象模型】和BOM【浏览器对象模型】

## DOM

### DOM简介

DOM【Document Object Model】：用来呈现以及与任意HTML或XML文档交互的API

简单理解就是浏览器提供的一套专门用来操作网页内容的功能

DOM作用：

- 开发网页内容特效和实现用户交互

DOM树：

- 将HTML文档以树的结构直观的表现出来，我们称之为文档书或者DOM树
- 描述网页内容关系的名词
- 作用：文档树可以直观的体现出标签与标签之间的关系

DOM对象：

- 浏览器根据html标签生成的js对象
- 所有的标签都可以在这个对象上面找到
- 修改这个对象的属性会自动映射到标签身上

DOM对象和html标签的关系：

> HTML标签在浏览器里叫标签
>
> 从html文档里获取出来叫做对象
>
> - console.dir()，打印对象
> - console.log(typeof btn)，打印btn的类型

DOM的核心思想：把网页内容当作对象来处理

document：最大的js对象。

### 获取DOM对象

#### 根据css选择器来获取DOM元素（重点）

选择匹配的第一个元素：`document.querySelector('css选择器')`

参数：一个或多个有效的css选择器【和css语法相同，类就是.xxx, id就是#xxx, 后代就是’ xxx‘】

返回值：css选择器匹配的**第一个元素**，一个HTMLElement对象，没有匹配到，返回null。

**可以直接修改获取到的对象**

选择匹配的多个元素：`ducument.querySelectorAll('css选择器')`和第一个类似

返回值：得到的是一个伪数组，有长度有索引，没有pop()和push()等数组方法

**不能直接修改，需要遍历（for）获得**

#### 其他获取DOM元素方法（了解）

`document.getElementById('id')`

`document.getElementByTagName()`

……

### 设置/修改DOM元素内容

`document.write()`方法

- 只能将文本内容追加到</body>前面的位置
- 文本中包含的标签会被解析

`对象.innerText属性`

- 将文本内容添加/更新到任意标签位置
- 文本中包含的标签不会被解析

`对象.innerHTML属性`

- 将文本内容添加/更新到任意标签位置
- 文本中包含的标签会被解析

###  设置/修改DOM元素属性

修改图片标签的src属性、

```html
<img src='./1.webp' alt='' title=''>

<script>
	// 1、获取元素 图片
    let pic = document.querySelector('img')
	// 2、修改元素属性 src  对象.属性 = 值
    pic.sc = './2.webp'
    pic.title = 'yuluo';
</script>
```

### 设置/修改元素的样式属性

- 比如通过轮播图小圆点自动更换颜色样式
- 点击按钮可以滚动图片，这是移动图片位置，left等等

通过style属性操作css

```html
<div>
    
</div>

<script>
// 对象.style.样式属性 = 值
// 获取元素
let box = document.querySelector('div')

// 改变背景颜色 样式 style
box.style.background = 'hotpink';
</script>
```

> 注意点：
>
> 1. 修改样式通过style属性引出
> 2. 如果属性有-连接符，需要转换为小驼峰命名法（margin-top === marginTop）
> 3. 赋值的时候，不要忘记加css单位，px……
> 4. 设置的样式是行内样式
> 5. 适合修改少量的样式

操作类名操作css:

如果修改的样式过多，直接通过style属性修改比较繁琐，我们可以通过css类名的形式。

```html
<script>
// active是类名
元素.className = 'active'
</script>
```

> 注意：
>
> 1. 因为class是关键字，所以使用className去代替
>
> 2. className是使用新值取替换旧值，如果需要添加一个类，需要保留之前的类名
>
>    `元素.className = 'oldClassName newClassName'`
>
> 3. 这样的写的样式添加到了css中

通过classList操作类控制css：

为了解决className容易覆盖之前类名的问题，我们可以通过classList方式追加和删除类名

```html
<script>
	// 追加一个类
    元素.classList.add('类名')
    // 删除一个类
    元素.classList.remove('类名')
    // 切换一个类
    元素.classList.toggle('类名')
</script>
```

### 设置/修改元素的表单属性

表单很多情况，也需要修改属性。比如点击密码输入框的眼睛，可以看到眼睛，本质是将**表单类型转换为文本框**

```html
<body>
    <input class="password" type="text" value="请输入密码">

    <script>
        // 获取元素
        let input = document.querySelector('input')
        // 取值或设置值  得到input里面的value
        // console.log(input.value)
        // 设置值
        input.value = '密码'
    </script>

</body>
```

表单属性中添加就有效果，移除就没有效果，一律使用布尔值表示，如果为true代表添加了该属性，false则代表移除了该属性

比如：disabled【不可用的】、checked【是否被选中】、selected【选中】

```html
    <!-- 代表此按钮不可用 -->
    <button disabled>按钮</button>
        
	// 获取元素
    let btn = document.querySelector('button')
    // 更改元素状态为可用  可以用js控制标签状态
    btn.disabled = false;
```

### 定时器-间歇函数

网页中经常会用到一种功能，每隔一段时间自动执行一段代码，不需要我们手动取触发。

比如：网页中的倒计时、用户协议……要实现这种需求，需要使用定时器函数。

定时器函数有两种，第一种是间歇函数

```markdown
1、开启定时器
setInterval(函数，间隔时间)
2、特点
	作用：每隔一段时间调用这个函数
	间隔时间单位是毫秒
3、关闭定时器
返回值是数字型的，
let timer = setInterval(函数， 间隔时间)
// 清除定时器
clearInterval(timer)
```

```html
<script>
    // 匿名函数
	setInterval(function (){
        console.log("yuluo,,,,")
    }, 1000)
</script>
```

## 事件

学习目标：能够给DOM元素添加事件监听

### 简介

定义：事件是在编程时系统内发生的动作或者发生的事情

比如用户再网页上单击一个按钮，鼠标移动，拖拽……

事件监听：

>  让程序检测是否有事件产生，一旦有事件触发，就立即调用一个函数做出响应，也成为 `注册事件`

### 语法：

> `元素.addEventListener('事件', 要执行的函数)`

### 事件监听三要素：

- 事件源：那个dom元素被事件触发了，要获取dom元素
- 事件，用什么方式触发，比如鼠标单击 clibk，鼠标经过mouseover等
- 事件调用的函数：要做什么事

注意：

> 1、事件类型要加引号
>
> 2、函数是点击之后再去执行，每次点击都会执行一次

### 事件监听版本：

DOM L0：事件源.on事件 = function () {}

```html
        startBtn.addEventListener('click', function () {})
```



DOM L2：事件源.addEventListener(事件，事件处理函数)

```html
        startBtn.onclick = function () {
            alert("222")
        }
```

![image-20220726213938196](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220726213938196.png)

### 事件类型

鼠标事件: 鼠标触发

- click：鼠标点击
- mouseenter：鼠标经过
- mouseleave：鼠标离开

焦点事件：表单获得光标

- focus：获得焦点
- blur：失去焦点

键盘事件：键盘触发

- keydown：键盘按下触发
- keyup：键盘抬起触发

文本事件：表单输入触发

- input：用户输入事件

## 高阶函数

目标：掌握高阶函数用法，实现函数的更多使用形式

高阶函数：

> 可以被简单理解为函数的高级应用，JavaScript中函数可以被当成【值】来对待，基于这个特性实现函数的高级应用。

```js
let num = 10
// 函数表达式  高阶函数，把函数当成值来用
let fn = function() {}
```

### 回调函数

将函数A作为参数传递给函数B时，我们称函数A为回调函数

简单理解：当一个函数当作参数来传递给另外一个函数的时候，这个函数就是回调函数

```js
function fn() {
    console.log("我是回调函数……")
}

// fn为回调函数  回头去调用的函数
setInterval(fn, 1000)

// function也是回调函数
box.addEventListener('click', function () { })
```

## 环境对象  this

能够分析判断函数运行再不同环境中this所处的环境

> 环境对象指的是函数内部特殊的变量this，它代表这当前函数运行时所处的环境

作用：弄清除this的指向，可以让我们更加简洁

- 函数的调用方式不同，this指代的对象也不同
- 【谁调用，this就是谁】时判断this指向的粗略规则

```js
function fn() {
    console.log(this)
}

// this指的是window对象
windos.fn()

// this指向的是btn
let btn = document.querySelector('btn')
btn.fn()
```



## 节点操作

### DOM节点

DOM树中的每一个内容都称之为节点

![image-20220727094804471](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220727094804471.png)

节点类型

- **元素节点**
  1. 所有的标签  比如 body、div
  2. html是根节点
- 属性节点
  1. 所有的属性比如 href
- 文本节点
  1. 所有的文本
- 其他

### 查找节点

节点关系：父节点，兄弟节点，子级节点

#### 父节点查找

parentNode 属性

返回最近一级的父节点，找不到返回null

`子元素.parentNode`

#### 子节点查找

childNodes: 获得所有子节点、包括文本节点（空格、换行）、注释节点等

children【重点】：

- 仅获得所有的元素节点
- 返回的是一个伪数组

`父元素.children`

#### 兄弟关系查找

1、下一个兄弟节点：

`nextElementSibling`属性

2、上一个兄弟节点：

`previousElementSibling`属性

### 增加节点

新增节点步骤：

- 创建一个新的节点
- 把创建的新的节点放入到指定的元素内部

创建元素节点：`document.createElement('标签名')`

追加到父元素内部的最后一个子元素：`父元素.appendChild(''创建的标签)`

插入到父元素中某个子元素的前面：`父元素.insertBefore(要插入的子元素，在那个元素前面)`

### 克隆和删除节点

克隆节点：`元素.cloneNode(布尔值)`， cloneNode会克隆出一个跟源标签一样的元素，括号内传入布尔值

- 若为true，则代表克隆时会包含后代节点一起克隆
- 若为false，则代表克隆时不包含后代节点
- 默认为false
- 追加和以前一样

删除节点：在js原生的DOM操作中，要删除元素必须通过父元素删除

语法：	`父元素.removeChile(要删除的元素)`

- 若不存在父子关系则删除不成功
- 删除节点和隐藏节点是有区别的，隐藏节点还是存在的，但是删除，则从html中删除节点

## 时间对象

目标：掌握时间对象，可以让网页显示时间

作用：可以得到当前系统的时间

### 实例化

通过`new`关键字实例化时间对象

获取当前时间：`let date = new Date()`

获得指定时间：let now = new Date('2022-7-26 18:30:00')

### 时间对象方法

![image-20220727154624812](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220727154624812.png)

```js
// new 实例化时间对象
let date = new Date()

consoloe.log(date.getFullYear())
// 月份取值为0-11
consoloe.log(date.getMonth() + 1)
```

获取当前时间：

```js
        let arr = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六']
        let div = document.querySelector('div')

        // 可以在刷新页面就调用函数获取日期，避免间歇空白
        getTime()

        // 定时器在1秒钟之后才调用函数，刷新页面会出现1秒钟的间歇
        setInterval(getTime, 1000)

        function getTime() {
            let date = new Date()
            let year = date.getFullYear()
            let month = date.getMonth() + 1
            let date1 = date.getDate()
            let hour = date.getHours()
            let min = date.getMinutes()
            let seconds = date.getSeconds()
            let days = date.getDay()

            div.innerHTML = `今天是: ${year}年${month}月${date1}日${hour}:${min}:${seconds} ${arr[days]}`
        }
```

### 时间戳

定义：是指从1970年01月01日00时00分00秒起之现在的毫秒数，是一种特殊的计量时间的方式

场景：计算倒计时

三种方式获取时间戳：

> 1、console.log(newDate().getTime())：得到的是一串数字
>
> 2、console.log(+new Date())
>
> 3、console.log(Date.now())：这种方法只能得到当前的时间戳，前面两种可以指定时间的时间戳

时间戳转换公式：

> d = parseInt(总秒数 / 60 / 60 /24)  天数
>
> h = parseInt(总秒数 / 60 / 60 % 24) 小时
>
> m = parseInt(总秒数 / 60 % 60)  分数
>
> s = parseInt(总秒数 % 60) 秒数

## 重绘和回流

### 浏览器是如何进行界面渲染的

![image-20220727173343613](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220727173343613-16589144257551.png)

![image-20220727173359045](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220727173359045.png)

### 重绘

由于节点（元素）的样式的改变并不影响它在文档流中的位置和文档布局时（比如：color、background-color、outline等）。

### 回流（）重排

当Render Tree中部分或者全部元素的尺寸、结构、布局等发生变化时，浏览器就会重新渲染部分或者全部文档的过程。

> 会导致回流的操作
>
> - 激活css伪类：：hover
> - 盒子大小变化
> - 字体大小
> - padding

简单理解就是影响到页面布局，就会有回流

### 关系

有回流一定有重绘，重绘不一定有回流

## 事件对象和属性

### 获取事件对象

简介：

- 也是一个对象，这个对象里有事件触发时的相关信息
- 鼠标点击事件时，事件对象中就存了鼠标点在那个位置等信息

如何获取：

- 在事件绑定的回调函数中的第一个参数就是事件对象
- 一般命名为event、ev、e

语法：

`元素.addEventListener('click', function (e) { })`

### 事件对象常用属性

type: 获取当前的事件类型

clientX/clientY: 获取相对于浏览器可见窗口左上角的位置

offsetX/offsetY：获取光标相对于DOM元素左上角的位置

key：用户按下的键盘键的值，现在不提倡使用keyCode

## 事件流以及两个阶段

事件流指的是事件完整执行过程中的流动路径

![image-20220728090816227](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220728090816227.png)

两个阶段分为：

- 捕获阶段
- 冒泡阶段

### 事件冒泡：

当一个元素的事件被触发时，同样的事件将会在该元素的所有祖先元素中依次被触发。这一过程被称为事件冒泡

简单理解：**当一个元素触发时，会依次向上调用所有父级元素的同名事件**

事件冒泡是默认存在的

```js
let fa = document.querySelector('.father')
let son = document.querySelector('.son')

fa.addEventListener('click', function () {
    alert('我是爸爸')
})
son.addEventListener('click', function () {
    alert('我是儿子')
})

// 点击父盒子会弹出我是爸爸，点击子盒子，会先弹出我是儿子，然后弹出我是爸爸。click是同名事件
// 这样的行为被称为事件冒泡
```

### 事件捕获

从DOM的根元素开始去执行对应的事件（从外到里）

事件捕获需要写相应的代码才能看到结果

语法：`DOM.addEventListener(事件类型, 事件处理函数，是否使用捕获机制)`

- 如果传入的是true，则代表捕获阶段触发，很少使用
- 默认为false，不触发

### 阻止事件流动

因为默认有冒泡模式的存在，所以容易导致事件影响到父级元素

在添加事件的函数中加入`e.stopProoagetion()`

```java
fa.addEventListener('click', function () {
    alert('我是爸爸')
})
son.addEventListener('click', function (e) {
    alert('我是儿子')
    // 阻止流动
    e.stopPropagation()    
})
```

鼠标经过事件：

- mouseover和mouseout会有冒泡效果
- mouseenter和mouseleave没有冒泡效果（推荐使用）

阻止事件的默认行为

- a 超链接的点击自动跳转功能：`e.preventDefault()`

### 两种事件注册方式的区别

#### 传统on注册 （L0）

![image-20220728093218749](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220728093218749.png)

同一个对象，后面注册的事件会覆盖前面注册的事件（同一个事件）

![image-20220728093303375](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220728093303375.png)

直接使用null覆盖就可以实现事件的解绑

都是冒泡阶段执行的

#### 事件监听注册 （L2）

后面注册的事件不会覆盖前面注册的事件 （同一个事件）

可以通过第三个参数去确定是在冒泡阶段执行还是捕获阶段执行

必须使用removeEventListener实现事件解绑

匿名函数无法绑定

### 事件委托

利用事件流的特征解决一些开发需求的知识技巧

```js
// 不要给每个小li注册事件，而是把事件委托给他的爸爸
// 事件委托是给父级添加事件，而不是孩子添加事件
let ui = document.querySelector('ul')
ul.addEventListener('click', function (e) {
    // 得到当前元素
	// console.log(e.target)
    e.target.style.color = 'red'
})
```

- 给父级元素添加事件，可以提高性能
- 事件委托原理是利用了事件冒泡的特点，给父元素添加事件，子元素可以触发
- 事件对象.target 可以获得真正触发的事件

## 网页特效

### 滚动和加载事件

#### 滚动事件

当页面进行滚动时触发的操作

场景：网页检测用户把页面滚动到某个区域后做一些处理，比如固定导航栏，返回顶部等

事件名：scroll

监听整个页面滚动：给window或者document添加scroll事件

```js
// 页面滚动事件
window.addEventListener('scroll', function() {
	// 执行的操作
})
```

也可以监听某个元素的内部滚动

#### 加载事件

加载外部资源完毕（如：图片、外联css、javascript等）加载完毕时触发的事件

场景：

- 有些时候需要等页面资源处理完了做一些事情，比如图片加载完了开始轮播，……
- 老代码喜欢把script写在head中，这时候直接找dom元素找不到（页面的加载顺序是从上往下的，可以window加上load事件，等待页面所有资源加载完成在执行js）

事件名：load

监听所有页面资源加载完毕：给window添加load事件

```js
// 页面加载事件
window.addEventListener('load', function () {
    // 执行的操作
})
```

也可以针对某个事件加load事件

**提倡js写在下面，网页的结构和样式重要些，js只是效果**

当初始的HTML文档被完全加载和解析完成之后，DOMContentLoaded事件被触发，而无需等待样式表、图像等完全加载

事件名：DOMContentLoaded

监听页面所有DOM加载完毕：给document添加DOMContentLoaded事件

```js
document.addEventListener('DOMContentLoaded', function () {
    // 执行的操作
})
```

## 元素大小和位置

### scroll家族

使用场景：我们想要页面滚动一段距离，比如100px，就让某些元素显示隐藏，那怎么知道，页面是不是滚动了100px呢？

获取元素内容的宽度和高度：scrollWidth，scrolleHeight（了解）

被卷去的头部和左侧：scrollLeft，scrollTop  【获取元素内容往左，往上滚出去看不到的距离，值可以修改，不用给单位】

页面滚动事件：

```js
windows.addEventListener('scroll', function () {
    let num = document.documentElement.scrollTop
    // 页面滚动的距离  documentElement就是html元素
    console.log(num)
})
```

### offset家族 （用的次数较多）

使用场景：scroll滚动的距离是我们自己计算出来的，开发中，最好是页面滚动到某个元素，就可以做某些事。

即js自己获取到页面顶部的位置，再去做某件事

获取宽高：获取元素自身的宽高，包含元素自身设置的宽高，padding、border 。`offestHeight，offsetWidth`

获取位置：offsetLeft和offsetTop获取元素距离自己定位父级元素的左，上距离

### client家族

获取宽高：获取的是元素的可见部分的宽高（不包含边框，滚动条等）

clientWidth，clientHteight  【注意是只读属性】

在窗口尺寸大小改变时发生的事件：resize   【可视区域发生变化】

```js
window.addEventListener('resize', function () {
    // 执行的操作
})
```

## Window对象

### BOM

BOM【Browser Object Model】是浏览器对象模型

![image-20220729171459464](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220729171459464.png)

window是浏览器内置中的全局对象，我们所学习的web APIs的知识内容都是基于window对象实现的，在大部分情况下是可以省略的

```js
window.addEventListener('scroll', function () {
    console.log(11)
})

// window可以省略
addEventListener('scroll', function () {
    console.log(11)
})
```

### 定时器-延时函数

js中内置一个用来让代码延迟执行的函数，叫setTimeout

setTimeout仅仅执行一次，所以可以理解为就是把一段代码延迟后执行，平时省略window

场景：5秒钟之后广告自动关闭……

```js
setTimeout(回调函数，等待的毫秒数)

let timer = setTimeout(回调函数，等待的毫秒数)
clearTimeout(timer)
```

结合递归函数可以使用setTimeout实现setInterval一样的功能

```js
        let div = document.querySelector('div')

        function fn() {
            div.innerHTML = new Date().toLocaleString()

            // 递归调用fn函数
            setTimeout(fn, 1000)
        }

        fn();
```

### js执行

```js
console.log(1111)
setTimeout(function () {
    console.log(2222)
}, 1000)
console.log(3333)
```

此段代码的执行结果是1111，3333，2222，setTimeout函数1秒钟之后开始执行

```js
console.log(1111)
setTimeout(function () {
    console.log(2222)
}, 0)
console.log(3333)
```

此段代码的执行结果也是1111，3333，2222

js是单线程

> JavaScript语言的一大特点就是单线程，也就是说，同一个时间只能做一件事。这是因为Javascript这
> 门脚本语言诞生的使命所致一一javaScript是为处理面中用户的交互，以及操作DOM而诞生的。比
> 如我们对某个DOM元素进行添加和删除操作，不能同时进行。应该先进行添加，之后再删除。

单线程就意味着，所有任务需要排队，前一个任务结束，才会执行后一个任务、这样所导致的问题是：如果js执行的时间过长，这样就会造成页面的渲染不够连贯，导致页面渲染加载阻塞的感觉

同步和异步：

> 为了解决这个问题，利用多喝cpu的计算能力，html5提出了web worker标准，允许javascript脚本创建多个线程。于是，js中出现了同步和异步

同步：前一个任务完成了之后再去执行另一个任务，程序的执行顺序和任务的排列顺序是相同的。

> 同步任务都是在主线程上执行的，形成一个执行栈

异步：同时进行多个任务，程序的执行顺序和任务的排列顺序是不同的。比如做饭时：在烧开水的同时，去炒菜，切菜等……

> js的异步是通过回调函数实现的，【任务队列】
>
> 一般异步任务有一下三种类型：
>
> - 普通事件：click，resize
>
> - 资源加载：load，error
>
> - 定时器：setInterval，setTimeout等

**最大的区别是任务的执行顺序不同。**

同步和异步任务的执行顺序：

1. 先执行执行栈中的同步任务
2. 异步任务放入任务队列中
3. 一旦执行栈中的所有同步任务执行完毕之后，系统就会一次读取任务队列中的异步任务，被读取的异步任务结束等待状态，进入执行栈，开始执行。

![image-20220730104138609](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220730104138609.png)

### location对象

location的数据类型是对象，拆分并保存了url地址中的各个组成部分

常用属性和方法

- href属性用户获取完成的url地址，对其赋值时用于地址的跳转

  > 利用js的方式跳转页面  `window.location.href = 'url地址'   window可以省略`
  >
  >  场景：用户点击可以跳转页面，如果不点击，5秒后自动跳转，要求里面有描述倒计时  【支付成功之后跳转，app首页跳转】
  >
  > - 分析：
  >
  >   1. 目标元素是链接
  >   2. 利用定时器设置数字倒计时
  >   3. 时间到了，自动跳转到新页面
  >
  >   ```js
  >   <a href="https://www.baidu.com">支付成功，<span>5</span>秒之后跳转首页</a>
  >         
  >   let a = document.queruSelector('a')    
  >   let num = 6
  >   setInterval(function () {
  >       num --
  >       a.innerHTML = `支付成功，<span>${num}</span>秒之后跳转首页`
  >   }, 1000)
  >   ```

- search属性获取地址中携带的参数，符号？后面的部分

- hash属性获取地中的哈希值，符号#后的部分

  > ![image-20220730110110072](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220730110110072.png)

- reload方法用于刷新当前页面，传入参数为true时表示强制刷新  有本地缓存，ctrl+f5强制刷新，从服务端拉取

### history对象

![image-20220730110658997](D:\information\Typora笔记\前端\JavaScript进阶web APIs.assets\image-20220730110658997.png)

### swiper插件

https://www.swiper.com.cn/

自定义轮播图效果，插件

### 本地存储

随着互联网的快速发展，基于网页的应用越来越普遍，同时也变得越来越复杂，为了满足各种各样的需求，会经常性在本地存储大量的数据，html5规范提出了相关的解决方案

1. 数据存储在用户浏览器中
2. 设置，读取方便，甚至页面刷新不丢失数据
3. 容量较大，sessionStorage和localStorage容量约5M左右

#### localStorage

1. 生命周期永久有效，除非手动删除，否则管不页面也会存在
2. 乐意多窗口共享，同一浏览器共享
3. 以键值对的形式存储使用

```js
localStorage.setItem(key, value)

// 存
localStorage.setItem('name', 'yuluo')
// 取
localStorage.getItem('name')
// 删除数据
localStorage.removeItem('name')

// 也可以存储引用数据类型
let obj = {
    name: 'yuluo',
    age: 20
}
// localStorage.setItem('obj', obj)
// 本地存储只能存储字符串，无法存储复杂数据类型，需要将复杂数据类型转换成json字符串，在存储到本地
// JSON.stringify(复杂数据类型)   将复杂数据类型转换成json字符串， 存储到本地存储中
// JSON.parse(JSON字符串)  将json字符串转换为对象， 取出时使用
// JSON字符串正规的使用
localStorage.setItem('obj', JSON.stringify(obj))
```





