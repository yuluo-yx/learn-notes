# JWT在spring boot中的应用

## 介绍

JWT：全称为JSON Web Token ，通过数字签名的方式，以json对象为载体，在不同的服务终端之间安全的传输信息

### 作用

JWT是最常见的场景就是授权认证，一旦用户登录，后续每个请求都将包含JWT，系统在每次处理用户请求之前，都要先进性JWT安全校验，通过之后在进行处理。

### 组成部分

JWT由三部分组成，由 . 号进行拼接

![image-20220826105631372](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20220826105631372.png)

这三部分分别是：

1. Header

   ```java
   {
       "typ": 'JWT',
       'alg': 'HS256'
   }
   ```

   对信息进行base64编码加密，构成JWT的第一部分

2. Payload(载荷)

   ```java
   {
       "sub": "1234567890",
       "name": "john",
       "admin": true
   }
   ```

   同样需要进行base64编码加密，构成第二部分

3. signature

   ```java
   // 进行拼接
   var encodeString = base64UrlEncode(header) + "." + base63UrlEncode(payload);
   
   //加盐（secret）加密 加密算法是Header中的HS256加密算法
   var signature = HMACSHA256(encoding, 'secret');
   ```

   构成第三部分

## 使用

### 引入依赖

```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.9.1</version>
</dependency>
```

如果是高版本的JDK，需要引入如下依赖，JDK8不用

```xml
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.3.0</version>
</dependency>
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-core</artifactId>
    <version>2.3.0</version>
</dependency>
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-impl</artifactId>
    <version>2.3.0</version>
</dependency>
<dependency>
    <groupId>javax.activation</groupId>
    <artifactId>activation</artifactId>
    <version>1.1.1</version>
</dependency>
```

##  在spring boot中的应用

### 请求流程

![img](D:\information\Typora笔记\java framework\JWT在spring boot中的应用.assets\900b3e81f832b2f08c2e8aabb540536a.png)

如上图所示：
        1. 用户导航到登录页，输入用户名和密码，进行登录
        2. 服务器对登录用户进行认证，如果认证通过，根据用户的信息和JWT的生成规则生成JWT Token
        3. 服务器将该Token字符串返回
        4.  客户端得到Token信息，将Token存储在localStorage、sessionStorage或cookie等存储形式中。
        5. 当用户请求服务器API时，在请求的Header中加入 Authorization：Token。
        6.  服务端对此Token进行校验，如果合法就解析其中内容，根据其拥有的权限和自己的业务逻辑给出响应结果，如果不通过，返回HTTP 401。
        7. 用户进入系统，获得请求资源

### JWT工具类

```java
package indi.yuluo.xojbackgroundmanagmentsystem.utils;

import io.jsonwebtoken.Claims;
import io.jsonwebtoken.JwtBuilder;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;

import javax.crypto.SecretKey;
import javax.crypto.spec.SecretKeySpec;
import java.util.Base64;
import java.util.Date;
import java.util.UUID;

/**
 * @Author: yuluo
 * @CreateTime: 2022-08-26  15:54
 * @Description: TODO
 */

public class JwtUtil {

    //有效期为
    public static final Long JWT_TTL = 60 * 60 *1000L;// 60 * 60 *1000  一个小时
 
    //设置秘钥明文  注意这里的key长度，至少大于10位 可能会出现问题 Last unit does not have enough valid bits
    public static final String JWT_KEY = "yuluo089026loveyou";

    /**
     * 生成用户的uuid == token
     * @return
     */
    public static String getUUID(){
        return UUID.randomUUID().toString().replaceAll("-", "");
    }

    /**
     * 生成jtw
     * @param subject token中要存放的数据（json格式）
     * @return
     */
    public static String createJWT(String subject) {
        JwtBuilder builder = getJwtBuilder(subject, null, getUUID());// 设置过期时间
        return builder.compact();
    }

    /**
     * 生成jwt
     * @param subject token中要存放的数据（json格式）
     * @param ttlMillis token超时时间
     * @return
     */
    public static String createJWT(String subject, Long ttlMillis) {
        JwtBuilder builder = getJwtBuilder(subject, ttlMillis, getUUID());// 设置过期时间
        return builder.compact();
    }

    /**
     * 创建token
     * @param id
     * @param subject
     * @param ttlMillis
     * @return
     */
    public static String createJWT(String id, String subject, Long ttlMillis) {
        JwtBuilder builder = getJwtBuilder(subject, ttlMillis, id);// 设置过期时间
        return builder.compact();
    }


    private static JwtBuilder getJwtBuilder(String subject, Long ttlMillis, String uuid) {
        SignatureAlgorithm signatureAlgorithm = SignatureAlgorithm.HS256;
        SecretKey secretKey = generalKey();
        long nowMillis = System.currentTimeMillis();
        Date now = new Date(nowMillis);
        if(ttlMillis==null){
            ttlMillis=JwtUtil.JWT_TTL;
        }
        long expMillis = nowMillis + ttlMillis;
        Date expDate = new Date(expMillis);
        return Jwts.builder()
                .setId(uuid)              //唯一的ID
                .setSubject(subject)   // 主题  可以是JSON数据
                .setIssuer("yuluo")     // 签发者
                .setIssuedAt(now)      // 签发时间
                .signWith(signatureAlgorithm, secretKey) //使用HS256对称加密算法签名, 第二个参数为秘钥
                .setExpiration(expDate);
    }



    /**
     * 生成加密后的秘钥 secretKey
     * @return
     */
    public static SecretKey generalKey() {
        byte[] encodedKey = Base64.getDecoder().decode(JwtUtil.JWT_KEY);
        SecretKey key = new SecretKeySpec(encodedKey, 0, encodedKey.length, "AES");
        return key;
    }

    /**
     * 解析
     *
     * @param jwt
     * @return
     * @throws Exception
     */
    public static Claims parseJWT(String jwt) throws Exception {
        SecretKey secretKey = generalKey();
        return Jwts.parser()
                .setSigningKey(secretKey)
                .parseClaimsJws(jwt)
                .getBody();
    }
}


```

### 在controller中使用

```java
/**
 * 用户登录方法
 * @param username 用户名
 * @param password 用户密码
 * @return
 */
@PostMapping("/login")
@ApiOperation(value = "用户登录方法", httpMethod = "POST")
public Result<?> login(
        @RequestParam(value = "username") String username,
        @RequestParam(value = "password") String password
) {

    String jwtToken = null;
    UserInfo curUser = userService.getUserByUserNameAndCheckUserRole(username, password);

    if (Objects.nonNull(curUser)) {
        // 可以登录，生成jwt token凭证 并返回给前端
        // 错误交由globalExceptionHandler处理异常
        JwtUtil.createJWT(String.valueOf(curUser.getUuid()), 1000L * 60 * 60 * 24);
    }

    return Result.success(jwtToken);

}
```



