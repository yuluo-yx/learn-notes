# 网络爬虫

网络爬虫（Web crawler）,是一种按照一定的规则，自动地抓取万维网信息的程序或脚本。

## 爬虫入门程序

### 环境

- jdk1.8
- idea
- idea自带maven

> 依赖

```java
 <!-- apache开源组织的jar包 -->
        <dependency>
            <groupId>org.apache.httpcomponents</groupId>
            <artifactId>httpclient</artifactId>
            <version>4.5.2</version>
        </dependency>

        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.25</version>
            <!--<scope>test</scope>-->
        </dependency>

```





> 第一个爬虫程序

```java
public static void main(String[] args) throws Exception {
        //1.打开浏览器，创建httpClient对象
        CloseableHttpClient client = HttpClients.createDefault();

        //2.输入网址,发起请求，创建HTTP对象
        HttpGet httpGet = new HttpGet("http://www.itcast.cn");

        //3.发起请求，返回响应
        CloseableHttpResponse response = client.execute(httpGet);

        //4.解析响应，获取数据
        if (response.getStatusLine().getStatusCode() == 200) {
            HttpEntity entity = response.getEntity();
            String content = EntityUtils.toString(entity, "UTF8");
            System.out.println(content);
        }

    }
```

## 网络爬虫

在大数据时代，信息的采集时一项很重要的工作，互联网中的数据数据是海量的，单靠人力去进行获取，不仅效率低，成本也会提高，如何自动高效获取互联网中我们感兴趣的信息成为一个很重要的问题，因此衍生了爬虫这种技术。

网络爬虫（web crawier）也叫网络机器人，可以代替人们进行自动的在互联网中进行数据的信息采集与整理，它能按照一定规则，自动抓取万维网信息的程序或脚本，可以自动采集所有其能够访问到的页面内容，以获取相关数据

从功能上看，爬虫一般分为数据采集，处理，存储三个部分。爬虫从一个或多个初始网页的url开始，获取初始网页上的url，在抓取网页的过程中，不断从当前页面上抽取新的url放入队列，直到满足系统的一定停止条件。

## 学习网络爬虫的原因

1. 实现搜索引擎
2. 大数据时代，可以让我们获取更多的数据源
3. 可以更好地进行搜索引擎优化（SED）
4. 有利于就业

## Httpclient

网络爬虫就是利用程序帮助我们访问网络上的资源，我们一直是使用http协议访问互联网的网页，网络爬虫也需要编写程序，在这里同样使用http协议访问网页

这里我们使用java的http协议客户端httpclient技术，实现抓取

### GET

> 不带参数

```java
public static void main(String[] args) throws Exception {
        //1.打开浏览器，创建httpClient对象
        CloseableHttpClient client = HttpClients.createDefault();

        //2.输入网址,发起请求，创建HTTP对象,设置url地址
        HttpGet httpGet = new HttpGet("https://https://aiqicha.baidu.com/");

        //配置请求信息
        RequestConfig requestConfig = RequestConfig.custom().setConnectTimeout(1000)//创建连接最长时间，单位是毫秒
                .setConnectionRequestTimeout(500)//获取连接的最大时间，单位毫秒
                .setSocketTimeout(10*1000)//数据传输的最大时间，单位毫秒
                .build();

        httpGet.setConfig(requestConfig);

        //3.发起请求，返回响应，获取response
        CloseableHttpResponse response = null;
        try {
            response = client.execute(httpGet);


            //4.解析响应，获取数据
            if (response.getStatusLine().getStatusCode() == 200) {
                HttpEntity entity = response.getEntity();
                String content = EntityUtils.toString(entity, "UTF8");
                System.out.println(content);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                response.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                client.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```





> 带参数

