# Java NIO

## java NIO概述

Java NIO: （New IO 或 Non Blocking IO）是从java1.4版本开始引入的一个新的IO API。可以替代标准的Java Io Api。NIO支持面向缓冲区的，基于通道的IO操作，NIO将以更加高效的方式进行文件的读写操作

### 阻塞 BIO

阻塞 IO（BIO）是最传统的一种 IO模型，即在读写数据过程中会发生阻塞现象，直至 有可供读取的数据或者数据能够写入。
（1）在BIO模式中，服务器会为每个客户端请求建立一个线程，由该线程单独负责 处理一个客户请求，这种模式虽然简单方便，但由于服务器为每个客户端的连接都采 用一个线程去处理，使得资源占用非常大。因此，当连接数量达到上限时，如果再有 用户请求连接，直接会导致资源瓶颈，严重的可能会直接导致服务器崩溃。
（2）大多数情况下为了避免上述问题，都采用了线程池模型。也就是创建一个固定大 小的线程池，如果有客户端请求，就从线程池中取一个空闲线程来处理，当客户端处 理完操作之后，就会释放对线程的占用。因此这样就避免为每一个客户端都要创建线 程带来的资源浪费，使得线程可以重用。但线程池也有它的弊端，如果连接大多是长 连接，可能会导致在一段时间内，线程池中的线程都被占用，那么当再有客户端请求 连接时，由于没有空闲线程来处理，就会导致客户端连接失败。

### 非阻塞IO（NIO）

基于BIO的各种弊端，在 JDK1.4开始出现了高性能 IO设计模式非阻塞 IO（NIO）。 （1）NIO采用非阻塞模式，基于Reactor 模式的工作方式，I/O调用不会被阻塞，它 的实现过程是：会先对每个客户端注册感兴趣的事件，然后有一个线程专门去轮询每 个客户端是否有事件发生，当有事件发生时，便顺序处理每个事件，当所有事件处理完之后，便再转去继续轮询。

NIO中实现非阻塞 I/O的核心对象就是 Selector，Selector 就是注册各种 I/O 事件地方，而且当我们感兴趣的事件发生时，就是这个对象告诉我们所发生的事件，

NIO 的最重要的地方是当一个连接创建后，不需要对应一个线程，这个连接会被 注册到多路复用器上面，一个选择器线程可以同时处理成千上万个连接，系统不必创
建大量的线程，也不必维护这些线程，从而大大减小了系统的开销。

### 异步非阻塞IO AIO

（1）AIO 也就是 NIO 2，在 Java 7 中引入了 NIO 的改进版 NIO 2,它是异步非阻塞 的 IO模型。异步 IO 是基于事件和回调机制实现的，也就是说AIO模式不需要selector 操作，而是是事件驱动形式，也就是当客户端发送数据之后，会主动通知服 务器，接着服务器再进行读写操作。
（2）Java 的AIO API 其实就是Proactor 模式的应用，和Reactor 模式类似。 Reactor 和Proactor 模式的主要区别就是真正的读取和写入操作是有谁来完成的， Reactor 中需要应用程序自己读取或者写入数据，而Proactor 模式中，应用程序不需 要进行实际的读写过程，它只需要从缓存区读取或者写入即可，操作系统会读取缓存
区或者写入缓存区到真正的 IO设备。

### NIO概述

Java NIO 由以下几个核心部分组成： -

- Channels：

  ```markdown
  首先说一下Channel，可以翻译成“通道”。Channel 和 IO中的Stream(流)是差不 多一个等级的。只不过 Stream是单向的，譬如：InputStream, OutputStream.而 Channel 是双向的，既可以用来进行读操作，又可以用来进行写操作。
  NIO中的Channel 的主要实现有：FileChannel、DatagramChannel、 SocketChannel 和 ServerSocketChannel，这里看名字就可以猜出个所以然来：分别
  可以对应文件 IO、UDP和TCP（Server 和Client）。
  ```

  

