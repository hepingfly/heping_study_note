# NIO笔记

### 一、Java NIO 简介

Java NIO（New IO）是从 Java 1.4版本开始引入的一个新的 IO API，可以替代标准的 Java IO API。NIO 与原来的 IO 有同样的作用和目的，但是使用方式完全不同，NIO 支持面向缓冲区的、基于通道的 IO 操作。NIO 将以更加高效的方式进行文件的读写操作。

### 二、NIO 与 IO 的区别

#### 1、区别

| IO                        | NIO                           |
| ------------------------- | ----------------------------- |
| 面向流（Stream Oriented） | 面向缓冲区（Buffer Oriented） |
| 阻塞IO（Blocking IO）     | 非阻塞IO（Non Blocking IO）   |
|                           | 选择器（Selectors）           |

#### 2、通道和缓冲区

Java NIO 系统的核心在于：==通道==（Channel）和==缓冲区==（Buffer）。通道表示打开到 IO 设备（例如：文件、套接字）的连接。若需要使用 NIO 系统，需要获取用于连接 IO 设备的通道以及用于容纳数据的缓冲区。然后操作缓冲区，对数据进行处理。==简而言之，Channel 负责传输，Buffer 负责存储==

#### 3、面向流和面向缓冲区解释

- **传统 IO 流**

![传统IO流](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-nio/传统IO流.png)

> - 我们需要把磁盘文件或者网络文件中的数据读取到程序中来，我们需要建立一个用于传输数据的管道，原来我们传输数据面对的直接就是管道里面一个个字节数据的流动（我们弄了一个 byte 数组，来回进行数据传递），所以说原来的 IO 它面对的就是管道里面的一个数据流动，所以我们说原来的 IO 是面向流的 
> - 我们说传统的 IO 还有一个特点就是，==它是单向的==。解释一下就是：如果说我们想把目标地点的数据读取到程序中来，我们需要建立一个管道，这个管道我们称为输入流。相应的，如果如果我们程序中有数据想要写到目标地点去，我们也得再建立一个管道，这个管道我们称为输出流。所以我们说传统的 IO 流是单向的。

- **NIO**

 ![缓冲区](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-nio/%E7%BC%93%E5%86%B2%E5%8C%BA.png)

> - 我们说只要是 IO ，那么就是为了完成数据传输的。
> - 即便你用 NIO ，它也是为了数据传输，所以你要想完成数据传输，你也得建立一个用于传输数据的通道，这个通道你不能把它理解为之前的水流了，但是你可以把它理解为铁路，铁路本身是不能完成运输的，铁路要想完成运输它必须依赖火车，说白了这个通道就是为了连接目标地点和源地点。所以注意==通道本身不能传输数据，要想传输数据必须要有缓冲区==，这个缓冲区你就可以完全把它理解为火车，比如说你现在想把程序中的数据写到文件中，那么你就可以把数据都写到缓冲区，然后缓冲区通过通道进行传输，最后再把数据从缓冲区拿出来写到文件中，你想把文件中的数据传数到程序中，也是一个道理，把数据写到缓冲区，缓冲区通过通道进行传输，到程序中把数据拿出来。所以我们说原来的 IO 单向的现在的缓冲区是双向的，这种传输数据的方式也叫面向缓冲区。总结一下，就是通道只负责连接，缓冲区才负责存储数据。

#### 4、缓冲区的数据存取

缓冲区（Buffer）：一个用于特定基本数 据类型的容器。由 java.nio 包定义的，所有缓冲区都是 Buffer 抽象类的子类。

![缓冲区数据的存取](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-nio/%E7%BC%93%E5%86%B2%E5%8C%BA%E6%95%B0%E6%8D%AE%E7%9A%84%E5%AD%98%E5%8F%96.png)

