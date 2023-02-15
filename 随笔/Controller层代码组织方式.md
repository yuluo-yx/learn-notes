# Controller层代码组织方式

一个优秀的controller逻辑

> 说到 Controller，相信大家都不陌生，它可以很方便地对外提供数据接口。
>
> 我认为是**「不可或缺的配角」**
>
> - 说它不可或缺是因为无论是传统的三层架构还是现在的COLA架构，Controller 层依旧有一席之地，说明他的必要性；
>
> - 说它是配角是因为 Controller 层的代码一般是不负责具体的逻辑业务和逻辑实现，但是它负责接收和响应请求。

## 问题分析

Controller 主要的工作有以下几项

- 接收请求，解析参数
- 调用 Service 执行具体的业务代码（可能包含参数校验）
- 捕获业务逻辑异常做出反馈
- 业务逻辑执行成功做出响应

```java
//DTO
@Data
public class TestDTO {
    private Integer num;
    private String type;
}


//Service
@Service
public class TestService {

    public Double service(TestDTO testDTO) throws Exception {
        if (testDTO.getNum() <= 0) {
            throw new Exception("输入的数字需要大于0");
        }
        if (testDTO.getType().equals("square")) {
            return Math.pow(testDTO.getNum(), 2);
        }
        if (testDTO.getType().equals("factorial")) {
            double result = 1;
            int num = testDTO.getNum();
            while (num > 1) {
                result = result * num;
                num -= 1;
            }
            return result;
        }
        throw new Exception("未识别的算法");
    }
}


//Controller
@RestController
public class TestController {

    private TestService testService;

    @PostMapping("/test")
    public Double test(@RequestBody TestDTO testDTO) {
        try {
            Double result = this.testService.service(testDTO);
            return result;
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }

    @Autowired
    public DTOid setTestService(TestService testService) {
        this.testService = testService;
    }
}
```

上述代码存在的问题

> 1. 参数校验过多地耦合了业务代码，违背单一职责原则
> 2. 可能在多个业务中都抛出同一个异常，导致代码重复
> 3. 各种异常反馈和成功响应格式不统一，接口对接不友好

## 优化

### 统一返回结构

统一返回值类型无论项目前后端是否分离都是非常必要的，方便对接接口的开发人员更加清晰地知道这个接口的调用是否成功（不能仅仅简单地看返回值是否为 `null` 就判断成功与否，因为有些接口的设计就是如此），使用一个状态码、状态信息就能清楚地了解接口调用情况

#### 前后端不分离

在前后端不分离的项目中，我通常使用的是如下的返回类

```java
import io.swagger.annotations.ApiModel;
import io.swagger.annotations.ApiModelProperty;
import lombok.*;

import java.util.HashMap;
import java.util.Map;

/**
 * @author: yuluo
 * @FileName: Msg.java
 * @createTime: 2022/4/11 12:50
 * @Description: 通用的返回类，含有json信息以及对数据操作是否成功的状态给浏览器
 */

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
@ApiModel(value = "Msg 通用信息返回类", description = "")
public class Msg {

    // 状态码
    @ApiModelProperty("状态码")
    private int code;

    // 提示信息
    @ApiModelProperty("提示信息")
    private String msg;

    // 用户要返回给浏览器的数据
    @ApiModelProperty("返回给浏览器的数据")
    private Map<String, Object> extend = new HashMap<>();

    /**
     * 添加信息
     * @param key
     * @param value
     * @return
     */
    public Msg add(String key, Object value) {

        this.getExtend().put(key, value);

        return this;
    }

    /**
     * 处理成功
     * @return
     */
    public static Msg success() {
        Msg result = new Msg();
        result.setCode(100);
        result.setMsg("处理成功");

        return result;
    }

    /**
     * 处理失败
     * @return
     */
    public static Msg fail() {
        Msg result = new Msg();
        result.setCode(200);
        result.setMsg("处理失败");

        return result;
    }
}

```
使用示例：