- Buffers :

  ```mark
  NIO中的关键Buffer 实现有：ByteBuffer, CharBuffer, DoubleBuffer, FloatBuffer, IntBuffer, LongBuffer, ShortBuffer，分别对应基本数据类型: byte, char, double,
  float, int, long, short。
  ```

  

- Selectors:

  ```mark
  Selector 运行单线程处理多个Channel，如果你的应用打开了多个通道，但每个连接 的流量都很低，使用 Selector 就会很方便。例如在一个聊天服务器中。要使用 Selector, 得向Selector 注册Channel，然后调用它的 select()方法。这个方法会一直 阻塞到某个注册的通道有事件就绪。一旦这个方法返回，线程就可以处理这些事件，
  事件的例子有如新的连接进来、数据接收等。
  ```

  

  虽然 Java NIO 中除此之外还有很多类和组件，但Channel，Buffer 和 Selector 构成 了核心的API。其它组件，如Pipe 和 FileLock，只不过是与三个核心组件共同使用的
  工具类。

## java NIO（channel）

### 概述

Java NIO的通道类似流，但又有些不同： 

> 既可以从通道中读取数据，又可以写数据到通道。但流的读写通常是单向的。
>
> 通道可以异步地读写。 
>
> 通道中的数据总是要先读到一个Buffer，或者总是要从一个Buffer 中写入。

正如上面所说，从通道读取数据到缓冲区，从缓冲区写入数据到通道。

### 实现

下面是 Java NIO中最重要的Channel 的实现：

 FileChannel

 DatagramChannel 

SocketChannel

ServerSocketChannel 

​		（1）FileChannel 从文件中读写数据。 

​		（2）DatagramChannel 能通过UDP读写网络中的数据。 

​		（3）SocketChannel 能通过 TCP读写网络中的数据。
​		（4）ServerSocketChannel 可以监听新进来的TCP连接，像Web服务器那样。对 每一个新进来的连接都			会创建一个 SocketChannel。
正如你所看到的，这些通道涵盖了UDP 和 TCP 网络 IO，以及文件 IO

### FileChannel操作详解

#### FileChannel的详细使用

![image-20220531191914158](E:\Typora笔记\java framework\mybatisplus\images\image-20220531191914158.png)

#### 打开FileChannel：

```mark
在程序中，无法直接打开一个channel，只能通过InputStream，OutputStream，RandomAccessFile来获取一个FileChannel实例。
```

#### 读操作

代码示例：

```java
package indi.yuluo.nio.channel;

import java.io.FileNotFoundException;
import java.io.IOException;
import java.io.RandomAccessFile;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

/**
 * @author: yuluo
 * @FileName: FileChannelTest01.java
 * @createTime: 2022/5/31 19:07
 * @Description: FileChannel读取数据到buffer中
 */

public class FileChannelTest01 {

    public static void main(String[] args) throws IOException {

        // 创建FileChannel
        RandomAccessFile accessFile = new RandomAccessFile("F:\\Java\\Java-project\\NIOAndJUC\\NIO\\file\\fileChannel.txt", "rw");
        // 通过文件获取一个channel对象
        FileChannel channel = accessFile.getChannel();

        // 创建buffer
        ByteBuffer buffer = ByteBuffer.allocate(1024);

        // 读取数据到buffer中
        // read表示读到的数据内容
        int read = channel.read(buffer);

        while (read != -1) {
            System.out.println("读取了：" + read);
            // 读写模式转换 将读转换成写，之前代码是读数据到buffer中，之后的代码是写
            buffer.flip();
            // buffer中是否有剩余内容
            while (buffer.hasRemaining()) {
                System.out.print((char) buffer.get());
            }
            buffer.clear();
            read = channel.read(buffer);
        }
        // 关闭
        accessFile.close();
        System.out.println();
    }

}
```

####  FileChannel写操作