```java
/**
 * 一、缓冲区（Buffer）：在 Java NIO 中负责数据的存取。缓冲区就是数组。用于存储不同类型的数据
 * 根据数据类型的不同（boolean 除外），提供了相应类型的缓冲区：
 * ByteBuffer
 * CharBuffer
 * ShortBuffer
 * IntBuffer
 * LongBuffer
 * FloatBuffer
 * DoubleBuffer
 *
 * 上述缓冲区管理方式几乎一致，都是通过 allocate() 来获取缓冲区
 *
 * 二、缓冲区存取数据的两个核心方法：
 * put():存入数据到缓冲区中
 * get():获取缓冲区中的数据
 *
 * 三、缓冲区中的四个核心属性
 * capacity: 容量，表示缓冲区中最大存储数据的容量。一旦声明不能更改
 * limit: 界限，表示缓冲区中可以操作数据的大小。（limit 后的数据不能进行读写）
 * position: 位置，表示缓冲区中正在操作数据的位置
 * mark: 标记，表示记录当前 position 的位置。可以通过 reset() 恢复到 mark 的位置
 *
 * 0 <= mark <= position <= limit <= capacity
 */
public class TestBuffer {

    public static void test2() {
        String str = "abcde";
        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
        byteBuffer.put(str.getBytes());
        byteBuffer.flip();
        byte[] bytearray = new byte[byteBuffer.limit()];
        byteBuffer.get(bytearray,0,2);
        System.out.println(new String(bytearray,0,2));  //结果是 ab
        System.out.println(byteBuffer.position());   //结果是 2
        //标记一下当前 position 的位置
        byteBuffer.mark();
        byteBuffer.get(bytearray,2,2);
        System.out.println(new String(bytearray,2,2));
        System.out.println(byteBuffer.position());   //结果是 4
        //reset() 恢复到 mark 的位置
        byteBuffer.reset();
        System.out.println(byteBuffer.position());   //结果是 2

        //判断缓冲区中是否还有剩余数据
        if (byteBuffer.hasRemaining()) {
            //获取缓冲区中可以操作的数量
            System.out.println(byteBuffer.remaining());  //结果是 3,上面 position 是从 2 开始的
        }
    }

    public static void test1() {
        String str = "abcde";

        //分配一个指定大小的缓冲区
        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
        System.out.println("---------allocate-----------");
        System.out.println(byteBuffer.capacity());   //1024
        System.out.println(byteBuffer.limit());      //1024
        System.out.println(byteBuffer.position());   //0

        //利用 put() 存入数据到缓冲区中
        byteBuffer.put(str.getBytes());
        System.out.println("---------put-----------");
        System.out.println(byteBuffer.capacity());   //1024
        System.out.println(byteBuffer.limit());      //1024
        System.out.println(byteBuffer.position());   //5

        //切换到读数据模式
        byteBuffer.flip();
        System.out.println("---------flip-----------");
        System.out.println(byteBuffer.capacity());   //1024
        System.out.println(byteBuffer.limit());      //5,limit 表示可以操作数据的大小,只有 5 个字节的数据给你读,所以可操作数据大小是 5
        System.out.println(byteBuffer.position());   //0,读数据要从第 0 个位置开始读

        //利用 get() 读取缓冲区中的数据
        byte[] dst = new byte[byteBuffer.limit()];
        byteBuffer.get(dst);
        System.out.println(new String(dst,0,dst.length));
        System.out.println("---------get-----------");
        System.out.println(byteBuffer.capacity());   //1024
        System.out.println(byteBuffer.limit());      //5,可以读取数据的大小依然是 5 个
        System.out.println(byteBuffer.position());   //5,读完之后位置变到了第 5 个

        //rewind() 可重复读
        byteBuffer.rewind();         //这个方法调用完后,又变成了读模式
        System.out.println("---------rewind-----------");
        System.out.println(byteBuffer.capacity());   //1024
        System.out.println(byteBuffer.limit());      //5
        System.out.println(byteBuffer.position());  //0

        //clear() 清空缓冲区,虽然缓冲区被清空了，但是缓冲区中的数据依然存在，只是出于"被遗忘"状态。意思其实是，缓冲区中的界限、位置等信息都被置为最初的状态了，所以你无法再根据这些信息找到原来的数据了，原来数据就出于"被遗忘"状态
        byteBuffer.clear();
        System.out.println("---------clear-----------");
        System.out.println(byteBuffer.capacity());   //1024
        System.out.println(byteBuffer.limit());      //1024
        System.out.println(byteBuffer.position());  //0
    }

    public static void main(String[] args) {
//        test1();
        test2();

    }
}
```

#### 5、直接缓冲区与非直接缓冲区

1）、非直接缓冲区

通过 `allocate()` 方法分配缓冲区，将缓冲区建立在 JVM 的内存之中。

![非直接缓冲区](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-nio/%E9%9D%9E%E7%9B%B4%E6%8E%A5%E7%BC%93%E5%86%B2%E5%8C%BA.png)

> 应用程序和磁盘之间想要传输数据，是没有办法直接进行传输的。操作系统出于安全的考虑，会经过上图几个步骤。例如，我应用程序想从磁盘中读取一个数据，这时候我应用程序向操作系统发起一个读请求，那么首先磁盘中的数据会被读取到内核地址空间中，然后会把内核地址空间中的数据拷贝到用户地址空间中（其实就是 JVM 内存中），最后再把这个数据读取到应用程序中来。
>
> 同样，如果我应用程序有数据想要写到磁盘中去，那么它会首先把这个数据写入到用户地址空间中去，然后把数据拷贝到内核地址空间，最后再把这个数据写入到磁盘中去。



