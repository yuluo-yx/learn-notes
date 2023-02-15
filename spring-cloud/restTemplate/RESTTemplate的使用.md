# RESTTemplate的使用

spring官方提供的发送请求的模板

## TestController

```java
package indi.yuluo.resttemplate.controller;

import indi.yuluo.resttemplate.pojo.User;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

/**
 * @author: yuluo
 * @FileName: TestController.java
 * @createTime: 2022/6/17 12:33
 * @Description:
 */

@RestController
public class TestController {

    @GetMapping("/testGet")
    public String get(String name) {

        System.out.println(name);

        return "ok";
    }

    /**
     * post传参常见的有两种
     * 1、application/json
     *      json参数的核心在content-type 是 application/json类型 charset=utf-8
     * 2、form 表单参数
     *      header content-type = application/x-www-form-urlencoded
     * @param user
     * @return
     */
    @PostMapping("/testPost1")
    public String post(@RequestBody User user) {

        System.out.println(user);

        return "ok";
    }

    @PostMapping(value = "/testPost2")
    public String post2(User user) {
        System.out.println(user);

        return "ok";
    }

}
```

## Test测试类

```java
package indi.yuluo.resttemplate;

import indi.yuluo.resttemplate.pojo.User;
import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.ResponseEntity;
import org.springframework.util.LinkedMultiValueMap;
import org.springframework.web.client.RestTemplate;

@SpringBootTest
class ApplicationTests {

    @Test
    void contextLoads() {
        // 在java代码中发送请求
        RestTemplate restTemplate = new RestTemplate();

        // 返回值是一个object  将返回值转转换成为string类型 返回的是html页面代码
        String forObject = restTemplate.getForObject("https://www.baidu.com", String.class);
        System.out.println(forObject);
    }

    /**
     *  用于测试：restTemplate发送get和post请求
     */
    @Test
    public void testGet() {
        RestTemplate restTemplate = new RestTemplate();
        String url = "http://localhost:8080/testGet?name=yuluo";

        String result = restTemplate.getForObject(url, String.class);
        System.out.println(result);
    }

    /**
     *  用于测试：getForEntity
     */
    @Test
    public void testGet2() {
        RestTemplate restTemplate = new RestTemplate();

        String url = "http://localhost:8080/testGet?name=yuluo";

        /*
        http:// ip 协议 （规范，）
        请求头，请求参数 响应头 相应状态码，报文
         */
        ResponseEntity<String> responseEntity = restTemplate.getForEntity(url, String.class);

        System.out.println(responseEntity);
    }

    /**
     *  用于测试： restTemplate post请求
     */
    @Test
    public void testPost1() {
        RestTemplate restTemplate = new RestTemplate();

        User user = new User();
        user.setAge(20);
        user.setName("yuluo");

        String url = "http://localhost:8080/testPost1";

        /*
        发送post请求，是json参数，web中默认使用的是jackson，将对象转换成json字符串
         */
        String result = restTemplate.postForObject(url, user, String.class);

        System.out.println(result);
    }

    /**
     *  用于测试： 发送post表单参数
     */
    @Test
    public void test() {
        RestTemplate restTemplate = new RestTemplate();
        String url = "http://localhost:8080/testPost2";

        // 设置表单参数
        LinkedMultiValueMap<String, Object> linkedMultiValueMap = new LinkedMultiValueMap<>();
        linkedMultiValueMap.add("name", "yuluo");
        linkedMultiValueMap.add("age", 21);

        String result = restTemplate.postForObject(url, linkedMultiValueMap, String.class);

        System.out.println(result);
    }

}
```

## 对象

```java
package indi.yuluo.resttemplate.pojo;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;

/**
 * @author: yuluo
 * @FileName: User.java
 * @createTime: 2022/6/17 12:35
 * @Description:
 */

@Data
@AllArgsConstructor
@NoArgsConstructor
public class User implements Serializable {

    private String name;

    private Integer age;

}
```