```java
package indi.yuluo.nio.channel;

import java.io.RandomAccessFile;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;

/**
 * @author: yuluo
 * @FileName: FileChannelTest02.java
 * @createTime: 2022/5/31 19:19
 * @Description:  向channel中写数据
 */

public class FileChannelTest02 {

    public static void main(String[] args) throws Exception {

        // 打开Filechannel
        RandomAccessFile accessFile = new RandomAccessFile("F:\\Java\\Java-project\\NIOAndJUC\\NIO\\file\\fileChannel.txt", "rw");
        FileChannel channel = accessFile.getChannel();

        // 写操作
        // 创建buffer对象
        ByteBuffer buffer = ByteBuffer.allocate(1024);

        // 写入的内容
        String newData = "this is a test write demo！";

        // 清空buffer
        buffer.clear();

        // 写入内容
        buffer.put(newData.getBytes());

        buffer.flip();

        // FileChannel完成最终的实现
        while (buffer.hasRemaining()) {
            channel.write(buffer);
        }

        // 关闭channel
        channel.close();
    }

}
```

注意FileChannel.write()是在while 循环中调用的。因为无法保证write()方法一次能 向 FileChannel 写入多少字节，因此需要重复调用write()方法，直到Buffer 中已经没
有尚未写入通道的字节。

#### FileChannel的position方法

有时可能需要在 FileChannel 的某个特定位置进行数据的读/写操作。可以通过调用 position()方法获取 FileChannel 的当前位置。也可以通过调用position(long pos)方法设置 FileChannel 的当前位置。

如果将位置设置在文件结束符之后，然后试图从文件通道中读取数据，读方法将返回1 （文件结束标志）。
如果将位置设置在文件结束符之后，然后向通道中写数据，文件将撑大到当前位置并
写入数据。这可能导致“文件空洞”，磁盘上物理文件中写入的数据间有空隙。

#### FileChannel的size方法

FileChannel 实例的 size()方法将返回该实例所关联文件的大小。如:

` long fileSize = channel.size();`

#### FileChannel的truncate方法

可以使用 FileChannel.truncate()方法截取一个文件。截取文件时，文件将中指定长度 后面的部分将被删除。如： 

`channel.truncate(1024);`

这个例子截取文件的前 1024个字节。

#### FileChannel的force方法

FileChannel.force()方法将通道里尚未写入磁盘的数据强制写到磁盘上。出于性能方 面的考虑，操作系统会将数据缓存在内存中，所以无法保证写入到FileChannel 里的 数据一定会即时写到磁盘上。要保证这一点，需要调用 force()方法。 force()方法有一个boolean类型的参数，指明是否同时将文件元数据（权限信息等）
写到磁盘上。

#### FileChannel的transferTo和 transferFrom方法

**通道之间的数据传输**： 如果两个通道中有一个是FileChannel，那你可以直接将数据从一个 channel 传输到 另外一个 channel。

> （1）transferFrom()方法 FileChannel 的 transferFrom()方法可以将数据从源通道传输到FileChannel 中（译 者注：这个方法在 JDK文档中的解释为将字节从给定的可读取字节通道传输到此通道
> 的文件中）。下面是一个FileChannel 完成文件间的复制的例子：
>
> 
>
> 方法的输入参数position表示从 position处开始向目标文件写入数据，count 表示最 多传输的字节数。如果源通道的剩余空间小于 count 个字节，则所传输的字节数要小 于请求的字节数。此外要注意，在 SoketChannel 的实现中，SocketChannel 只会传 输此刻准备好的数据（可能不足 count 字节）。因此，SocketChannel 可能不会将请
> 求的所有数据(count 个字节)全部传输到FileChannel 中。

> （2）transferTo()方法
>
> transferTo()方法将数据从 FileChannel 传输到其他的 channel 中。

### 



## java NIO（Buffer）



## java NIO（Selector）



## java NIO（Pipe和FileLock）



## java NIO（其他）