2）、直接缓冲区

通过 `allocateDirect()` 方法分配缓冲区，将缓冲区建立在物理内存之中。

![直接缓冲区](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-nio/%E7%9B%B4%E6%8E%A5%E7%BC%93%E5%86%B2%E5%8C%BA.png)

```java
public static void test3() {
    // 分配直接缓冲区
    ByteBuffer byteBuffer = ByteBuffer.allocateDirect(1024);
    // 判断是直接缓冲区还是非直接缓冲区
    System.out.println(byteBuffer.isDirect());
}
```



**注：**

字节缓冲区是直接缓冲区还是非直接缓冲区可通过调用其 `isDirect()` 方法来确定。

#### 6、通道

1）、介绍

> - 通道（channel）：由 java.nio.channels 包定义的。Channel 表示 IO 源与目标打开的连接。Channel 类似于传统的流，只不过 Channel 本身不能直接访问数据，Channel 只能与 Buffer 进行交互。
> - 通道用于源节点与目标节点的连接。在 Java NIO 中负责缓冲区中数据的传输。Channel 本身不存储数据，因此需要配合缓冲区进行传输。

2）、主要实现类

> `java.nio.channels.Channel`
>
> - FileChannel
> - SocketChannel
> - ServerSocketChannel
> - DatagramChannel

3）、获取通道

```
1、Java 针对支持通道的类提供了 getChannel() 方法
	本地IO：
	FileInputStream/FileOutputStream
	RandomAccessFile
	网络IO:
	Socket
	ServerSocket
	DatagramSocket
以上几个类都可以通过调用 getChannel() 方法获取通道

2、在 JDK1.7 中的 NIO.2 针对各个通道提供了静态方法 open()
3、在 JDK1.7 中的 NIO.2 的 Files 工具类的 newByteChannel() 方法
```

#### 7、通道数据传输和内存映射文件

1）、使用通道完成文件的复制（非直接缓冲区）

```java
public static void test1() throws Exception {
        // 利用通道完成文件的复制(非直接缓冲区)
        FileInputStream fis = new FileInputStream("a.txt");
        FileOutputStream fos = new FileOutputStream("b.txt");
        // 获取通道
        FileChannel fisChannel = fis.getChannel();
        FileChannel foschannel = fos.getChannel();

        // 通道没有办法传输数据，必须依赖缓冲区
        // 分配指定大小的缓冲区
        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);

        // 将通道中的数据存入缓冲区中
        while (fisChannel.read(byteBuffer) != -1) {  // fisChannel 中的数据读到 byteBuffer 缓冲区中
            byteBuffer.flip();  // 切换成读数据模式
            // 将缓冲区中的数据写入通道
            foschannel.write(byteBuffer);
            byteBuffer.clear();  // 清空缓冲区
        }
        foschannel.close();
        fisChannel.close();
        fos.close();
        fis.close();
    }
```

2）、使用直接缓冲区完成文件的复制（内存映射文件）

方式一：

```java
public static void test2() throws Exception {
    // 使用直接缓冲区完成文件的复制(内存映射文件)
    /**
         * 使用 open 方法来获取通道
         * 需要两个参数
         * 参数1：Path 是 JDK1.7 以后给我们提供的一个类，代表文件路径
         * 参数2：Option  就是针对这个文件想要做什么样的操作
         *      --StandardOpenOption.READ ：读模式
         *      --StandardOpenOption.WRITE ：写模式
         *      --StandardOpenOption.CREATE ：如果文件不存在就创建，存在就覆盖
         */
    FileChannel inChannel = FileChannel.open(Paths.get("a.txt"), StandardOpenOption.READ);
    FileChannel outChannel = FileChannel.open(Paths.get("c.txt"), StandardOpenOption.WRITE,
                                              StandardOpenOption.READ, StandardOpenOption.CREATE);

    /**
         * 内存映射文件
         * 这种方式缓冲区是直接建立在物理内存之上的
         * 所以我们就不需要通道了
         */
    MappedByteBuffer inMapped = inChannel.map(FileChannel.MapMode.READ_ONLY, 0, inChannel.size());
    MappedByteBuffer outMapped = outChannel.map(FileChannel.MapMode.READ_WRITE, 0, inChannel.size());

    // 直接对缓冲区进行数据的读写操作
    byte[] dst = new byte[inMapped.limit()];
    inMapped.get(dst);  // 把数据读取到 dst 这个字节数组中去
    outMapped.put(dst); // 把字节数组中的数据写出去

    inChannel.close();
    outChannel.close();
}
```