```java
public static void main(String[] args) throws Exception {
        //1.打开浏览器，创建httpClient对象
        CloseableHttpClient client = HttpClients.createDefault();

        //2.输入网址,发起请求，创建HTTP对象,设置url地址
        //创建URIbuilder
        URIBuilder uriBuilder = new URIBuilder("https://aiqicha.baidu.com/s");
        //设置参数
        uriBuilder.setParameter("q","中国石化").setParameter("t","0").setParameter("tp","click");
        HttpGet httpGet = new HttpGet(uriBuilder.build());
        RequestConfig build = RequestConfig.custom().setCookieSpec(CookieSpecs.STANDARD).build();
        httpGet.setConfig(build);


        //3.发起请求，返回响应，获取response
        CloseableHttpResponse response = null;
        try {
            response = client.execute(httpGet);

            //4.解析响应，获取数据
            if (response.getStatusLine().getStatusCode() == 200) {
                HttpEntity entity = response.getEntity();
                String content = EntityUtils.toString(entity, "UTF8");
                System.out.println(content);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                response.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                client.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```



### POST

>不带参数

```java
public static void main(String[] args) throws Exception {
        //1.打开浏览器，创建httpClient对象
        CloseableHttpClient client = HttpClients.createDefault();

        //2.输入网址,发起请求，创建HTTP对象,设置url地址
        HttpPost httpPost = new HttpPost("https://yun.ithelma.com/search");

        //3.发起请求，返回响应，获取response
        CloseableHttpResponse response = null;
        try {
            response = client.execute(httpPost);
            //4.解析响应，获取数据
            if (response.getStatusLine().getStatusCode() == 200) {
                HttpEntity entity = response.getEntity();
                String content = EntityUtils.toString(entity, "UTF8");
                System.out.println("content=========="+content.length()+"==============");
            }
        } catch (IOException e) {
            e.printStackTrace();
            logger.error("错误",e);
        } finally {
            try {
                response.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                client.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```





>带参数

```java
public static void main(String[] args) throws Exception {
        //1.打开浏览器，创建httpClient对象
        CloseableHttpClient client = HttpClients.createDefault();

        //2.输入网址,发起请求，创建HTTP对象,设置url地址
        HttpPost httpPost = new HttpPost("https://yun.ithelma.com/search");

        //声明list集合，封装表单中的参数
        List<NameValuePair> pairs = new ArrayList<NameValuePair>();
        pairs.add(new BasicNameValuePair("keys","java"));
        //创建表单的entity实体对象
        UrlEncodedFormEntity urlEncodedFormEntity = new UrlEncodedFormEntity(pairs, "UTF8");
        //设置表单的entity实体对象到post请求中
        httpPost.setEntity(urlEncodedFormEntity);



        //3.发起请求，返回响应，获取response
        CloseableHttpResponse response = null;
        try {
            response = client.execute(httpPost);


            //4.解析响应，获取数据
            if (response.getStatusLine().getStatusCode() == 200) {
                HttpEntity entity = response.getEntity();
                String content = EntityUtils.toString(entity, "UTF8");
                System.out.println("content=========="+content.length()+"==============");
            }
        } catch (IOException e) {
            e.printStackTrace();
            logger.error("错误",e);
        } finally {
            try {
                response.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            try {
                client.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
```





### 连接池

```java
public static void main(String[] args) {
        //创建连接池管理工具
        PoolingHttpClientConnectionManager cm = new PoolingHttpClientConnectionManager();

        //最大连接数设置
        cm.setMaxTotal(100);

        //设置每个主机的最大连接数
        cm.setDefaultMaxPerRoute(10);

        //使用连接池管理工具发起请求
        doGet(cm);

        doPost(cm);

    }


    public static void doGet(PoolingHttpClientConnectionManager cm) {
        CloseableHttpClient httpClient = HttpClients.custom().setConnectionManager(cm).build();
        HttpGet httpGet = new HttpGet("http://www.itcast.cn");
        CloseableHttpResponse response = null;
        try {
            response = httpClient.execute(httpGet);
            if (response.getStatusLine().getStatusCode() == 200) {
                HttpEntity entity = response.getEntity();
                String content = EntityUtils.toString(entity, "UTF8");
                System.out.println(content.length());
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                response.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            //由连接池管理httpClient，不能进行关闭
        }

    }


    public static void doPost(PoolingHttpClientConnectionManager cm) {
        CloseableHttpClient httpClient = HttpClients.custom().setConnectionManager(cm).build();
        HttpPost httpPost = new HttpPost("http://www.itcast.cn");
        CloseableHttpResponse response = null;
        try {
            response = httpClient.execute(httpPost);
            if (response.getStatusLine().getStatusCode() == 200) {
                HttpEntity entity = response.getEntity();
                String content = EntityUtils.toString(entity, "UTF8");
                System.out.println(content.length());
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                response.close();
            } catch (IOException e) {
                e.printStackTrace();
            }

        }

    }
```

