# VUE3.0

## Vue.js 是什么

Vue (读音 /vjuː/，类似于 **view**) 是一套用于构建用户界面的**渐进式框架**。与其它大型框架不同的是，Vue 被设计为可以自底向上逐层应用。Vue 的核心库只关注视图层，不仅易于上手，还便于与第三方库或既有项目整合。另一方面，当与[现代化的工具链](https://v3.cn.vuejs.org/guide/single-file-component.html)以及各种[支持类库](https://github.com/vuejs/awesome-vue#components--libraries)结合使用时，Vue 也完全能够为复杂的单页应用提供驱动。

> 对于 渐进式 的理解：
>
> 对框架进行分层设计，每一层都是可选的，不同层可以灵活选择其他的替代方案。
>
> **VUE不强求你一次性接受并使用它的全部功能特性**。

## cdn方式

从cdn地址中复制源码编写简单的vue实例

https://unpkg.com/vue@3.2.36/dist/vue.global.js

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>vue</title>

    <!-- 引入vue.js文件 -->
    <script src="../vue3.js"></script>

</head>
<body>
    
    <div id="user">
        <!-- 告诉vue，这个里面放了一个变量  -->
       <p>{{num}}</p> 
       <p>{{name}}</p>
    </div>

    <script>
        // 配置对象
        const Counter = {
            // 存放数据
            data: function() {
                return {
                    // 对象属性
                    num: 0,
                    name: 'yuluo'
                }
            },
            methods: function() {
                // 定义方法
            }
        } 
        
        // 使用vue, 创建vue应用，将配置对象传入 mount()挂载
        let app = Vue.createApp(Counter).mount('#user')
        console.log(app)
        // 在浏览器的console修改app.num的值，页面的值也会发生变化，这是vue的一个特性，数据双向绑定
    </script>

</body>
</html>
```

![image-20220801164544327](\VUE3.0.assets\image-20220801164544327.png)

## Vite

[Vite](https://cn.vitejs.dev/) 是一个 web 开发构建工具，由于其原生 ES 模块导入方式，可以实现闪电般的冷服务器启动。可以减少很多cli有得依赖项

使用 npm：使用npm -v查看npm的版本

```bash
# npm 6.x
$ npm init vite@latest <project-name> --template vue

# npm 7+，需要加上额外的双短横线
$ npm init vite@latest <project-name> -- --template vue

$ cd <project-name>
$ npm install
$ npm run dev
```

![image-20220801170200675](\VUE3.0.assets\image-20220801170200675.png)

## 声明式渲染

Vue.js 的核心是一个允许采用简洁的模板语法来声明式地将数据渲染进 DOM 的系统：

```html
<div id="counter">
  Counter: {{ counter }}
</div>
```

```js
const Counter = {
  data() {
    return {
      counter: 0,
      mouseEvent: 'click'
    }
  }
}

Vue.createApp(Counter).mount('#counter')
```

我们已经成功创建了第一个 Vue 应用！看起来这跟渲染一个字符串模板非常类似，但是 Vue 在背后做了大量工作。现在数据和 DOM 已经被建立了关联，所有东西都是**响应式的**。

## vue模板语法

v-once: 当数据改变时，插值处的内容不会更新  【“Mustache” (双大括号) 语法的文本插值】

v-html：

双大括号会将数据解释为普通文本，而非 HTML 代码。为了输出真正的 HTML，你需要使用[`v-html` 指令](https://v3.cn.vuejs.org/api/directives.html#v-html)：

```html
<p>Using mustaches: {{ rawHtml }}</p>
// 此时就可以不使用mostache语法
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

这个 `span` 的内容将会被替换成为 `rawHtml` property 的值，直接作为 HTML——会忽略解析 property 值中的数据绑定。注意，你不能使用 `v-html` 来复合局部模板，因为 Vue 不是基于字符串的模板引擎。反之，对于用户界面 (UI)，组件更适合作为可重用和可组合的基本单位。

v-bind：动态绑定属性内容 ，后台传过来的图片地址

语法糖：可以省略v-bind 写冒号

```html
<div v-bind:id="dynamicId"></div>
```

### 使用 JavaScript 表达式

迄今为止，在我们的模板中，我们一直都只绑定简单的 property 键值。但实际上，对于所有的数据绑定，Vue.js 都提供了完全的 JavaScript 表达式支持。

```html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

这些表达式会在当前活动实例的数据作用域下作为 JavaScript 被解析。有个限制就是，每个绑定都只能包含**单个表达式**，所以下面的例子都**不会**生效。

```html
<!--  这是语句，不是表达式：-->
{{ var a = 1 }}

<!-- 流程控制也不会生效，请使用三元表达式 -->
{{ if (ok) { return message } }}
```

v-on：监听dom事件

语法糖：@符号

```vue
<button v-on:click='changColor'>
    改变颜色
</button>

<button @click='changColor'>
    改变颜色
</button>

```

动态参数：

【动态属性】

```vue
<p v-bind:[attributeName]='id'>
    v-bind动态参数绑定
</p>
```

【动态事件】mouseEvent在data里面声明

```vue
<button @[mouseEvent]="attributeName = 'class'">
    改变属性
</button>

<button @click="mouseEvent='mouseover'">
    改变事件
</button>
```

## 计算属性

```vue
export default {
  data() {
    return {
      msg: 'helloworld'
    }
  },
  computed: {
    // 计算属性 只要依赖值不变，那么就不会重新计算
    reverseMsg: function () {
      return this.msg.split('').reverse().join('')
    }
  },
  methods: {
    // 方法
  }
}


  <p>{{ msg }}</p>
  // 在开发中使用计算属性
  <p>{{ msg.split('').reverse().join('') }}</p>
  <p>{{ reverseMsg }}</p>
```

我们可以将同样的函数定义为一个方法，而不是一个计算属性。从最终结果来说，这两种实现方式确实是完全相同的。然而，不同的是**计算属性将基于它们的响应依赖关系缓存**。计算属性只会在相关响应式依赖发生改变时重新求值。这就意味着只要 `msg` 还没有发生改变，多次访问 `msg` 时计算属性会立即返回之前的计算结果，而不必再次执行函数。提高程序性能。

### 计算属性的setter

计算属性默认只有 getter，不过在需要时你也可以提供一个 setter

```js
  computed: {
    // 计算属性 用法

    // 简写用法
    // reverseMsg: function () {
    //   return this.msg.split('').reverse().join('')
    // }

    // 每一个计算属性中都有一个getter和setter方法,
    // 完整写法
    reverseMsg: {
      // get方法在开发中不常用，一般直接写简写方法
      get:function() {
        return this.msg.split('').reverse().join('')
      },
      // 默认参数是newValue 在设置或者更改计算属性的时候使用  
      set: function(newValue) {
      },
    }

  },
```

## watch监听器的使用

虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的侦听器。这就是为什么 Vue 通过 `watch` 选项提供了一个更通用的方法来响应数据的变化。当需要在数据变化时执行异步【ajax请求】或开销较大的操作时，这个方式是最有用的。

```vue
<script>

export default {
    data() {
        return {
            message: 'hello world',
            age: 0
        };
    },
    methods: {

    },
    // 监听数据的变化
    watch: {
        // 侦听message的变化 发生变化时，触发后面的函数 两个参数名不是固定的
        // 一个属性影响多个数据
        message: function(newValue, oldValue) {
            console.log(newValue)
            console.log(oldValue)
        }
    }
}
</script>

<template>
    <div>
        <p>{{message}}</p>
        <button @click="message='你好'">改变message</button>
    </div>
</template>
```

## class和style的绑定

操作元素的 class 列表和内联样式是数据绑定的一个常见需求。因为它们都是 attribute，所以我们可以用 `v-bind` 处理它们：只需要通过表达式计算出字符串结果即可。不过，字符串拼接麻烦且易错。因此，在将 `v-bind` 用于 `class` 和 `style` 时，Vue.js 做了专门的增强。表达式结果的类型除了字符串之外，还可以是对象或数组

```vue
<script>

export default {
    data() {
        return {
            message: 'hello world',
            isActive: true
        };
    },
    methods:{

    }
}

</script>

<template>
<div>
    <!-- 第一种，防止字符穿 -->
    <p class="helloWorld">
        hello
    </p>
    <!-- 第二种,放置对象 (常用) -->
    <!-- <p class="{类名: boolean}">hello</p> -->
    <p :class="{active: isActive}">
        hello1
    </p>
    <!-- 点击切换样式 -->
    <button @click="isActive = !isActive">
        改变样式
    </button>
</div>
</template>

<style>
.active {
    font-size: 50px;
    color: skyblue;
}

.helloWorld {
    color: pink;
}
</style>
```

## style样式的绑定

`:style` 的对象语法十分直观——看着非常像 CSS，但其实是一个 JavaScript 对象。CSS property 名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用引号括起来) 来命名：

```html
<div :style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```js
data() {
  return {
    activeColor: 'red',
    fontSize: 30
  }
}
```

直接绑定到一个样式对象通常更好，这会让模板更清晰：

```html
<div :style="styleObject"></div>
```

```js
data() {
  return {
    styleObject: {
      color: 'red',
      fontSize: '13px'
    }
  }
}
```

同样的，对象语法常常结合返回对象的计算属性使用。

## 条件渲染

```vue
<script>
export default {
    data() {
        return {
            arg: 'hello',
            age: 16,
            isActive: false,
            sex: 'woman'
        };
    },
    methods:{

    },

}
</script>

<template>
    <div>
        <!-- 可以使用条件表达式 -->
        <p v-if="true">
            hello
        </p>
        <p v-if="false">
            {{arg}}
        </p>
        <!-- 判断，if不成立走else -->
        <p v-if="age >= 18">
            我是成年人
        </p>
        <p v-else>
            我还是小朋友
        </p>

        <!-- 在template元素上使用 v-if 条件渲染分组 -->
        <template v-if="isActive">
            <p> v-if条件渲染分组</p>
            <p> v-if条件渲染分组</p>
            <p> v-if条件渲染分组</p>
            <p> v-if条件渲染分组</p>
            <p> v-if条件渲染分组</p>
            <p> v-if条件渲染分组</p>
        </template>

        <!-- 使用 v-show 不支持template和v-else-->
        <p v-show="sex=='man'">男</p>
        <p v-show="sex=='woman'">女</p>
    </div>
</template>
```

v-if: 直接控制dom元素的销毁和创建

v-show：> 不同的是带有 `v-show` 的元素始终会被渲染并保留在 DOM 中。`v-show` 只是简单地切换元素的 `display` CSS property。

`v-if` 是“真正”的条件渲染，因为它会确保在切换过程中，条件块内的事件监听器和子组件适当地被销毁和重建。

`v-if` 也是**惰性的**：如果在初始渲染时条件为假，则什么也不做——直到条件第一次变为真时，才会开始渲染条件块。

相比之下，`v-show` 就简单得多——不管初始条件是什么，元素总是会被渲染，并且只是简单地基于 CSS 进行切换。

一般来说，`v-if` 有更高的切换开销，而 `v-show` 有更高的初始渲染开销。因此，如果需要非常频繁地切换，则使用 `v-show` 较好；如果在运行时条件很少改变，则使用 `v-if` 较好。

***根据特性分场景使用***

## 列表渲染

v-for

```vue
<script>
export default {
    data() {
        return {
            person: ['yuluo', 'huakai', 'asdf'],
            // person对象
            personObj: {
              name1: '李四',
              name2: '张三',
              name3: 'yuluo',
            },
        };
    },
    methods: {
        addPersonObjName: function() {
            this.person.unshift('huakai')
        }
    }
}
</script>

<template>
    <div>
        <ul>
            <!-- 使用v-for循环 -->
            <!-- in之后接数组名字 v-on:key='item' === :key='item'  index当前索引值-->
            <li v-for="item in person" :key="item">
                {{item}}
            </li>

            <br>

            <!-- in 和 of的作用相同 -->
            <li v-for="(item, index) of person" :key="item">
                {{item}}----> {{index}}
            </li>

            <br>

            <!-- key表示键名 -->
            <!--  -->
            <li v-for="(item, index) in person" :key="item">
                {{item}}----> {{index}}
            </li>

            <br>

            <!-- v-for使用对象。item键值，key键名 -->
            <li v-for="(item, key, index) in personObj" :key="index">
                {{item}}-->{{key}}-->{{index}}
            </li>

            <br>

            <!-- 为了给 Vue 一个提示，以便它能跟踪每个节点的身份，从而重用和重新排序现有元素 -->
            <!-- key是 唯一标识-->
            <!-- 快速找到节点，减少渲染次数 -->
            <li v-for="item in person" :key="item">
                <input type="checkbox">{{item}}
            </li>

            <button @click="addPersonObjName">添加</button>
        </ul>

    </div>
</template>
```

## 数组更新检测

```vue
<script>
export default {
    data() {
        return {
            person: [
                'yuluo',
                '张三',
                '李四',
                'huakai'
            ]
        };
    },
    methods: {
        changePersonList: function() {
            /*
                vue3可以通过索引修改数组  this.peson[2] = 'qwer'
                push() 给数组末尾添加元素
                pop()   删除掉数组最末尾的元素
                shift() 删除数组的第一位元素
                unshift()   给数组首位添加元素
                splice()    删除元素，插入元素，替换元素
                    1、删除元素，splice()方法有三个可选参数，第一个参数表示开始插入或者开始删除的元素的位置下标
                        第二个参数表示删除元素的个数，没有传参数删除后面所有元素
                    2、插入元素，第二个参数传入0表示没有可以删除的元素，第三个参数可以写入多个
                        this.person.splice(1, 0, '王五', '赵六')
                    3、替换元素 第一个元素表示替换元素开始的位置 第二个参数表示替换几个元素【可以理解为先删除后插入】
                sort()  排序数组
                reverse()  翻转数组
            */
           // this.person.push('王五', '赵六')
           this.person.splice(1, 0, '王五', '赵六')
        }
    }
}
</script>
```

## 事件处理

我们可以使用 `v-on` 指令 (通常缩写为 `@` 符号) 来监听 DOM 事件，并在触发事件时执行一些 JavaScript。用法为 `v-on:click="methodName"` 或使用快捷方式 `@click="methodName"`

### 事件修饰符

在事件处理程序中调用 `event.preventDefault()` 或 `event.stopPropagation()` 是非常常见的需求。尽管我们可以在方法中轻松实现这点，但更好的方式是：方法只有纯粹的数据逻辑，而不是去处理 DOM 事件细节。

为了解决这个问题，Vue.js 为 `v-on` 提供了**事件修饰符**。之前提过，修饰符是由点开头的指令后缀来表示的。

- `.stop`  
- `.prevent`
- `.capture`
- `.self`
- `.once`
- `.passive`

```html
<!-- 阻止单击事件继续冒泡 -->
<a @click.stop="doThis"></a>

<!-- 提交事件不再重载页面 -->
<form @submit.prevent="onSubmit"></form>

<!-- 修饰符可以串联 -->
<a @click.stop.prevent="doThat"></a>

<!-- 只有修饰符 -->
<form @submit.prevent></form>

<!-- 添加事件监听器时使用事件捕获模式 -->
<!-- 即内部元素触发的事件先在此处理，然后才交由内部元素进行处理 -->
<div @click.capture="doThis">...</div>

<!-- 只当在 event.target 是当前元素自身时触发处理函数 -->
<!-- 即事件不是从内部元素触发的 -->
<div @click.self="doThat">...</div>
```

## 表单输入绑定

可以用 v-model 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。它会根据控件类型自动选取正确的方法来更新元素。尽管有些神奇，但 `v-model` 本质上不过是语法糖。它负责监听用户的输入事件来更新数据，并在某种极端场景下进行一些特殊处理。

v-model的原理

> 使用的是v-bind和v-on的组合
>
> v-bind绑定一个value属性
>
> v-on给当前元素添加一个input事件

v-model 修饰符

### `.lazy`: 全部输入完成，失去焦点之后再更改数据

在默认情况下，`v-model` 在每次 `input` 事件触发后将输入框的值与数据进行同步 (除了[上述](https://v3.cn.vuejs.org/guide/forms.html#vmodel-ime-tip)输入法组织文字时)。你可以添加 `lazy` 修饰符，从而转为在 `change` 事件之后进行同步：

```html
<!-- 在“change”时而非“input”时更新 -->
<input v-model.lazy="msg" />
```

### `.number`

如果想自动将用户的输入值转为数值类型，可以给 `v-model` 添加 `number` 修饰符：

```html
<input v-model.number="age" type="text" />
```

当输入类型为 `text` 时这通常很有用。如果输入类型是 `number`，Vue 能够自动将原始字符串转换为数字，无需为 `v-model` 添加 `.number` 修饰符。如果这个值无法被 `parseFloat()` 解析，则返回原始的值。

### `.trim`

如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符：

```html
<input v-model.trim="msg" />
```

## 组件化开发

```vue
<script>
// 项目中的根组件就是App.vue

// 引入content.vue组件   
import Content from './components/Content.vue'

export default {
    data() {
        return {

        };
    },
    methods: {

    },

    // 引入组件
    components:{
        Content
    }
}
</script>

<template>
    <div>
        <!-- 使用content组件 -->
        <Content></Content>
    </div>
</template>

```

```vue
<!-- 组件首字母要大写 -->
<template>
<div>
    <Hello></Hello>
    <h2>
        我是组件content的内容
    </h2>
    <!-- 可以随意切换位置 展示效果 -->
    <Hello></Hello>
    
</div>
</template>

<script>

// 引入hello组件
import Hello from './Hello.vue'

export default {
    // 注册hello组件
    components: {
        Hello
    },

    // 这里的data必须是一个对象 是对象的原因是每个组件都返回一个新的对象，不会造成组件之间的数据污染，
    // 数组污染是再父组件中使用多个子组件的时候。改变任意一个组件中的值，其他的组件值不会变
    data: function() {
        return {

        };
    },
}

</script>
```

```vue
<template>
    <div>
        <h2>hello 这是hello组件  组件是可复用的实例</h2>
    </div>
</template>
```

## 通过prop向子组件传递数据



## 插槽 slot

App.vue:

```vue
<script>

import Content from './components/Content.vue'

export default {
  data() {
    return {

    };
  },
  methods: {
    
  },
  components: {
    Content
  }
}
</script>

<template>
  <div>
      <Content>
        <!-- 给子组件slot位置的元素 -->
        <button>按钮</button>
      </Content>

      <!-- 两个组件相互独立开 -->
      <Content>
        <input type="text">
      </Content>
  </div>
</template>
```

Content.vue

```vue
<template>
    <div>
        <h2>我是content组件内容</h2>
        <div>
            <!-- 这里的内容由父组件决定，先使用slot占位置 --> 
            <!-- 插槽的用法 -->
            <slot></slot>
        </div>
    </div>
</template>
```

## Provide / Inject

> 该页面假设你已经阅读过了[组件基础](https://v3.cn.vuejs.org/guide/component-basics.html)。如果你对组件还不太了解，推荐你先阅读它。

通常，当我们需要从父组件向子组件传递数据时，我们使用 [props](https://v3.cn.vuejs.org/guide/component-props.html)。想象一下这样的结构：有一些深度嵌套的组件，而深层的子组件只需要父组件的部分内容。在这种情况下，如果仍然将 prop 沿着组件链逐级传递下去，可能会很麻烦。

对于这种情况，我们可以使用一对 `provide` 和 `inject`。无论组件层次结构有多深，父组件都可以作为其所有子组件的依赖提供者。这个特性有两个部分：父组件有一个 `provide` 选项来提供数据，子组件有一个 `inject` 选项来开始使用这些数据。

![Provide/inject scheme](D:\information\Typora笔记\前端\vue\VUE3.0.assets\components_provide.png)

例如，我们有这样的层次结构：

```text
Root
└─ TodoList
   ├─ TodoItem
   └─ TodoListFooter
      ├─ ClearTodosButton
      └─ TodoListStatistics
```

如果要将 todo-items 的长度直接传递给 `TodoListStatistics`，我们要将 prop 逐级传递下去：`TodoList` -> `TodoListFooter` -> `TodoListStatistics`。通过 provide/inject 的方式，我们可以直接执行以下操作：

```js
const app = Vue.createApp({})

app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide: {
    user: 'John Doe'
  },
  template: `
    <div>
      {{ todos.length }}
      <!-- 模板的其余部分 -->
    </div>
  `
})

app.component('todo-list-statistics', {
  inject: ['user'],
  created() {
    console.log(`Injected property: ${this.user}`) // > 注入的 property: John Doe
  }
})
```

但是，如果我们尝试在此处 provide 一些组件的实例 property，这将是不起作用的：

```js
app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide: {
    todoLength: this.todos.length // 将会导致错误 `Cannot read property 'length' of undefined`
  },
  template: `
    ...
  `
})
```

要访问组件实例 property，我们需要将 `provide` 转换为返回对象的函数：

```js
app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide() {
    return {
      todoLength: this.todos.length
    }
  },
  template: `
    ...
  `
})
```

这使我们能够更安全地继续开发该组件，而不必担心可能会更改/删除子组件所依赖的某些内容。这些组件之间的接口仍然是明确定义的，就像 prop 一样。

实际上，你可以将依赖注入看作是“长距离的 prop”，除了：

- 父组件不需要知道哪些子组件使用了它 provide 的 property
- 子组件不需要知道 inject 的 property 来自哪里

## 生命周期

### 生命周期图示

下图展示了实例的生命周期。我们不需要立马弄明白所有的东西，不过随着不断学习和使用，它的参考价值会越来越高。

![实例的生命周期](D:\information\Typora笔记\前端\vue\VUE3.0.assets\lifecycle.svg)

```vue
<template>
    <div>
        hello world

        <button @click="addCounter">{{counter}}</button>
    </div>
</template>

<script>
export default {

    data: function() {
        return {
            counter: 0,
        };
    },

    methods: {
        addCounter: function() {
           this.counter ++;
        }
    },

    // 生命周期函数的使用
    beforeCreate() {
        console.log('beforeCreate');
    },

    created() {
        console.log('created')
    },

    beforeMount() {
        console.log('beforMount');
    },  

    mounted() {
        console.log('mounted');
    },

    beforeUpdate() {
        console.log('beforeUpdate');
    },

    updated() {
        console.log('updated');
    },

    beforeUnmount() {
        console.log('beforeUnmount');
    },

    unmounted() {
        console.log('unmounted');
    }
}
</script>
```

## 组合式API

如果能够将同一个逻辑关注点相关代码收集在一起会更好。而这正是组合式 API 使我们能够做到的。

既然我们知道了**为什么**，我们就可以知道**怎么做**。为了开始使用组合式 API，我们首先需要一个可以实际使用它的地方。在 Vue 组件中，我们将此位置称为 `setup`。

setup在组件被创建之前执行，不需要使用this关键字

### 组合式API响应式数据的定义

```vue
import {ref} from 'vue';

export default {
    data() {
        return {

        }
    },

    setup() {
        let msg = 'hello'
        console.log(msg);

        function changeMSg() {
            msg = 'nihao';
            console.log(msg);  
            // 点击按钮值发生变化，但是页面没有刷新，说明数据不是响应式的
        }

        // 通过ref定义响应式变量  ref是一个函数，返回一个带有value属性的对象
        const counter = ref(0)
        function addCounter() {
            counter.value ++;
        }

        // 暴露出去
        return {msg, changeMSg, counter, addCounter}
    }
}
</script>

<template>
    <div>
        <button @click="changeMSg">改变msg</button>

        <!-- 模板会自动解析value值 -->
        <h2>{{counter}}</h2>
        <button v-on:click="addCounter">改变counter</button>
    </div>
</template>
```

### 在组合式API中使用侦听器侦听

```vue
<script>

import {ref, reactive, watch, watchEffect} from 'vue'

export default {
    data() {
        return {
            msg: 'hello world'
        };
    },

    setup() {

        const counter = ref(0)
        function changeCounter() {
            counter.value ++;
        }
        // 侦听函数
        // 第一个参数是需要侦听的响应式引用，回调函数
        watch(counter, (newValue, oldValue)=> {
            console.log("newValue----->", newValue)
            console.log("oldValue----->", oldValue)
        })

        const user = reactive({
            uname: 'yuluo',
            age: 20
        })
        function changeUserName() {
            user.uname = 'huakai'
        }
        // 这种方式的侦听对user对象中的uname属性不管用
        // 必须要使用深度侦听 watchEffect
        watch(user, (newValue, oldValue)=> {
            console.log("newValue----->", newValue)
            console.log("oldValue----->", oldValue)
        })
        // wathEffect的参数只有一个  回调函数
        // 注意不需要指定侦听的属性，自动收集依赖
        watchEffect(()=> {
            console.log(user.uname);
        })

        return {counter, user, changeCounter, changeUserName};
    },

    // 选项式api的watch写法
    watch: {
        msg: function(newValue, oldValue) {
            console.log(newValue)
        }
    }
}

</script>

<template>
    <div>
        <h2>{{counter}}</h2>
        <button @click="changeCounter">改变counter</button>
        <br>
        <h2>{{user}}</h2>
        <button @click="changeUserName">改变name</button>
    </div>
</template>
```

### 在组合式API中使用生命周期钩子

因为setup是围绕beforeCreate和created生命周期钩子运行的，所以不需要显式的定义他们，换句话说，在这些钩子中编写的任何代码都应该在setup函数中编写

**要在**组合式API中使用生命周期钩子只需要在生命周期函数前加上on即可

```vue
<script setup>
import { onMounted, onUnmounted } from 'vue'

let intervalId
onMounted(() => {
  intervalId = setInterval(() => {
    // ...
  })
})

onUnmounted(() => clearInterval(intervalId))
</script>
```

### setup参数

第二个参数context

传入 `setup` 函数的第二个参数是一个 **Setup 上下文**对象。上下文对象暴露了其他一些在 `setup` 中可能会用到的值：

```vue
export default {
  setup(props, context) {
    // 透传 Attributes（非响应式的对象，等价于 $attrs）
    console.log(context.attrs)

    // 插槽（非响应式的对象，等价于 $slots）
    console.log(context.slots)

    // 触发事件（函数，等价于 $emit）
    console.log(context.emit)

    // 暴露公共属性（函数）
    console.log(context.expose)
  }
}
```

该上下文对象是非响应式的，可以安全地解构：

```vue
export default {
  setup(props, { attrs, slots, emit, expose }) {
    ...
  }
}
```

`attrs` 和 `slots` 都是有状态的对象，它们总是会随着组件自身的更新而更新。这意味着你应当避免解构它们，并始终通过 `attrs.x` 或 `slots.x` 的形式使用其中的属性。此外还需注意，和 `props` 不同，`attrs` 和 `slots` 的属性都**不是**响应式的。如果你想要基于 `attrs` 或 `slots` 的改变来执行副作用，那么你应该在 `onBeforeUpdate` 生命周期钩子中编写相关逻辑。

## Vue-router

安装：

https://unpkg.com/vue-router@4

[Unpkg.com](https://unpkg.com/) 提供了基于 npm 的 CDN 链接。上述链接将始终指向 npm 上的最新版本。 你也可以通过像 `https://unpkg.com/vue-router@4.0.15/dist/vue-router.global.js` 这样的 URL 来使用特定的版本或 Tag。

### npm

```bash
npm install vue-router@4
```

介绍：

> **路由核心：改变url，但是页面不进行刷新**
>
> 路由可以理解为指向

概念介绍：

路由表：本质是一个映射表，一个路由就是一个映射关系，key【路径】：value【函数function/组件component】

### `router-link`[#](https://router.vuejs.org/zh/guide/#router-link)

请注意，我们没有使用常规的 `a` 标签，而是使用一个自定义组件 `router-link` 来创建链接。这使得 Vue Router 可以在不重新加载页面的情况下更改 URL，处理 URL 的生成以及编码。我们将在后面看到如何从这些功能中获益。

### `router-view`[#](https://router.vuejs.org/zh/guide/#router-view)

`router-view` 将显示与 url 对应的组件。你可以把它放在任何地方，以适应你的布局。

### 编程式导航

通过js进行页面跳转

**注意：在 Vue 实例中，你可以通过 `$router` 访问路由实例。因此你可以调用 `this.$router.push`。**

想要导航到不同的 URL，可以使用 `router.push` 方法。这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，会回到之前的 URL。

当你点击 `<router-link>` 时，内部会调用这个方法，所以点击 `<router-link :to="...">` 相当于调用 `router.push(...)` ：

> $route: 当前活跃的路由对象，path，params，query，name
>
> $router: 跳转页面的操作，push，forward，go方法

### 不同的历史模式

哈希模式：

hash 模式是用 `createWebHashHistory()` 创建的：

```vue
import { createRouter, createWebHashHistory } from 'vue-router'

const router = createRouter({
  history: createWebHashHistory(),
  routes: [
    //...
  ],
})
```

它在内部传递的实际 URL 之前使用了一个哈希字符（`#`）。由于这部分 URL 从未被发送到服务器，所以它不需要在服务器层面上进行任何特殊处理。不过，**它在 SEO 中确实有不好的影响**。如果你担心这个问题，可以使用 HTML5 模式。

历史模式：

用 `createWebHistory()` 创建 HTML5 模式，推荐使用这个模式：

```
import { createRouter, createWebHistory } from 'vue-router'

const router = createRouter({
  history: createWebHistory(),
  routes: [
    //...
  ],
})
```

当使用这种历史模式时，URL 会看起来很 "正常"，例如 `https://example.com/user/id`。漂亮!

不过，问题来了。由于我们的应用是一个单页的客户端应用，如果没有适当的服务器配置，用户在浏览器中直接访问 `https://example.com/user/id`，就会得到一个 404 错误。这就丑了。

不用担心：要解决这个问题，你需要做的就是在你的服务器上添加一个简单的回退路由。如果 URL 不匹配任何静态资源，它应提供与你的应用程序中的 `index.html` 相同的页面。漂亮依旧!

### 导航守卫

## 获取数据

### fetch

### axios: 

>  基于promise可以用于浏览器和node.js的网络请求库

在当前项目中安装axios

` cnpm install axios`

在使用页面引入即可

## 通过proxy解决跨域问题

```js
import { defineConfig } from 'vite'
import vue from '@vitejs/plugin-vue'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [vue()],
  // 中转服务器
  server: {
    // 通过代理实现跨域
    proxy: {
      // https://m.php.cn
      '/path': {
        // 表示要替换的服务端地址
        target: 'https://m.php.cn',
        // 开启代理，允许跨域
        changeOrigin: true,
        // 设置重写的路径
        rewrite: path=>path.replace(/^\/path/, '')
      }
    }
  }
})

```

## VueX

Vuex 是一个专为 Vue.js 应用程序开发的**状态管理模式 + 库**。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。

### 什么时候应该使用VueX

Vuex 可以帮助我们管理共享状态，并附带了更多的概念和框架。这需要对短期和长期效益进行权衡。

如果您不打算开发大型单页应用，使用 Vuex 可能是繁琐冗余的。确实是如此——如果您的应用够简单，您最好不要使用 Vuex。一个简单的 [store 模式](https://v3.cn.vuejs.org/guide/state-management.html#从零打造简单状态管理)就足够您所需了。但是，如果您需要构建一个中大型单页应用，您很可能会考虑如何更好地在组件外部管理状态，Vuex 将会成为自然而然的选择。引用 Redux 的作者 Dan Abramov 的话说就是：

### 开始

每一个 Vuex 应用的核心就是 store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的**状态 (state)**。Vuex 和单纯的全局对象有以下两点不同：

1. Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
2. 你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地**提交 (commit) mutation**。这样使得我们可以方便地跟踪每一个状态的变化，从而让我们能够实现一些工具帮助我们更好地了解我们的应用。

### Mutations

提交载荷：传参数时设置参数即可，在commit的参数中追加，在mutations里面的函数通过形参接受

```js
import { createStore } from 'vuex'

export default createStore({
  state() { //是存储的单一状态，是存储的基本数据
    return {
      count: 1
    }
  },
  getters: {
  },

  // 通过此方法来改变仓库中的数据
  mutations: {
    // 参数state表示上面定义的state对象
    // value 传递的参数
    increment(state, value) {
      // state.count ++;
      state.count += value;
    }
  },

  actions: {
  },

  modules: {
  }
})

```

```vue
<template>
  <div class="home">
    <h2>{{$store.state.count}}</h2>
    <!-- 可以改变仓库中的数据 -->
    <!-- 不要这样去改变仓库中的数据，这样VueX不会追踪到数据的变化 -->
    <button @click="$store.state.count ++">count++</button>
    <br>
    <!-- 使用mutations来改变数据 -->
    <button @click="addCount">addCount</button>

  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'HomeView',

  methods: {
    addCount: function() {
      // 使用store.commit方法去改变仓库中的数据
      // 可以很清楚的跟踪到是那个组件更新了数据
      this.$store.commit('increment', num)
    }
  }

}
</script>

```

提交 mutation 的另一种方式是直接使用包含 `type` 属性的对象：

```js
store.commit({
  type: 'increment',
  amount: 10
})
```

当使用对象风格的提交方式，整个对象都作为载荷传给 mutation 函数，因此处理函数保持不变：

```js
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

### getter

```js
  // 可以理解为仓库中数据的计算属性
  // 如果state的状态需要过滤或者其他操作，可放在getters中
  getters: {
    reverseMSg: function(state) {
      return state.msg.split('').reverse().join('')
    },

    // 第二个参数表示的是store中的getters参数对象
    reverseMSgLength: function(state, getters) {
      return getters.reverseMSg.length
    }
  },
```

```cue
    <h2>{{$store.getters.reverseMSg}}</h2>
    <h2>{{$store.getters.reverseMSgLength}}</h2>
```

### action

Action 类似于 mutation，不同在于：

- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作。

```js
  // 异步数据处理，获取数据，和后台信息交互等
  actions: {
    getHot: function(context) {  // context：与store实例具有相同的属性和方法的对象
      // fetch('/path/api/mmdb/movie/v3/list/hot.json?ct=%E9%95%BF%E6%B2%99&ci=70&channelId=4').then((res)=>{
      //   console.log(res.json());
      //   // console.log(context.state.hotList);
      // })
      axios.get('/path/api/mmdb/movie/v3/list/hot.json?ct=%E9%95%BF%E6%B2%99&ci=70&channelId=4').then(res => {
        // console.log(res);
        // console.log(context);
        // 通过mutations更新数据

        // context.state.hotList = res.data.data.hot   

        // 触发mutations函数
        context.commit('updateHotList', res.data.data.hot)        

        console.log(context.state.hotList);
      })
    }
  },
```

```vue
  mounted() {
    // 分发action函数
    this.$store.dispatch('getHot')
  }
```

```js
  mutations: {
    // 参数state表示上面定义的state对象
    increment(state, value) {
      state.count += value;
    },
    updateHotList: function(state, value) {
      state.hotList = value
    }
  },
```

### modules

由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。

为了解决以上问题，Vuex 允许我们将 store 分割成**模块（module）**。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割：

### 辅助函数

简化store的使用方式

## 如何在vue-cli中解决跨域问题

```js
 fetch('/path/api/mmdb/movie/v3/list/hot.json?ct=%E9%95%BF%E6%B2%99&ci=70&channelId=4').then((res)=>{
        console.log(res.json());
      })
```

```js
const { defineConfig } = require('@vue/cli-service')
module.exports = defineConfig({
  transpileDependencies: true,

  devServer: {
    proxy: { //通过代理实现跨域
      //https://i.maoyan.com
      '/path': {
        target: 'https://i.maoyan.com', //替换的服务端地址
        changeOrigin: true, //开启代理，允许跨域
        pathRewrite: { //设置重写的路径
          '^/path': ''
        }
      }
    }
  }

})
```

## 面试题

- v-for和v-if不能放在一起使用

  ```markdown
  v-for的优先级高于v-if，将两个放在一块，会先执行v-for。
  会造成性能浪费，一般会使用计算属性来替换
  ```

  ![image-20220812212416245](D:\information\Typora笔记\前端\vue\VUE3.0.assets\image-20220812212416245.png)

- vue中如何使用自定义指令

  ![image-20220812220226782](D:\information\Typora笔记\前端\vue\VUE3.0.assets\image-20220812220226782.png)

- Vue中￥nextTick

  ```markdo
  理解：是将回调函数延迟在下一次dom更新数据之后调用
  ```

  ![image-20220812221954385](D:\information\Typora笔记\前端\vue\VUE3.0.assets\image-20220812221954385.png)