方式二：

```java
public static void test3() throws Exception {
    /**
         * 通道之间的数据传输（直接缓冲区的方式）
         * transferFrom
         * transferTo
         */
    FileChannel inChannel = FileChannel.open(Paths.get("a.txt"), StandardOpenOption.READ);
    FileChannel outChannel = FileChannel.open(Paths.get("d.txt"), StandardOpenOption.READ, StandardOpenOption.WRITE,
                                              StandardOpenOption.CREATE);
    inChannel.transferTo(0, inChannel.size(), outChannel);
    // 或者可以使用下面这种方式
    //outChannel.transferFrom(inChannel, 0, inChannel.size());
    inChannel.close();
    outChannel.close();
}
```

#### 8、分散读取与聚集写入

分散（Scatter）和聚集（Gather）

- 分散读取（Scattering Reads）是指从 Channel 中读取的数据 "分散" 到多个 Buffer 中

![分散读取](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-nio/%E5%88%86%E6%95%A3%E8%AF%BB%E5%8F%96.png)

- 聚集写入（Gathering Writes）是指将多个 Buffer 中的数据 "聚集" 到 Channel

![聚集写入](https://shp-notes-1257820375.cos.ap-chengdu.myqcloud.com/shp-nio/%E8%81%9A%E9%9B%86%E5%86%99%E5%85%A5.png)

```java
public static void test4() throws Exception {
    RandomAccessFile raf = new RandomAccessFile("a.txt", "rw");
    // 获取通道
    FileChannel channel = raf.getChannel();
    // 分配指定大小缓冲区
    ByteBuffer buf1 = ByteBuffer.allocate(2);
    ByteBuffer buf2 = ByteBuffer.allocate(1024);
    // 分散读取
    ByteBuffer[] bufs = {buf1, buf2};
    channel.read(bufs);  // 参数需要一个数组
    for (ByteBuffer byteBuffer : bufs) {
        byteBuffer.flip();  // 切换到读模式
    }
    System.out.println(new String(bufs[0].array(), 0, bufs[0].limit()));  // 打印 he
    System.out.println(new String(bufs[1].array(), 0, bufs[1].limit()));  // 打印 llo

    // 聚集写入
    RandomAccessFile raf2 = new RandomAccessFile("e.txt","rw");
    // 获取通道
    FileChannel channel2 = raf2.getChannel();
    channel2.write(bufs);  // 把 bufs 里面的几个缓冲区聚集到 channel2 这个通道中，聚集到通道中，也就是到了 e.txt 文件中
    channel2.close();
}
```

#### 9、字符集

编码：把字符串转换成字节数组的过程就是编码

解码：把字节数组转换成字符串的过程就是解码

1）、查看 nio 包下的 CharSet 类支持哪些字符集

```java
public static void test5() {
    // Java nio 包下提供了一个字符集 Charset 类
    SortedMap<String, Charset> charsets = Charset.availableCharsets();
    // 查看 CharSet 支持的字符集种类数
    for (Map.Entry<String, Charset> entry: charsets.entrySet()) {
        System.out.println(entry.getKey() + "-->" + entry.getValue());
    }
}
```

2）、字符编码使用案例

```java
public static void test6() throws CharacterCodingException {
    // 指定字符集编码
    Charset charset = Charset.forName("GBK");
    // 获取编码器
    CharsetEncoder encoder = charset.newEncoder();
    // 获取解码器
    CharsetDecoder decoder = charset.newDecoder();
    // 字符缓冲区
    CharBuffer charBuffer = CharBuffer.allocate(1024);
    charBuffer.put("视频号 hepingfly");
    charBuffer.flip();
    // 字符缓冲区编码成字节缓冲区(编码)
    ByteBuffer byteBuffer = encoder.encode(charBuffer);
    // 字节缓冲区解码成字符缓冲区(解码)
    CharBuffer cbuffer = decoder.decode(byteBuffer);
    System.out.println(cbuffer.toString());  // 打印 视频号 hepingfly
    System.out.println("***************");
    // 上面是用 GBK 去编码的，下面尝试使用 UTF8 去解码
    Charset utf8 = Charset.forName("UTF-8");
    byteBuffer.flip();
    CharBuffer utf8Buffer = utf8.decode(byteBuffer);
    System.out.println(utf8Buffer.toString());  // 发现打印乱码 ��Ƶ�� hepingfly
}
```



### 三、NIO 的非阻塞式网络通信



























