# java IO流概述

## 什么是IO流

I：Input 输入

O：Output 输出

- 完成对硬盘文件的读写操作

## IO分类

- 按照流的方向区分：**输入流 输出流**
  - 输入内存中，叫做输入，或者叫做读
  - 从内存中输出，叫做输出，或者叫做写
- 按照读取数据方式分类：**字节流 字符流**
  - 按照字节读取，一次读取一个字节，等同于一次读取8个二进制位  **万能流**
  - 按照字符的方式读取，一次读取一个字符，为了方便读取文本文件而存在的，**不能读取视频，图片，声音等文件**

## 流的四大分支

以stream结尾的为字节流

以reader和writer结尾的字符流

- java.io.InputStream   字节输入流
- java.io.OutputStream  字节输出流
- java.io.Reader  字符输入流
- java.io.Writer  字符输入出流

四大分支都是抽象类，都继承了java.io.Closeable,Flushable ，因此所有的流都是可关闭和可刷新的。

**输出流再最终输出之后，一定要记得刷新，使用flush()方法，清空管道输出剩余的数据**

**流是非常占资源的，使用完毕之后记得关闭流对象**

## 需要掌握的io流

	文件专属：
		FileInputStream
		FileOutputStream
		FileReader
		FileWriter
	
	缓冲流专属：
		BufferedReader
		BufferedWriter
		BufferedInputStream
		BufferedOutputStream
	
	转换流：（将字节流转换成为字符流）
		InputStreamReader
		OutputStreamWriter
	
	对象专属流：
		ObjectInputStream
		ObjectOutputStream
	
	数据流专属：
		DataInputStream
		DataOutputStream
	
	标准输出流：
		PrintWriter
		PrintStream



## IDEA的相对路径

**IDEA默认的当前路径是工程project的根就是IDEA的默认当前路径**

所有路径的开始都会默认从项目的根路径开始查找

D:

|--javaProject

|			|--demo01

|						|--src

|								|--tempFile1.txt

|			|--demo02

|			|--demo03

tempFile1.txt的相对路径为：`demo01/src/com/tempFile1.txt`

## 文件专属流

FileInputStream
FileOutputStream

FileReader
FileWriter

### FileInputStream

```java
FileInputStream fis = null;
try {
    // 创建文件字节输入流对象
	fis = new FileInputStream(String path);
    
    // 读取文件
    /*// 这个方法的返回值是：读取到的“字节”本身 （ASCII码值）
    int readData = fis.read(); 
    System.out.println(readData);*/
    
    // 准备一个byte数组
    // byte[] bytes = new byte[4];
    
    /*while(true) {
        int readCount = fis.read(byte);
        if (readCount == -1) {
            break;
        }
        // 把byte数组转换成为字符串
        System.out.println(new String(bytes, 0, readCount));
    }*/
    
    /*int readCount = 0;
    while((readCount = fis.read(bytes)) != -1) {
        System.out.println(new String(bytes, 0, readCount));
    }*/
    
    // 根据文件字节大小，动态设定byte数组长度 读取一次即可
    byte[] bytes = new byte[fix.available()];
    int readCount = 0;
    while((readCount = fis.read(bytes)) != -1) {
        System.out.println(new String(bytes));
    }
    
} catch (IOException e) {
    e.printStackTrace();
} finally {
    // 关闭流
    if (fis != null) {
        try {
            fis.close();
        } cathc (IOException e) {
            e.printStackTrace();
        }
    }
}

```

- 其他方法：
  - int available(); 返回流当中剩余的没有读到的字节数量
  - long skip(); 跳过几个字节不读

### FileOutputStream

```java
FileOutputStream fos = null;
try {
    // 创建文件字节输出流对象
    // myfilename文件不存在的时候会自动新建
	fos = new FileOutputStream("myfilename");
    
    // 以追加的方式在文件末尾写入
    // fos = new FileOutputStream("myfilename"， true);
    
    // 开始写
    // 这种方式会覆盖写入
    byte[] bytes = {97, 98, 99 ,100};
    fos.write(bytes);
    
    // 带参数的write方法 写入bytes数组的0-2个元素。
    // fos.write(bytes, 0, 2);
    
    // 最后一定要刷新
    fos.flush();
} catch (IOException e) {
    e.printStackTrace();
} finally {
    // 关闭流
    if (fos != null) {
        try {
            fos.close();
        } cathc (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 复制文件的方法

```java
/// 核心代码

byte[] bytes = new byte[1024 * 1024];

int readCount = 0;
// 一边读取一边写入
while((readCount = fis.read(bytes)) != -1) {
    fos.write(bytes, 0, readCount);
}
```

### FileReader

文件字符输入流，只能读取普通文本文件，读取文本内容时，方便，快捷

```java
FileReader reader = null;

try {
    // 创建文件字符输入流对象
    reader = new FileReader("myfile");
    
    // 开始读
    char[] chars = new char[4];  //一次读取4个字符
    int readCount = 0;
    while((readCount = reader.read(chars)) != -1) {
        System.out.println(new String(chars, 0, readCount));
    }
    
} catch(IOException e) {
    e.printStackTrace();
} finally {
    if (reader != null) {
        try {
            reader.close();
        } catch(IOException e) {
            e.printStackTrace();
        }
    }
}
```

### FileWriter

文件字符输出流 用于写文件，只能输出普通文本

```java
FileWriter out = null;

