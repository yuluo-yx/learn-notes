# Spring boot 整合邮件实现验证码注册功能

> spring boot整合邮件实现验证码注册功能，发送HTML模板邮件。

效果展示：

![image-20221013160508089](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20221013160508089.png)

## 准备工作

### maven依赖

```xml
<!--发送邮件相关依赖-->
<!--邮箱验证码-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
<!--邮件模板-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
```

### 邮箱授权码

QQ邮箱为例

设置  -> 账户 -> 生成授权码

![image-20221013155843168](C:\Users\14815\AppData\Roaming\Typora\typora-user-images\image-20221013155843168.png)

## 编码

### controller

```java
package indi.yuluo.server.controller;

import indi.yuluo.common.model.Result;
import indi.yuluo.server.dto.EmailDTO;
import indi.yuluo.server.service.SendEmail;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiOperation;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.*;

import javax.annotation.Resource;
import javax.mail.MessagingException;

/**
 * @Author: yuluo
 * @CreateTime: 2022-10-13  12:22
 * @Description: TODO
 */

@Slf4j
@RestController
@RequestMapping("/email")
@Api("发送邮件控制器")
public class SendEmailController {

    @Resource
    private SendEmail sendEmail;

    /**
     * 发送邮件
     * @param email 收件人信息
     * @return 结果
     */
    @PostMapping("/sendEmail")
    @ApiOperation(value = "发送注册验证", httpMethod = "POST", notes = "邮箱发送")
    public Result<?> sendEmail(@RequestBody EmailDTO email) {

        email.setContent("");
        email.setSubject("xoj用户注册验证");

        String sendHtmlBool;
        try {
            sendHtmlBool = sendEmail.sendHtmlEmail(email);
        } catch (MessagingException e) {
            throw new RuntimeException("邮件发送失败！");
        }

        return Result.success(sendHtmlBool);

    }

}
```

### DTO

```java
package indi.yuluo.server.dto;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import javax.validation.constraints.NotBlank;
import javax.validation.constraints.NotEmpty;

/**
 * @Author: yuluo
 * @CreateTime: 2022-10-13  12:00
 * @Description: TODO
 */

@Data
@AllArgsConstructor
@NoArgsConstructor
public class EmailDTO {

    /**
     * 发送邮箱列表
     */
    @NotEmpty
    private String to;

    /**
     * 主题
     */
    @NotBlank
    private String subject;

    /**
     * 内容
     */
    @NotBlank
    private String content;

}
```

### Service

接口

```java
package indi.yuluo.server.service;

import indi.yuluo.server.dto.EmailDTO;

import javax.mail.MessagingException;

/**
 * @Author: yuluo
 * @CreateTime: 2022-10-13  12:06
 * @Description: TODO
 */

public interface SendEmail {

    /**
     * 发送邮件
     * @param email
     * @return
     */
    String sendHtmlEmail(EmailDTO email) throws MessagingException;

}
```

实现类

```java
package indi.yuluo.server.service.impl;

import indi.yuluo.server.dto.EmailDTO;
import indi.yuluo.server.service.SendEmail;
import indi.yuluo.server.utils.ValidateCodeUtils;
import lombok.extern.slf4j.Slf4j;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.mail.javamail.JavaMailSender;
import org.springframework.mail.javamail.MimeMessageHelper;
import org.springframework.stereotype.Service;
import org.thymeleaf.TemplateEngine;
import org.thymeleaf.context.Context;

import javax.annotation.Resource;
import javax.mail.MessagingException;
import javax.mail.internet.MimeMessage;
import java.util.Arrays;
import java.util.Date;

/**
 * @Author: yuluo
 * @CreateTime: 2022-10-13  12:06
 * @Description: TODO
 */

@Slf4j
@Service
public class SendHtmlEmailImpl implements SendEmail {

    @Resource
    private JavaMailSender mailSender;

    @Resource
    private TemplateEngine templateEngine;

    /**
     * 发送邮件
     * @param email
     * @return
     */
    @Override
    public String sendHtmlEmail(EmailDTO email) throws MessagingException {

        log.info(email.getTo());

        MimeMessage mimeMessage = mailSender.createMimeMessage();
        MimeMessageHelper helper = new MimeMessageHelper(mimeMessage,true, "UTF-8");
        helper.setSubject("XOJ注册验证码"); // 邮件的标题
        helper.setFrom("yuluo829@qq.com"); // 发送者
        helper.setTo(email.getTo());    // 接受者
        helper.setSentDate(new Date()); //时间

        // 这里引入的是Template的Context
        Context context = new Context();
        //设置模板中的变量
        context.setVariable("verifyCode", Arrays.asList(ValidateCodeUtils.generateValidateCode4String(6).split("")));
        // 第一个参数为模板的名称
        String process = templateEngine.process("email.html", context); //这里不用写全路径
        // 第二个参数true表示这是一个html文本
        helper.setText(process,true);

        mailSender.send(mimeMessage);

        return "发送成功！";
    }
}
```

