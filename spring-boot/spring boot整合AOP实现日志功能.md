# spring boot整合AOP实现日志功能

## AOP简介

>  AOP (Aspect Oriented Programing)   面向切面编程 = Spring动态代理开发
> 以切面为基本单位的程序开发，通过切面间的彼此协同，相互调用，完成程序的构建
> 切面 = 切入点 + 额外功能

>  OOP (Object Oritened Programing)   面向对象编程 Java
> 以对象为基本单位的程序开发，通过对象间的彼此协同，相互调用，完成程序的构建

> POP (Producer Oriented Programing) 面向过程(方法、函数)编程 C 
> 以过程为基本单位的程序开发，通过过程间的彼此协同，相互调用，完成程序的构建

## AOP的概念

> AOP的概念：
>      本质就是Spring得动态代理开发，通过代理类为原始类增加额外功能。
>      好处：利于原始类的维护

***注意：AOP编程不可能取代OOP，OOP编程有意补充。***

## 结合AOP，实现日志功能

>  参考[ruoyi](http://doc.ruoyi.vip/) 

## 注解类

```java
package indi.yuluo.xojbackgroundmanagmentsystem.annotation;

import indi.yuluo.xojbackgroundmanagmentsystem.Enum.BusinessType;

import java.lang.annotation.*;

/**
 * @Author: yuluo
 * @CreateTime: 2022-10-01  17:21
 * @Description: TODO
 */


@Target({ ElementType.PARAMETER, ElementType.METHOD, ElementType.TYPE }) // 注解放置的目标位置,PARAMETER: 可用在参数上  METHOD：可用在方法级别上
@Retention(RetentionPolicy.RUNTIME)    // 指明修饰的注解的生存周期  RUNTIME：运行级别保留
@Documented
public @interface Log {

    /**
     * 模块
     */
    String title() default "";

    /**
     * 功能
     */
    public BusinessType businessType() default BusinessType.OTHER;

    /**
     * 是否保存请求的参数
     */
    public boolean isSaveRequestData() default true;

    /**
     * 是否保存响应的参数
     */
    public boolean isSaveResponseData() default true;
}


```

### Aspectj

```java
package indi.yuluo.xojbackgroundmanagmentsystem.aop;

import cn.hutool.extra.servlet.ServletUtil;
import com.alibaba.fastjson.JSON;
import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import indi.yuluo.xojbackgroundmanagmentsystem.annotation.Log;
import indi.yuluo.xojbackgroundmanagmentsystem.common.BaseContext;
import indi.yuluo.xojbackgroundmanagmentsystem.entity.domain.log.SysLog;
import indi.yuluo.xojbackgroundmanagmentsystem.entity.domain.user.UserInfo;
import indi.yuluo.xojbackgroundmanagmentsystem.service.UserService;
import indi.yuluo.xojbackgroundmanagmentsystem.service.XOJLogService;
import indi.yuluo.xojbackgroundmanagmentsystem.utils.AspectjUtils;
import lombok.extern.slf4j.Slf4j;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpMethod;
import org.springframework.stereotype.Component;
import org.springframework.validation.BindingResult;
import org.springframework.web.multipart.MultipartFile;
import org.springframework.web.servlet.HandlerMapping;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Collection;
import java.util.Map;
import java.util.Objects;

import static indi.yuluo.xojbackgroundmanagmentsystem.utils.AspectjUtils.getNowTime;

/**
 * @Author: yuluo
 * @CreateTime: 2022-10-01  17:33
 * @Description: 参考ruoyi
 */


@Aspect
@Component
@Slf4j
public class LogAspect {

    @Autowired
    private XOJLogService operLogService;

    @Autowired
    private UserService userService;

    /**
     * 处理完请求后执行
     *
     * @param joinPoint 切点
     */
    @AfterReturning(pointcut = "@annotation(controllerLog)", returning = "jsonResult")
    public void doAfterReturning(JoinPoint joinPoint, Log controllerLog, Object jsonResult) {
        handleLog(joinPoint, controllerLog, null, jsonResult);
    }

    protected void handleLog(final JoinPoint joinPoint, Log controllerLog, final Exception e, Object jsonResult) {
        try {
            // 获取当前的用户
            String curUserId = BaseContext.getUuid();
            LambdaQueryWrapper<UserInfo> lambdaQueryWrapper = new LambdaQueryWrapper<>();
            lambdaQueryWrapper.eq(UserInfo::getUuid, curUserId);
            UserInfo curUser = userService.getOne(lambdaQueryWrapper);

            // 日志记录
            SysLog operLog = new SysLog();
            operLog.setStatus(0);

            // 请求的IP地址
            String iP = ServletUtil.getClientIP(AspectjUtils.getRequest());
            if ("0:0:0:0:0:0:0:1".equals(iP)) {
                iP = "127.0.0.1";
            }
            operLog.setOperIp(iP);
            operLog.setOperUrl(AspectjUtils.getRequest().getRequestURI());
            if (curUser != null) {
                operLog.setOperName(curUser.getUsername());
            }
            if (e != null) {
                operLog.setStatus(1);
                operLog.setErrorMsg(AspectjUtils.substring(e.getMessage(), 0, 2000));
            }

            // 设置方法名称
            String className = joinPoint.getTarget().getClass().getName();
            String methodName = joinPoint.getSignature().getName();
            operLog.setMethod(className + "." + methodName + "()");
            operLog.setRequestMethod(AspectjUtils.getRequest().getMethod());

            // 处理设置注解上的参数
            getControllerMethodDescription(joinPoint, controllerLog, operLog, jsonResult);

            // 保存数据库
            operLogService.saveLog(operLog);

        } catch (Exception exp) {
            log.error("异常信息:{}", exp.getMessage());
            exp.printStackTrace();
        }
    }

    /**
     * 获取注解中对方法的描述信息 用于Controller层注解
     *
     * @param log     日志
     * @param operLog 操作日志
     * @throws Exception
     */
    public void getControllerMethodDescription(JoinPoint joinPoint, Log log, SysLog operLog, Object jsonResult) throws Exception {

        // 设置操作业务类型
        operLog.setBusinessType(log.businessType().ordinal());

        // 设置标题
        operLog.setTitle(log.title());
        // 是否需要保存request，参数和值
        if (log.isSaveRequestData()) {
            // 设置参数的信息
            setRequestValue(joinPoint, operLog);
        }
        // 是否需要保存response，参数和值
        if (log.isSaveResponseData() && AspectjUtils.isNotNull(jsonResult)) {
            operLog.setJsonResult(AspectjUtils.substring(JSON.toJSONString(jsonResult), 0, 2000));
        }
    }

    /**
     * 获取请求的参数，放到log中
     *
     * @param operLog 操作日志
     * @throws Exception 异常
     */
    private void setRequestValue(JoinPoint joinPoint, SysLog operLog) throws Exception {

        String requsetMethod = operLog.getRequestMethod();

        if (HttpMethod.PUT.name().equals(requsetMethod) || HttpMethod.POST.name().equals(requsetMethod)) {
            String parsams = argsArrayToString(joinPoint.getArgs());
            operLog.setOperParam(AspectjUtils.substring(parsams, 0, 2000));
        } else {
            Map<?, ?> paramsMap = (Map<?, ?>) AspectjUtils.getRequest().getAttribute(HandlerMapping.URI_TEMPLATE_VARIABLES_ATTRIBUTE);
            operLog.setOperParam(AspectjUtils.substring(paramsMap.toString(), 0, 2000));
        }

    }

    /**
     * 参数拼装
     */
    private String argsArrayToString(Object[] paramsArray) {
        StringBuilder params = new StringBuilder();
        if (paramsArray != null && paramsArray.length > 0) {
            for (Object object : paramsArray) {
                // 不为空 并且是不需要过滤的 对象
                if (Objects.nonNull(object) && !isFilterObject(object)) {
                    Object jsonObj = JSON.toJSON(object);
                    params.append(jsonObj.toString()).append(" ");
                }
            }
        }
        return params.toString().trim();
    }

    /**
     * 判断是否需要过滤的对象。
     *
     * @param object 对象信息。
     * @return 如果是需要过滤的对象，则返回true；否则返回false。
     */
    @SuppressWarnings("rawtypes")
    public boolean isFilterObject(final Object object) {
        Class<?> clazz = object.getClass();
        if (clazz.isArray()) {
            return clazz.getComponentType().isAssignableFrom(MultipartFile.class);
        } else if (Collection.class.isAssignableFrom(clazz)) {
            Collection collection = (Collection) object;
            for (Object value : collection) {
                return value instanceof MultipartFile;
            }
        } else if (Map.class.isAssignableFrom(clazz)) {
            Map map = (Map) object;
            for (Object value : map.entrySet()) {
                Map.Entry entry = (Map.Entry) value;
                return entry.getValue() instanceof MultipartFile;
            }
        }
        return object instanceof MultipartFile || object instanceof HttpServletRequest
                || object instanceof HttpServletResponse || object instanceof BindingResult;
    }
}
```

### AOPUtils

```java
package indi.yuluo.xojbackgroundmanagmentsystem.utils;

import org.springframework.web.context.request.RequestAttributes;
import org.springframework.web.context.request.RequestContextHolder;
import org.springframework.web.context.request.ServletRequestAttributes;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.text.DateFormat;
import java.util.Date;
import java.util.Locale;
import java.util.logging.SimpleFormatter;

/**
 * @Author: yuluo
 * @CreateTime: 2022-10-01  17:49
 * @Description: AOP工具类
 */

public class AspectjUtils {

    private AspectjUtils() {}

    /**
     * 截取字符串
     *
     * @param str 字符串
     * @param start 开始
     * @return 结果
     */
    public static String substring(final String str, int start)
    {
        if (str == null)
        {
            return "";
        }

        if (start < 0)
        {
            start = str.length() + start;
        }

        if (start < 0)
        {
            start = 0;
        }
        if (start > str.length())
        {
            return "";
        }

        return str.substring(start);
    }

    /**
     * 截取字符串
     *
     * @param str 字符串
     * @param start 开始
     * @param end 结束
     * @return 结果
     */
    public static String substring(final String str, int start, int end)
    {
        if (str == null)
        {
            return "";
        }

        if (end < 0)
        {
            end = str.length() + end;
        }
        if (start < 0)
        {
            start = str.length() + start;
        }

        if (end > str.length())
        {
            end = str.length();
        }

        if (start > end)
        {
            return "";
        }

        if (start < 0)
        {
            start = 0;
        }
        if (end < 0)
        {
            end = 0;
        }

        return str.substring(start, end);
    }

    /**
     * * 判断一个对象是否为空
     *
     * @param object Object
     * @return true：为空 false：非空
     */
    public static boolean isNull(Object object)
    {
        return object == null;
    }

    /**
     * * 判断一个对象是否非空
     *
     * @param object Object
     * @return true：非空 false：空
     */
    public static boolean isNotNull(Object object)
    {
        return !isNull(object);
    }

    /**
     * 获取request
     */
    public static HttpServletRequest getRequest()
    {
        return getRequestAttributes().getRequest();
    }

    /**
     * 获取response
     */
    public static HttpServletResponse getResponse()
    {
        return getRequestAttributes().getResponse();
    }

    public static ServletRequestAttributes getRequestAttributes()
    {
        RequestAttributes attributes = RequestContextHolder.getRequestAttributes();
        return (ServletRequestAttributes) attributes;
    }

    /**
     * 获取系统时间并格式化
     */
    public static String getNowTime() {

        DateFormat df1 = DateFormat.getDateInstance(DateFormat.LONG, Locale.CHINA);
        DateFormat df2 = DateFormat.getTimeInstance(DateFormat.LONG, Locale.CHINA);

        String datePrefix = df1.format(new Date());
        String dateSuffix = df2.format(new Date());

        return new StringBuilder()
                .append(datePrefix)
                .append(" ")
                .append(dateSuffix)
                .toString();
    }

}
```

### 枚举类

```java
package indi.yuluo.xojbackgroundmanagmentsystem.Enum;

/**
 * @Author: yuluo
 * @CreateTime: 2022-10-01  19:39
 * @Description: 业务操作类型
 */
public enum BusinessType
{
    /**
     * 其它
     */
    OTHER("其他"),

    /**
     * 新增
     */
    INSERT("新增"),

    /**
     * 查询
     */
    SELECT("查询"),

    /**
     * 修改
     */
    UPDATE("修改"),

    /**
     * 删除
     */
    DELETE("删除"),

    /**
     * 授权
     */
    GRANT("授权"),

    /**
     * 导出
     */
    EXPORT("导出"),

    /**
     * 导入
     */
    IMPORT("导入"),

    /**
     * 强退
     */
    FORCE("强退"),

    /**
     * 清空
     */
    CLEAN("清空"),

    /**
     * 生成代码
     */
    GENCODE("生成代码"),
    ;

    private String des;

    BusinessType(String des) {
        this.des = des;
    }

    public String getDes() {
        return des;
    }

    public void setDes(String des) {
        this.des = des;
    }
}
```

### 在controller中使用

```java
/**
 * 获取XOJ后台数据库所有的用户信息
 *
 * @param page     页码
 * @param pageSize 每页展示的数据数
 * @return 分页对象
 */
@GetMapping("/getAll")
@Log(title = "查询用户信息", businessType = BusinessType.SELECT)
@CachePut(value = "allUser", key = "'user_' + #uuid + ':' + #page + '_' + #pageSize")
@ApiOperation(value = "获取XOJ后台数据库所有的用户信息", notes = "分页查询")
public Result<?> getAllXOJUser(int page, int pageSize) {

    String uuid = BaseContext.getUuid();

    Page<UserInfoAndUserRoleDTO> user = userService.getAll(page, pageSize);

    return Result.success(user);
}
```

## 总结

> 使用AOP可以完成在不侵入代码的前提下完成额外功能的添加，比如日志，事务，统计方法的执行的时间，从而对代码调优等。