# 利用命令行创建vue工程的步骤

## 安装cli

`npm install -g @vue/cli`

检查是否安装完成

`vue --version`

## 创建项目

`vue create + 项目名`                  **项目名不能有大写字母**

选择第 4 个：手动配置vue3项目

![image-20220812211014251](\VUE3.0.assets\image-20220812211014251.png)

## 根据自己的需要选择配置

![image-20220812210944253](\VUE3.0.assets\image-20220812210944253.png)

| 序号 | 选项                              | 描述                                                         | 选择 |
| ---- | --------------------------------- | ------------------------------------------------------------ | ---- |
| 1    | Choose Vue version                | 选择Vue版本                                                  | Y    |
| 2    | Babel                             | vue项目中普遍使用es6语法，但有时我们的项目需要兼容低版本浏览器，这时就需要引入babel插件，将es6转成es5 | Y    |
| 3    | TypeScript                        | TypeScript通过添加类型来扩展JavaScript。通过了解JavaScript，TypeScript可以节省您捕获错误的时间并在运行代码之前提供修复。任何浏览器，任何操作系统，任何运行JavaScript的地方。 完全开源 |      |
| 4    | Progressive Web App (PWA) Support | 渐进式Web应用程序（PWA）支持                                 |      |
| 5    | Router                            | 路由                                                         |      |
| 6    | Vuex                              | Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化 |      |
| 7    | CSS Pre-processors                | CSS预处理器，预处理器：比如要用sass或者cssNext就要按照人家规定的语法形式，就是用人家的语法去编写，然后人家把你编写的代码转成css。 |      |
| 8    | Linter / Formatter                | 格式化程序                                                   | Y    |
| 9    | Unit Testing                      | 单元测试                                                     |      |
| 10   | E2E Testing                       | 端到端（end-to-end）                                         |      |

**向上向下箭头控制方向，空格进行选中，可选择如下配置，引入Router 和vuex以及CSS预处理器，选择完成回车即可， 可以不选择语法检测Linter/ Formatter**

![image-20220812211121848](\VUE3.0.assets\image-20220812211121848.png)

## 选择vue版本

![image-20220812211139442](\VUE3.0.assets\image-20220812211139442.png)

## 选择路由模式

不选择使用历史模式输入n  回车

使用历史模式输入y  回车

**建议选择历史模式**

## 选择css解释器

此处建议选择 Sass/SCSS(with dart-sass)，即直接选择回车即可。

node-sass是自动编译实时的，dart-sass需要保存后才会生效

## 选择如何存放配置

![image-20220812211316412](\VUE3.0.assets\image-20220812211316412.png)

此处一般选择默认配置，即直接回车

> In dedicated config files 存放到独立文件中，
>
> In package.json 存放到 package.json 中

## 选择是否保存本次配置为项目创建时的模板

不保存输入n

保存输入y

## 等待项目创建完成

![image-20220812211501623](\VUE3.0.assets\image-20220812211501623.png)

## 进入项目路径，输入 npm run serve 运行项目

## vue安装插件

cmd输入vue ui然后代入当前工程，进入依赖，搜索需要安装的依赖，点击安装之后，选择完成安装，让自己调用。

最终会在项目下生成一个plugis插件，安装的js文件会在这里显示出来

![image-20220812211619032](\VUE3.0.assets\image-20220812211619032.png)