### HTML模板

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8">
  <title>邮箱验证码</title>
  <style>
    table {
      width: 700px;
      margin: 0 auto;
    }

    #top {
      width: 700px;
      border-bottom: 1px solid #ccc;
      margin: 0 auto 30px;
    }

    #top table {
      font: 12px Tahoma, Arial, 宋体;
      height: 40px;
    }

    #content {
      width: 680px;
      padding: 0 10px;
      margin: 0 auto;
    }

    #content_top {
      line-height: 1.5;
      font-size: 14px;
      margin-bottom: 25px;
      color: #4d4d4d;
    }

    #content_top strong {
      display: block;
      margin-bottom: 15px;
    }

    #content_top strong span {
      color: #f60;
      font-size: 16px;
    }

    #verificationCode {
      color: #f60;
      font-size: 24px;
    }

    #content_bottom {
      margin-bottom: 30px;
    }

    #content_bottom small {
      display: block;
      margin-bottom: 20px;
      font-size: 12px;
      color: #747474;
    }

    #bottom {
      width: 700px;
      margin: 0 auto;
    }

    #bottom div {
      padding: 10px 10px 0;
      border-top: 1px solid #ccc;
      color: #747474;
      margin-bottom: 20px;
      line-height: 1.3em;
      font-size: 12px;
    }

    #content_top strong span {
      font-size: 18px;
      color: #FE4F70;
    }

    #sign {
      text-align: right;
      font-size: 18px;
      color: #FE4F70;
      font-weight: bold;
    }

    #verificationCode {
      height: 100px;
      width: 680px;
      text-align: center;
      margin: 30px 0;
    }

    #verificationCode div {
      height: 100px;
      width: 680px;

    }

    .button {
      color: #FE4F70;
      margin-left: 10px;
      height: 80px;
      width: 80px;
      resize: none;
      font-size: 42px;
      border: none;
      outline: none;
      padding: 10px 15px;
      background: #ededed;
      text-align: center;
      border-radius: 17px;
      box-shadow: 6px 6px 12px #cccccc,
      -6px -6px 12px #ffffff;
    }

    .button:hover {
      box-shadow: inset 6px 6px 4px #d1d1d1,
      inset -6px -6px 4px #ffffff;
    }

  </style>
</head>
<body>
<table>
  <tbody>
  <tr>
    <td>
      <div id="top">
        <table>
          <tbody><tr><td></td></tr></tbody>
        </table>
      </div>

      <div id="content">
        <div id="content_top">
          <strong>尊敬的用户：您好！</strong>
          <strong>
            您正在进行<span>注册账号</span>操作，请在验证码中输入以下验证码完成操作：
          </strong>
          <div id="verificationCode">
            <button class="button" th:each="a:${verifyCode}">[[${a}]]</button>
          </div>
        </div>
        <div id="content_bottom">
          <small>
            注意：此操作可能会修改您的密码、登录邮箱或绑定手机。如非本人操作，请及时登录并修改密码以保证帐户安全
            <br>（工作人员不会向你索取此验证码，请勿泄漏！)
          </small>
        </div>
      </div>
      <div id="bottom">
        <div>
          <p>此为系统邮件，请勿回复<br>
            请保管好您的邮箱，避免账号被他人盗用
          </p>
          <p id="sign">Xcode Online Judge</p>
        </div>
      </div>
    </td>
  </tr>
  </tbody>
</table>
</body>
```

### yml配置

```yml
spring
  # 邮箱配置
  mail:
    host: smtp.qq.com
    username: yuluo829@qq.com
    password: axrdumdofnuugeaf
    port: 465
    protocol: smtp
    default-encoding: utf-8
    properties:
      mail:
        smtp:
          auth: true
          starttls:
            enable: true
            required: true
          ssl:
            enable: true
          socketFactory:
            port: 465
            class: javax.net.ssl.SSLSocketFactory
            
# 模板配置
thymeleaf:
  prefix: classpath:/template/  #prefix：指定模板所在的目录
  check-template-location: true  #check-tempate-location: 检查模板路径是否存在
  cache: true  #cache: 是否缓存，开发模式下设置为false，避免改了模板还要重启服务器，线上设置为true，可以提高性能。
  suffix: .html
  #encoding: UTF-8
  #content-type: text/html
  mode: HTML
```