```java
   /**
     * 用户登录
     *
     * @param username 用户名
     * @return
     */
    @PostMapping(value = "/login")
    @ApiOperation(value = "根据用户名获取用户信息", httpMethod = "POST", notes = "用于用户登录")
    public Msg getUserByUsername(@RequestParam String username, @RequestParam String password) {

        UserInfo userByUsername = userService.getUserByUsername(username);
        // 处于安全考虑，密码使用md5加密
        String currentPassword = MD5Util.getMD5(password);

        // 1、验证用户名是否存在，不存在将异常信息返回给前端
        if (Objects.isNull(userByUsername)) {
            throw new LoginException("用户名不存在！");
        }

        // 2、用户名存在之后，验证密码，密码错误抛出异常信息 从数据库查出来的用户密码和在前端页面传过来的登录密码比对
        String userPassword = userByUsername.getPassword();
        log.info("当前用户密码： {}", currentPassword);
        log.info("数据库用户密码：{}", userPassword);

        if (!Objects.equals(userPassword, currentPassword)) {
            throw new LoginException("密码错误！");
        }

        // 3、检查完毕之后，将用户信息存到redis中，用于缓存用户信息
        // 生成uuid
        String uuid = UuidUtil.getUuid();
        redisTemplate.setObject(uuid, userByUsername, 18000L);

        // 给前端返回一个token值，用于校验用户登录状态，在过滤器器中拦截请求，查看redis中是否有对应的用户信息
        return Msg.success().add("token", uuid);
    }
```

#### 前后端分离

之后的例子演示使用此返回类

```java
package indi.yuluo.xojbackgroundmanagmentsystem.entity.model;

/**
 * @Author: yuluo
 * @CreateTime: 2022-08-26  14:48
 * @Description: 返回结果行为接口
 */

public interface IResult {

    /**
     * 获取code
     * @return
     */
    Integer getCode();

    /**
     * 获取描述
     * @return
     */
    String getMessage();

}


package indi.yuluo.xojbackgroundmanagmentsystem.Enum;

import indi.yuluo.xojbackgroundmanagmentsystem.entity.model.IResult;

/**
 * @Author: yuluo
 * @CreateTime: 2022-08-26  14:36
 * @Description: 返回结果枚举类
 */
public enum ResultEnum implements IResult {


    SUCCESS(8291, "接口调用成功"),
    VALIDATE_FAILED(8292, "参数校验失败"),
    COMMON_FAILED(8293, "接口调用失败"),
    FORBIDDEN(8294, "没有权限访问资源");

    private Integer code;
    private String message;

    ResultEnum(Integer code, String message) {
        this.code = code;
        this.message = message;
    }

    @Override
    public Integer getCode() {
        return code;
    }

    @Override
    public String getMessage() {
        return message;
    }

}


package indi.yuluo.xojbackgroundmanagmentsystem.entity.model;

import indi.yuluo.xojbackgroundmanagmentsystem.Enum.ResultEnum;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.io.Serializable;

/**
 * @Author: yuluo
 * @CreateTime: 2022-08-26  14:53
 * @Description: 统一返回数据结果
 */

@Data
@AllArgsConstructor
@NoArgsConstructor
public class Result<T> implements Serializable {

    // 编码
    private Integer code;

    // 错误信息
    private String message;

    // 数据
    private T data;

    /**
     * 只返回成功代码和描述，不返回其他数据
     */
    public static <T> Result<T> success() {
        return new Result<>(ResultEnum.SUCCESS.getCode(), ResultEnum.SUCCESS.getMessage(), null);
    }

    /**
     * 返回成功代码和描述，以及自定义数据
     */
    public static <T> Result<T> success(T data) {
        return new Result<>(ResultEnum.SUCCESS.getCode(), ResultEnum.SUCCESS.getMessage(), data);
    }

    /**
     * 返回成功代码和自定义的String类型的信息描述和数据
     */
    public static <T> Result<T> success(String message, T data) {
        return new Result<>(ResultEnum.SUCCESS.getCode(), message, data);
    }

    /**
     * 返回失败的代码和描述信息，不带数据
     */
    public static Result<?> failed() {
        return new Result<>(ResultEnum.COMMON_FAILED.getCode(), ResultEnum.COMMON_FAILED.getMessage(), null);
    }


    /**
     * 返回失败的代码和自定义的String描述信息，不带数据
     */
    public static <T> Result<T> failed(String message) {
        return new Result<>(ResultEnum.COMMON_FAILED.getCode(), message, null);
    }

    /**
     * 用于参数校验时，添加异常信息中的msg
     *
     * @param errorResult 继承IResult的枚举类
     * @param <T>         泛型
     * @return Result对象
     */
    public static <T> Result<T> failed(IResult errorResult, String message) {
        return new Result<>(errorResult.getCode(), message, null);
    }

    /**
     * 自定义选择结果枚举类中的信息
     *
     * @param errorResult 返回接口的具体实现类，通常是枚举
     * @param <T>
     * @return
     */
    public static <T> Result<T> failed(IResult errorResult) {
        return new Result<>(errorResult.getCode(), errorResult.getMessage(), null);
    }

    /**
     * 自定义返回信息
     *
     * @param code    代码
     * @param message 信息
     * @param data    数据
     * @param <T>     泛型
     * @return 返回中
     */
    public static <T> Result<T> instance(Integer code, String message, T data) {
        Result<T> result = new Result<>();

        result.setCode(code);
        result.setMessage(message);
        result.setData(data);

        return result;
    }

}

```

