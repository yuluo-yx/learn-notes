# java Httpclient发送get和post以及使用代理服务器

## 使用代理服务器

```java
package com.jhz.httpclient;

import org.apache.http.HttpEntity;
import org.apache.http.HttpHost;
import org.apache.http.NameValuePair;
import org.apache.http.client.config.RequestConfig;
import org.apache.http.client.entity.UrlEncodedFormEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.client.methods.HttpRequestBase;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicNameValuePair;
import org.apache.http.util.EntityUtils;

import java.io.FileWriter;
import java.io.IOException;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

/**
 * @author: yuluo
 * @FileName: ProxySelector.java
 * @createTime: 2022/4/26 19:05
 * @Description:
 */

public class ProxySelector {

    // 代理服务器
    final static String proxyHost = "XXXX";
    final static Integer proxyPort = 0;

    public static final String filePath = "F:\\Java\\Java-project\\Advanced-Framework\\homework\\src\\main\\resources\\index.htm";

    private static HttpHost proxy = null;

    private static RequestConfig reqConfig = null;

    static {
        proxy = new HttpHost(proxyHost, proxyPort, "http");
        reqConfig = RequestConfig.custom().setConnectionRequestTimeout(5000).setConnectTimeout(10000) // 设置连接超时时间
                .setSocketTimeout(10000) // 设置读取超时时间
                .setExpectContinueEnabled(false).setProxy(new HttpHost(proxyHost, proxyPort))
                .setCircularRedirectsAllowed(true) // 允许多次重定向
                .build();
    }

    public static void main(String[] args) {
        doGetRequest();
        // doPostRequest();
    }

    public static void doGetRequest() {
        // 目标地址
        String targetUrl = "http://aajy.net/";

        try {
            // 设置url参数 (可选)
            Map<String, String> urlParams = new HashMap<>();
            urlParams.put("uid", "1234567");
            String paramStr = EntityUtils.toString(new UrlEncodedFormEntity(paramsAdapter(urlParams), "UTF-8"));
            HttpGet httpGet = new HttpGet(targetUrl + "?" + paramStr);
            String result = doRequest(httpGet);
            writeFile(result);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
    * 将获取到信息写入index.htm文件中
    */
    private static void writeFile(String result) {
        FileWriter out = null;
        try {
            out = new FileWriter(filePath);
            out.write(result);
            out.flush();
            System.out.println("写入成功！");
        } catch(IOException e) {
            e.printStackTrace();
        } finally {
            if (out != null) {
                try {
                    out.close();
                } catch(IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static void doPostRequest() {
        try {
            // 要访问的目标页面
            HttpPost httpPost = new HttpPost("http://httpbin.org/post");

            // 设置表单参数
            Map<String, String> formParams = new HashMap<>();
            formParams.put("uid", "1234567");
            UrlEncodedFormEntity uefEntity = new UrlEncodedFormEntity(paramsAdapter(formParams), "utf-8");
            httpPost.setEntity(uefEntity);
            String result = doRequest(httpPost);
            System.out.println(result);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 设置请求头
     *
     * @param httpReq
     */
    private static void setHeaders(HttpRequestBase httpReq) {
        httpReq.setHeader("User-Agent",
                "Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/49.0.2623.110 Safari/537.36");
        httpReq.addHeader("xxx", "xxx");
    }

    /**
     * 执行请求
     *
     * @param httpReq
     * @return
     */
    public static String doRequest(HttpRequestBase httpReq) {
        String result = new String();
        httpReq.setConfig(reqConfig);
        try {
            // 设置请求头
            setHeaders(httpReq);

            CloseableHttpClient httpClient = HttpClients.createDefault();
            // 执行请求
            CloseableHttpResponse httpResp = httpClient.execute(httpReq);

            // 保存Cookie

            // 获取http code
            int statusCode = httpResp.getStatusLine().getStatusCode();
            System.out.println(statusCode);

            HttpEntity entity = httpResp.getEntity();
            if (entity != null) {
                result = EntityUtils.toString(entity, "utf-8");
            }

            httpResp.close();
            httpClient.close();
            httpReq.abort();
        } catch (Exception e) {
            e.printStackTrace();
            return null;
        }
        return result;
    }

    /**
     * 参数适配器，将系统定义的请求参数转换成HttpClient能够接受的参数类型
     *
     * @param map
     * @return HttpClient要求的参数类型
     */
    private static List<NameValuePair> paramsAdapter(Map<String, String> map) {
        List<NameValuePair> nvps = new ArrayList<NameValuePair>();

        for (Map.Entry<String, String> entry : map.entrySet()) {
            nvps.add(new BasicNameValuePair(entry.getKey(), entry.getValue()));
        }
        return nvps;
    }

}
```

## 发送get请求

