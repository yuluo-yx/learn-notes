# JSON解析以及在java里面的应用

******

## 介绍

> **JSON**（**J**ava**S**cript **O**bject **N**otation, [/ˈdʒeɪsən/](https://zh.wikipedia.org/wiki/Help:英語國際音標)）是由[道格拉斯·克罗克福特](https://zh.wikipedia.org/wiki/道格拉斯·克羅克福特)构想和设计的一种轻量级[资料交换格式](https://zh.wikipedia.org/wiki/数据交换)。其内容由属性和值所组成，因此也有易于阅读和处理的优势。JSON是独立于[编程语言](https://zh.wikipedia.org/wiki/程式語言)的[资料格式](https://zh.wikipedia.org/wiki/文本文件)，其不仅是[JavaScript](https://zh.wikipedia.org/wiki/JavaScript)的子集，也采用了[C语言](https://zh.wikipedia.org/wiki/C語言)家族的习惯用法，目前也有许多编程语言都能够将其[解析和字符串化](https://zh.wikipedia.org/wiki/语法分析器)，其广泛使用的程度也使其成为通用的资料格式。**主要用来在前后端的数据交互**

## 基本的数据类型

- 数值：十进制数，不能有前导0，可以为负数，可以有小数部分。还可以用`**e**`或者`**E**`表示指数部分。不能包含非数，如NaN。不区分整数与浮点数。JavaScript用双精度浮点数表示所有数值。
- 字符串：以双引号`**""**`括起来的零个或多个[Unicode](https://zh.wikipedia.org/wiki/Unicode)[码位](https://zh.wikipedia.org/wiki/码位)。支持[反斜杠](https://zh.wikipedia.org/wiki/反斜杠)开始的[转义字符序列](https://zh.wikipedia.org/wiki/转义字符序列)。
- 布尔值：表示为`true`或者`false`。
- 数组：有序的零个或者多个值。每个值可以为任意类型。序列表使用方括号`**[**`，`**]**`括起来。元素之间用逗号`**,**`分割。形如：`[value, value]`
- 对象：若干无序的“键-值对”(key-value pairs)，其中键只能是字符串[[1\]](https://zh.wikipedia.org/wiki/JSON#cite_note-1)。建议但不强制要求对象中的键是独一无二的。对象以花括号`**{**`开始，并以`**}**`结束。键-值对之间使用逗号分隔。键与值之间用冒号`**:**`分割。
- 空值：值写为`null`

token（6种标点符号、字符串、数值、3种字面量）之间可以存在有限的空白符并被忽略。四个特定字符被认为是空白符：[空格符](https://zh.wikipedia.org/wiki/空格符)、[水平制表符](https://zh.wikipedia.org/wiki/水平制表符)、[回车符](https://zh.wikipedia.org/wiki/回车符)、[换行符](https://zh.wikipedia.org/wiki/换行符)。空白符不能出现在token内部（但空格符可以出现在字符串内部）。JSON标准不允许有[字节序掩码](https://zh.wikipedia.org/wiki/字节序掩码)，不提供注释的句法。 一个有效的JSON文档的根节点必须是一个对象或一个数组。

JSON交换时必须编码为[UTF-8](https://zh.wikipedia.org/wiki/UTF-8)。[[2\]](https://zh.wikipedia.org/wiki/JSON#cite_note-rfc8259-2)转义序列可以为：“\\”、“\"”、“\/”、“\b”、“\f”、“\n”、“\r”、“\t”，或[Unicode](https://zh.wikipedia.org/wiki/Unicode)16进制转义字符序列（\u后面跟随4位[16进制数字](https://zh.wikipedia.org/wiki/十六进制)）。对于不在[基本多文种平面](https://zh.wikipedia.org/wiki/基本多文種平面)上的[码位](https://zh.wikipedia.org/wiki/码位)，必须用[UTF-16](https://zh.wikipedia.org/wiki/UTF-16)代理对（surrogate pair）表示，例如对于[Emoji](https://zh.wikipedia.org/wiki/Emoji)字符——喜极而泣的表情（U+1F602 😂 face with tears of joy）在JSON中应表示为：

```json
{ "face": "😂" }
// or
{ "face": "\uD83D\uDE02" }
```

JSON的格式描述可以参考RFC 4627。

在JS语言中。一切都是对象，因此，任何JS支持的类型都可以通过JSON表示，例如字符串，数字，对象，数组等。

> 要求和语法格式
>
> - 对象表示为键值对
> - 数据由逗号分割，最后一个数据后不加逗号
> - 花括号保存对象
> - 方括号保存数组
>
> JSON键值对是用来保存JS对象的一种方式，和JS对象的写法大同小异，键/值对组合中的键名写在前面并用双引号""包裹，使用冒号： 然后紧跟值
>
> ````JSON
> {"name": "yuluo"}
> {"age": "12"}
> {"sex": "男"}
> 
> // 编写一个对象
> var user = {
> 	name: "yuluo",
> 	age: "13",
> 	sex: "男"
> };
> 
> console.log(user)
> 
> // 将js对象转换为json字符串
> var str = JSON.stringify(user)
>  console.log(str)     // 输出结果为：{"name":"yuluo","age":"13","sex":"男"}
> 
> // 将json字符串解析为js对象  parse：解析
> var user1 = JSON.parse(str)
> console.log(user1)
> ````

##  在java的controller层应用json返回一个对象字符串

> 这里建议使用tomcat9，tomcat10有未知错误

````Java
package indi.yuluo.json.controller;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.databind.SerializationFeature;
import indi.yuluo.json.pojo.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;

import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

/**
 * @author: yuluo
 * @createTime: 2022/1/6 11:18
 * @Description:
 */

@Controller
public class UserController {

    /**
     * 在正常使用java web返回的时候会走试图解析器，而json需要返回的是一个字符串
     * 市面上有很多的第三方的jar包可以实现这个功能，比如jackson和fastjson（阿里巴巴）
     * 此处使用jackson jar包
     *
     * @ResponseBody  将服务器端返回的对象转换为json对象响应回去
     * @RequestBody
     */
    // 请求地址注解
    @RequestMapping("/json1")
    @ResponseBody
    public String json1() throws JsonProcessingException {

        // 需要一个jackson的对象映射器，就是一个类，使用它可以直接将对象转换为json字符串
        ObjectMapper objectMapper = new ObjectMapper();

        // 新建一个对象
        User user = new User("yuluo", "13", "男");
        System.out.println("user = " + user);

        // 将对象转换为json字符串
        String string = objectMapper.writeValueAsString(user);
        System.out.println(string);

        // 由于这里使用了@ResponseBody这个注解，这里会将string以json格式的字符串返回，十分方便
        return string;
    }

    /**
     * 发现了一个问题中文输出乱码的问题：进行修改
     *  给@RequestMapping注解加一个属性（produces）
     *  通过设置编码格式为UTF-8，以及它的返回类型
     */
    @RequestMapping(value = "/json2", produces = "application/json;charset=utf-8")
    @ResponseBody()
    public String json2() throws JsonProcessingException {

        // 以上的代码可以精简为一行实现，如下
        return new ObjectMapper().writeValueAsString(new User("yuluo", "13", "男"));
    }

    @RequestMapping(value = "/json3")
    @ResponseBody
    public String json3() throws JsonProcessingException {

        List<User> users = new ArrayList<User>();
        User user1 = new User("yuluo1", "14", "男");
        User user2 = new User("yuluo2", "15", "男");
        User user3 = new User("yuluo3", "16", "男");
        User user4 = new User("yuluo4", "17", "男");

        users.add(user1);
        users.add(user2);
        users.add(user3);
        users.add(user4);

        return new ObjectMapper().writeValueAsString(users);

        /*
        浏览器显示结果：
        [{"name":"yuluo1","age":"14","sex":"男"},{"name":"yuluo2","age":"15","sex":"男"},{"name":"yuluo3","age":"16","sex":"男"},{"name":"yuluo4","age":"17","sex":"男"}]
        json格式为：
        [
	        {
		        "name":	"yuluo1",
		        "age":	"14",
		        "sex":	"男"
	        },
	        {
		        "name":	"yuluo2",
		        "age":	"15",
		        "sex":	"男"
	        },
	        {
		        "name":	"yuluo3",
		        "age":	"16",
		        "sex":	"男"
	        },
	        {
		        "name":	"yuluo4",
		        "age":	"17",
		        "sex":	"男"
	        }
       ]
         */
    }

    @RequestMapping(value = "/time1")
    @ResponseBody
    public String json4() throws JsonProcessingException {
        Date date = new Date();
        System.out.println("date = " + date);
        /**
         * 出现问题：时间在后端显示正常， date = Thu Jan 06 13:37:44 CST 2022
         *              前端默认返回的json字符串为时间戳  1641447436848
         */

        return new ObjectMapper().writeValueAsString(date);
    }

    @RequestMapping(value= "/time2")
    @ResponseBody
    public String json5() throws JsonProcessingException {
        ObjectMapper objectMapper = new ObjectMapper();
        // 1，取消jackson的默认时间戳格式
        objectMapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
        // 2.自定义时间格式
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        // 3,让objectMapper指定的时间日期格式为sdf
        objectMapper.setDateFormat(sdf);
        // 4, 新建Date类
        Date date = new Date();

        return objectMapper.writeValueAsString(date);

        /*
        前端结果为："2022-01-06 13:48:44"
         */
    }

}

````

> 针对经常使用的工具类，可以把他封装为一个工具类，减少重复代码。