### 统一包装处理（进阶使用）

Spring 中提供了一个类 `ResponseBodyAdvice` ，能帮助我们实现上述需求

`ResponseBodyAdvice` 是对 Controller 返回的内容在 `HttpMessageConverter` 进行类型转换之前拦截，进行相应的处理操作后，再将结果返回给客户端。那这样就可以把统一包装的工作放到这个类里面。

```java
public interface ResponseBodyAdvice<T> {
    boolean supports(MethodParameter returnType, Class<? extends HttpMessageConverter<?>> converterType);

    @Nullable
    T beforeBodyWrite(@Nullable T body, MethodParameter returnType, MediaType selectedContentType, Class<? extends HttpMessageConverter<?>> selectedConverterType, ServerHttpRequest request, ServerHttpResponse response);
}
```

- supports：判断是否要交给 beforeBodyWrite 方法执行，ture：需要；false：不需要
- beforeBodyWrite：对 response 进行具体的处理

```java
// 如果引入了swagger或knife4j的文档生成组件，这里需要仅扫描自己项目的包，否则文档无法正常生成
@RestControllerAdvice(basePackages = "com.example.demo")
public class ResponseAdvice implements ResponseBodyAdvice<Object> {
    @Override
    public boolean supports(MethodParameter returnType, Class<? extends HttpMessageConverter<?>> converterType) {
        // 如果不需要进行封装的，可以添加一些校验手段，比如添加标记排除的注解
        return true;
    }
  

    @Override
    public Object beforeBodyWrite(Object body, MethodParameter returnType, MediaType selectedContentType, Class<? extends HttpMessageConverter<?>> selectedConverterType, ServerHttpRequest request, ServerHttpResponse response) {
        // 提供一定的灵活度，如果body已经被包装了，就不进行包装
        if (body instanceof Result) {
            return body;
        }
        return Result.success(body);
    }
}
```

经过这样改造，既能实现对 Controller 返回的数据进行统一包装，又不需要对原有代码进行大量的改动

### 处理cannot be cast to java.lang.String 问题

如果直接使用 `ResponseBodyAdvice`，对于一般的类型都没有问题，当处理字符串类型时，会抛出 `xxx.包装类 cannot be cast to java.lang.String` 的类型转换的异常

在 `ResponseBodyAdvice` 实现类中 debug 发现，只有 String 类型的 `selectedConverterType` 参数值是 `org.springframework.http.converter.StringHttpMessageConverter`，而其他数据类型的值是 `org.springframework.http.converter.json.MappingJackson2HttpMessageConverter`

- String 类型