**注意：**

当httpclient是通过连接池管理创建时，在请求结束后 ***不能对httpclient进行关闭*** 操作。



### 请求基础参数设置

```java
 //配置请求信息
        RequestConfig requestConfig = RequestConfig.custom().setConnectTimeout(1000)//创建连接最长时间，单位是毫秒
                .setConnectionRequestTimeout(500)//获取连接的最大时间，单位毫秒
                .setSocketTimeout(10*1000)//数据传输的最大时间，单位毫秒
                .build();

        httpGet.setConfig(requestConfig);
```



## 解析

我们抓取到页面后，需要对页面进行解析，可以使用字符串处理工具解析页面，，也可以使用正则表达式，但是这些方法会带来很大的开发成本，所有我们需要一款专门解析html页面的技术

### jsoup

jaoup时一款Java的html解析器，可以直接解析某个url地址，html文本内容，它提供了一套省力的api，可通过dom，css以及类似于jquery的操作方法来取出和操作数据。

**功能：**

1. 从一个url文件或字符串中解析html；
2. 使用dom或者css选择器来查找、取出数据；
3. 可操作html元素，属性，文本。



```java
 @Test
    public void testUrl()throws Exception {
        //解析url地址,url:访问的url；timeoutMillis：访问超时时间
        Document doc = Jsoup.parse(new URL("http://www.itcast.cn"), 10*5000);
        //使用标签选择器获取title标签中的内容
        String title = doc.getElementsByTag("title").first().text();
        System.out.println(title);
    }

```



**备注：**

虽然使用jsoup可以替代httpclient直接发起请求解析数据，但是往往不会这样用，因为在实际开发过程中，需要使用到多线程，连接池，代理等等方式，而jsoup对这些的支持并不友好，所以我们往往把jsoup作为html解析工具使用。



> 依赖

```java
<!-- https://mvnrepository.com/artifact/org.jsoup/jsoup -->
        <dependency>
            <groupId>org.jsoup</groupId>
            <artifactId>jsoup</artifactId>
            <version>1.10.2</version>
        </dependency>


        <!-- https://mvnrepository.com/artifact/junit/junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>

        <!-- https://mvnrepository.com/artifact/commons-io/commons-io -->
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.6</version>
        </dependency>


        <!-- https://mvnrepository.com/artifact/org.apache.commons/commons-lang3 -->
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-lang3</artifactId>
            <version>3.7</version>
        </dependency>
```







### 解析url

```java
@Test
    public void testUrl()throws Exception {
        //解析url地址,url:访问的url；timeoutMillis：访问超时时间
        Document doc = Jsoup.parse(new URL("http://www.itcast.cn"), 10*5000);
        //使用标签选择器获取title标签中的内容
        String title = doc.getElementsByTag("title").first().text();
        System.out.println(title);
    }
```



### 解析字符串

```java
@Test
    public void testString() throws Exception {
        //使用工具类读取文件，获取字符串
        String content = FileUtils.readFileToString(new File("C:\\Users\\ZKJH\\Desktop\\test.html"), "UTF8");
        //使用jsoup解析
        Document doc = Jsoup.parse(content);

        //使用标签选择器获取title标签中的内容
        String title = doc.getElementsByTag("title").first().text();
        System.out.println(title);
    }
```



### 解析文件

```java
@Test
    public void testFile() throws Exception {
        //使用工具类解析文件
        Document doc = Jsoup.parse(new File("C:\\Users\\ZKJH\\Desktop\\test.html"), "UTF8");

        //使用标签选择器获取title标签中的内容
        String title = doc.getElementsByTag("title").first().text();
        System.out.println(title);
    }
```