```java
package com.jhz.httpclient;

import org.apache.http.Header;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;

import java.io.BufferedReader;
import java.io.InputStreamReader;
import java.util.Arrays;

/**
 * @author: yuluo
 * @FileName: SendGetRequest.java
 * @createTime: 2022/4/26 19:43
 * @Description: 使用Apache的HttpClient发送GET和POST请求的步骤如下:
 * 1. 使用帮助类HttpClients创建CloseableHttpClient对象.
 * 2. 基于要发送的HTTP请求类型创建HttpGet或者HttpPost实例.
 * 3. 使用addHeader方法添加请求头部,诸如User-Agent, Accept-Encoding等参数.
 * 4. 对于POST请求,创建NameValuePair列表,并添加所有的表单参数.然后把它填充进HttpPost实体.
 * 5. 通过执行此HttpGet或者HttpPost请求获取CloseableHttpResponse实例
 * 6. 从此CloseableHttpResponse实例中获取状态码,错误信息,以及响应页面等等.
 * 7. 最后关闭HttpClient资源.
 * <p>
 * 使用HttpClient调用接口
 * 为性能测试写接口脚本
 * https://baike.baidu.com/search/word?word=HTTP
 */

public class SendGetRequest {

    private static String url = "https://baike.baidu.com/search/word?word=HTTP";

    public static void main(String args[]) throws Exception {
        HttpGet httpGet = new HttpGet(url);

        // 设置类型 "application/x-www-form-urlencoded" "application/json"
        httpGet.setHeader("Content-Type", "application/x-www-form-urlencoded");
        System.out.println("调用URL: " + httpGet.getURI());

        // httpClient实例化
        CloseableHttpClient httpClient = HttpClients.createDefault();
        // 执行请求并获取返回
        HttpResponse response = httpClient.execute(httpGet);
        // System.out.println("Response toString()" + response.toString());
        HttpEntity entity = response.getEntity();
        System.out.println("状态码：" + response.getStatusLine().getStatusCode());

        // 显示结果
        BufferedReader reader = new BufferedReader(new InputStreamReader(entity.getContent(), "UTF-8"));
        String line = null;
        StringBuffer responseSB = new StringBuffer();
        while ((line = reader.readLine()) != null) {
            responseSB.append(line);
        }
        System.out.println("响应头：" + Arrays.toString(response.getAllHeaders()));
        System.out.println("响应体：" + responseSB);
        reader.close();

        httpClient.close();
    }
}
```

## 发送post请求

```java
package com.jhz.httpclient;

import com.alibaba.fastjson.JSON;
import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.message.BasicHeader;
import org.apache.http.util.EntityUtils;

import java.io.IOException;
import java.util.Arrays;

/**
 * @author: yuluo
 * @FileName: SendPostRequest.java
 * @createTime: 2022/4/26 20:03
 * @Description:
 */

public class SendPostRequest {

    public static void main(String[] args) {
        System.out.println(sendPost("https://baike.baidu.com", JSON.toJSONString("work=HTTP")));
    }

    public static String sendPost(String url, String data){
        String body = "";
        try {
            //创建httpclient对象
            CloseableHttpClient client = HttpClients.createDefault();
            //创建post方式请求对象
            HttpPost httpPost = new HttpPost(url);

            //装填参数
            StringEntity s = new StringEntity(data, "utf-8");
            s.setContentEncoding(new BasicHeader("contentType",
                    "application/json"));
            //设置参数到请求对象中
            httpPost.setEntity(s);
            // System.out.println("请求地址：" + url);

            //设置header信息
            //指定报文头【Content-type】、【User-Agent】
            //httpPost.setHeader("Content-type", "application/x-www-form-urlencoded");
            httpPost.setHeader("Content-type", "application/json");
            httpPost.setHeader("User-Agent", "Mozilla/4.0 (compatible; MSIE 5.0; Windows NT; DigExt)");

            //执行请求操作，并拿到结果（同步阻塞）
            CloseableHttpResponse response = client.execute(httpPost);
            //获取结果实体
            HttpEntity entity = response.getEntity();
            if (entity != null) {
                //按指定编码转换结果实体为String类型
                body = EntityUtils.toString(entity, "UTF-8");
                System.out.println("状态码：" + response.getStatusLine().getStatusCode());
                System.out.println("响应头：" + Arrays.toString(response.getAllHeaders()));
                System.out.println("响应体：" + body);
            }
            EntityUtils.consume(entity);
            //释放链接
            response.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return body;
    }

}
```

## pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.jhz</groupId>
    <artifactId>homework</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>


    <dependencies>
        <dependency>
            <groupId>com.alibaba</groupId>
            <artifactId>fastjson</artifactId>
            <version>1.2.60</version>
        </dependency>


        <!-- https://mvnrepository.com/artifact/org.eclipse.jetty/jetty-client -->
        <!--异步请求的jar-->
        <dependency>
            <groupId>org.eclipse.jetty</groupId>
            <artifactId>jetty-client</artifactId>
            <version>11.0.9</version>
        </dependency>



        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpcore</artifactId>
            <version>4.4.5</version>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpcore-nio</artifactId>
            <version>4.4.5</version>
        </dependency>
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpasyncclient</artifactId>
            <version>4.1.2</version>
        </dependency>

    </dependencies>


</project>
```