![图片](https://mmbiz.qpic.cn/mmbiz_png/R3InYSAIZkHG5dd9fsb6GevNHQSHESSeiamOk1Hia7wUQ5ibTx1Pe1AHYo84bghtBspz531DTZCpwiaxRDmr8YGdyg/640?wx_fmt=png&random=0.33352683339589717&wxfrom=5&wx_lazy=1&wx_co=1)

- 其他类型 (如 Integer 类型)

![图片](https://mmbiz.qpic.cn/mmbiz_png/R3InYSAIZkHG5dd9fsb6GevNHQSHESSeFbMUd3x9U7tqo7ib2rLFYlqBRx3vd4QoRGcaZicpXcicqwcJeKFHcgE6g/640?wx_fmt=png&random=0.415119501608215&wxfrom=5&wx_lazy=1&wx_co=1)

现在问题已经较为清晰了，因为我们需要返回一个 `Result` 对象

所以使用 `MappingJackson2HttpMessageConverter` 是可以正常转换的，而使用 `StringHttpMessageConverter` 字符串转换器会导致类型转换失败。

有两种解决方式

1. 提前将String转换为JSON数据格式  (不常用)

   ```java
   @RestControllerAdvice(basePackages = "com.example.demo")
      public class ResponseAdvice implements ResponseBodyAdvice<Object> {
          ...
          @Override
          public Object beforeBodyWrite(Object body, MethodParameter returnType, MediaType selectedContentType, Class<? extends HttpMessageConverter<?>> selectedConverterType, ServerHttpRequest request, ServerHttpResponse response) {
              // 提供一定的灵活度，如果body已经被包装了，就不进行包装
              if (body instanceof Result) {
                  return body;
              }
              // 如果返回值是String类型，那就手动把Result对象转换成JSON字符串
              if (body instanceof String) {
                  try {
                      return this.objectMapper.writeValueAsString(Result.success(body));
                  } catch (JsonProcessingException e) {
                      throw new RuntimeException(e);
                  }
              }
              return Result.success(body);
          }
          ...
      }
      
      @GetMapping(value = "/returnString", produces = "application/json; charset=UTF-8")
      public String returnString() {
          return "success";
      }
   ```

2. 修改 `HttpMessageConverter` 实例集合中 `MappingJackson2HttpMessageConverter` 的顺序。因为发生上述问题的根源所在是集合中 `StringHttpMessageConverter` 的顺序先于 `MappingJackson2HttpMessageConverter` 的，调整顺序后即可从根源上解决这个问题

   - 在webMvc的配置类中添加MappingJackson2HttpMessageConverter

     ```java
     /**
      * 扩展mvc的消息转换器
      * @param converters
      */
     @Override
     protected void extendMessageConverters(List<HttpMessageConverter<?>> converters) {
     
         log.info("扩展mvc框架的消息转换器……");
     
         // 创建消息转换器对象
         MappingJackson2HttpMessageConverter messageConverter = new MappingJackson2HttpMessageConverter();
     
         // 设置对象转换器，底层使用jackson将java对象转换为json
         messageConverter.setObjectMapper(new JacksonObjectMapper());
     
         // 将上面的对象追加到mvc框架的转换同容器中
         // 这是有先后顺序的，将咱们自己的转换器放在最前面
         converters.add(0, messageConverter);
     }
     ```

   - 调整消息转换器的顺序

     ```java
     @Configuration
        public class WebMvcConfiguration implements WebMvcConfigurer {
        
            /**
             * 交换MappingJackson2HttpMessageConverter与第一位元素
             * 让返回值类型为String的接口能正常返回包装结果
             *
             * @param converters initially an empty list of converters
             */
            @Override
            public void configureMessageConverters(List<HttpMessageConverter<?>> converters) {
                for (int i = 0; i < converters.size(); i++) {
                    if (converters.get(i) instanceof MappingJackson2HttpMessageConverter) {
                        MappingJackson2HttpMessageConverter mappingJackson2HttpMessageConverter = (MappingJackson2HttpMessageConverter) converters.get(i);
                        converters.set(i, converters.get(0));
                        converters.set(0, mappingJackson2HttpMessageConverter);
                        break;
                    }
                }
            }
        }
     ```

     问题根源不是集合中缺少这一个转换器，而是转换器的顺序导致的，所以最合理的做法不是自己添加一个消息转换器，

     **应该是调整 `MappingJackson2HttpMessageConverter` 在集合中的顺序**

## 自定义异常与统一拦截异常

原来的代码中可以看到有几个问题

> 1. 抛出的异常不够具体，只是简单地把错误信息放到了 Exception 中
> 2. 抛出异常后，Controller 不能具体地根据异常做出反馈
> 3. 虽然做了参数自动校验，但是异常返回结构和正常返回结构不一致

自定义异常是为了后面统一拦截异常时，对业务中的异常有更加细颗粒度的区分，拦截时针对不同的异常作出不同的响应

而统一拦截异常的目的一个是为了可以与前面定义下来的统一包装返回结构能对应上，另一个是我们希望无论系统发生什么异常，Http 的状态码都要是 200 ，尽可能由业务来区分系统的异常.

```java
//自定义异常
package indi.yuluo.xojbackgroundmanagmentsystem.exception;

/**
 * @Author: yuluo
 * @CreateTime: 2022-08-25  13:51
 * @Description: TODO
 */

public class LoginException extends RuntimeException{

    public LoginException(String msg) {
        super(msg);
    }

}


package indi.yuluo.xojbackgroundmanagmentsystem.exception;

import indi.yuluo.xojbackgroundmanagmentsystem.Enum.ResultEnum;
import indi.yuluo.xojbackgroundmanagmentsystem.entity.model.Result;
import lombok.extern.slf4j.Slf4j;
import org.springframework.stereotype.Controller;
import org.springframework.util.StringUtils;
import org.springframework.validation.BindingResult;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.bind.annotation.RestController;

import javax.validation.ConstraintViolationException;

/**
 * @Author: yuluo
 * @CreateTime: 2022-07-21  11:37
 * @Description: 全局异常处理
 */

@ControllerAdvice(annotations = {RestController.class, Controller.class})   // 指定拦截的控制器
@Slf4j
@ResponseBody
public class GlobalExceptionHandler {

    /**
     * 用户不存在或者密码输入错误异常
     *
     * @param loginException 登录异常
     * @return
     */
    @ExceptionHandler({LoginException.class})
    public Result<?> handleLoginException(LoginException loginException) {
        log.info(loginException.getMessage());
        return Result.failed(loginException.getMessage());
    }

    /**
     * 参数校验不通过时抛出的异常处理
     *
     * @param ex 异常对象
     * @return 结果
     */
    @ExceptionHandler({MethodArgumentNotValidException.class})
    public Result<?> handleMethodArgumentNotValidException(MethodArgumentNotValidException ex) {
        BindingResult bindingResult = ex.getBindingResult();
        StringBuilder sb = new StringBuilder("参数校验失败:");
        for (FieldError fieldError : bindingResult.getFieldErrors()) {
            sb.append(fieldError.getField()).append("：").append(fieldError.getDefaultMessage()).append(", ");
        }
        String msg = sb.toString();
        // 如果有异常提示信息时使用
        if (StringUtils.hasText(msg)) {
            log.info(msg);
            return Result.failed(ResultEnum.VALIDATE_FAILED, msg);
        }
        return Result.failed(ResultEnum.VALIDATE_FAILED);
    }

    /**
     * 参数校验不通过时抛出的异常处理
     */
    @ExceptionHandler({ConstraintViolationException.class})
    public Result<?> handleConstraintViolationException(ConstraintViolationException ex) {
        if (StringUtils.hasText(ex.getMessage())) {
            log.info(ex.getMessage());
            return Result.failed(ResultEnum.VALIDATE_FAILED, ex.getMessage());
        }
        return Result.failed(ResultEnum.VALIDATE_FAILED);
    }

    /**
     * 顶级异常捕获并统一处理，当其他异常无法处理时候选择使用
     */
    @ExceptionHandler({Exception.class})
    public Result<?> handle(Exception ex) {
        return Result.failed(ex.getMessage());
    }

}

```

## 总结

优化之后可以发现 Controller 的代码变得非常简洁，可以很清楚地知道每一个参数、每一个 DTO 的校验规则，可以很明确地看到每一个 Controller 方法返回的是什么数据，也可以方便每一个异常应该如何进行反馈。