try {
    // 创建文件字符输出流对象
    // out = new FileWriter("myfile"); 
    // 追加
    out = new FileWriter("myfile", true); 
    
    // 开始写
    char[] chars = {'我', '是', '谁'};
    out.write(chars);
    // 限定位置
    // out.write(chars, 0, 1);
    String data = "我是一名java软件工程师";
    out.write(data);

    // 刷新
    out.flush();
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
```

### 复制文本文件

```java
char[] chars = new char[024 * 1024];  // 1MB
int readCount = 0;
while((readCount = in.read(chars) != -1) {
    out.write(chars, 0, readCount);
}
```

## 缓冲流

BufferedReader
BufferedWriter

### BufferedReader

带有缓冲区的字符输入流

使用这个流的时候不需要自定义char或者是byte数组，自带缓冲

```java
FileReader in = new FileReader("test.txt");

/*当一个流的构造方法中需要一个流的时候，这个被传进来的流叫做：节点流
外部负责包装的这个流叫做：包装流，还有一个名字叫做：处理流
节点流：FileReader
包装流：BufferReader*/
 
BufferedReader reader = new BufferedReader(in);

// 读取一行数据
String data = reader.readLine();
System.out.println(data);

// 重复读取数据
String s = null;
while((s = reader.readLine()) != null) {
    // 读不到最终的换行符
    System.out.println(s);
}

// 从控制台读取一行数据
BufferedReader bufferedReader = new BufferedReader(new InputStreamReader(System.in));
System.out.println(bufferedReader.readLine());

// 关闭的时候只需要关闭外层的包装流即可
reader.close();
bufferedReader.close();
```

### BufferedWriter

```java
BufferWriter out = new BufferWriter(new FileWriter("test"));
// 开始写
out.write("hello world!");

// 刷新
out.flush();
// 关闭最外层流
out.close();
```

## 数据流

DataInputStream

DataOutputStream

数据专属的流

这个流可以将数据连通数据的类型一并写入文件，这个文件不是普通文本文档

**DataInputStream写入的数据只能DataOutputStream读取，记事本打不开**

**必须要按照写入的顺序读取数据**

### DataInputStream

```java
// 创建数据专属的字节输出流
DataOutputStream dataOutputStream = new DataOutputStream(new FileOutputStream("data", true));

// 写数据
// 把数据以及数据的类型一并写到文件中
byte b = 100;
int i = 10;
char chars = '我';

dataOutputStream.writeByte(b);
dataOutputStream.writeChar(chars);
dataOutputStream.writeInt(i);

// 刷新
dataOutputStream.flush();
// 关闭最外层
dataOutputStream.close();
```

### DataOutputStream

```java
DataInputStream dataInputStream = new DataInputStream(new FileInputStream("data"));
// 开始读
byte b = dataInputStream.readByte();
char c = dataInputStream.readChar();
int i = dataInputStream.readInt();

System.out.println(b);
System.out.println(i);
System.out.println(c);
System.out.println("读取数据完成！");

// 关闭流
dataInputStream.close();
```

## 标准输出流

PrintWriter
PrintStream

**将数据输出到控制台**

标准输出流不需要关闭

### PrintWriter

和PrintStream相似

### PrintStream

```java
// 联合写
System.out.println("hello world!");

// 分开写
PrintStream ps = System.out;
ps.println("hello world!");

// 可以改变标准输出流的输出方向  使其指向日志文件
System.setOut(new PrintStream(new FileOutputStream("log")));
```

## File类的常用方法

File类和四大分支没有任何关系，所以File类不能完成文件的读和写

一个File对象可能是一个目录也可能是一个文件

是一个路径名的抽象表示形式

### 常用方法

```java
File file = new File("D:\\file");

// 判断时候存在
boolean flag = file.exists();

// 以文件的形式创建
if (!file.exists()) {
    file.createNewFile();
}

// 以目录形式创建
if (!file.exists()) {
    file.mkdir();
}

// 以多重目录形式新建
mkdirs();

// 获取文件的父路径
String parentPath = file.getParent();
File parentFile = file.getParentFile();

// 获取绝对路径
String absPath = file.getAbslutPath();

// 获取文件名
String fileName = file.getName();

// 判断是否是一个文件或者目录
isDirectory();
isFile();

// 获取文件最后一次修改时间
long haoMiao = file.lastModified();
Date time = new Date(haoMiao);
SimpleDataFormat  sdf = new SimpleDataFormat("yyyy-MM-dd HH:mm:ss SS");
String strTime = sdf.format(time);
System.out.println(time);

// 获取文件大小
file.length();

// listFiles()方法
// 获取当前目录下所有的子文件
File[] files = f.listFiles();
for (File f : files) {
    System.out.prinltn(f);
}
```

## 序列化与反序列化

参与序列化和反序列化的对象必须实现Serializeable 接口

- 序列化：Serialize java对象存储到文件中，将java对象状态保存下来的过程  （ObjectOutputStream）
- 反序列化：DeSerialize 将硬盘上的数据重新恢复到内存当中，恢复成java对象 （ObjectInputStream）

transient关键字：

> 表示游离的，不参与序列化

