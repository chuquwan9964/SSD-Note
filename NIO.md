# NIO

|   IO   |    NIO     |
| :----: | :--------: |
| 面向流 | 面向缓冲区 |
| 阻塞IO |  非阻塞IO  |
|   无   |   选择器   |



#### ByteBuffer

##### position

##### limit

##### capacity

##### put()

##### flip()	

翻转

**flip会将limit值设置为position，position设置为0**

##### rewind()

倒带

##### clear()



```
三个重要属性	
	position	表示缓冲区中正在操作数据的位置
	limit		表示缓冲区中可以操作数据的大小(limit后面的数据不允许操作)
	capacity	表示缓冲区的最大存储数据的容量
	
	position<=limit<=capacity
	
获取ByteBuffer
	
	ByteBuffer byteBuffer = ByteBuffer.allocate(1024);//1024表示最大容量
	这个时候position为0，表示当前读写指针指在了0位置
	limit为1024，表示1024个字节可用
	capacity为1024，表示最大容量为1024
	
写数据

	byteBuffer.put("abcde".getBytes());//写入数据
	这个时候position为5
	limit还是1024
	capacity是1024
	
读数据

	读数据的规则是从当前的position往后读，因此要调用方法调整position的位置
	byteBuffer.flip()	将position置为0，将limit置为position，capacity不变
	byte[] bytes = new byte[1024];
	byteBuffer.get(bytes);	//将数据读入bytes数组中，如果数组长度大于limit-position，将会抛出异常
	
重复读取数据
	
	byteBuffer.rewind()	将position置为0，其他都不变
	
清空缓冲区(遗忘数据，但是数据还存在，还是可以读出来的)

	byteBuffer.clear()	将position置为0，limit置为最大容量
	虽然是clear，但是数据并没有被清除，可以直接读出来
	
```

##### mark()

​	做一个标记

##### reset()

​	返回上次标记的状态使position，limit，capacity重置为上一次标记时的值

```
package com.chuquwan.nio;

import java.nio.ByteBuffer;

public class Test2 {

    public static void main(String[] args) {
        ByteBuffer buffer = ByteBuffer.allocate(10);
        buffer.put("abcd".getBytes());

        System.out.println(buffer); //4 10 10

        buffer.flip();
        System.out.println(buffer); //0 4 10

        byte[] bytes = new byte[buffer.limit()];
        byte[] bytes2 = new byte[buffer.limit()];

        buffer.get(bytes, 0, 2);//表示将0,1读取到bytes字节数组中
        System.out.println(buffer); //2 4 10
        System.out.println(new String(bytes, 0, 2));
        buffer.mark();

        buffer.get(bytes, 2, 2);
        System.out.println(buffer); //4 4 10
        System.out.println(new String(bytes, 2, 2));

        buffer.reset();
        System.out.println(buffer); //2 4 10
        buffer.get(bytes2, 0, 2);
        System.out.println(new String(bytes2, 0, 2));
    }
}

```

##### asReadOnlyBuffer()

​	将缓冲区转换为只读

```
ByteBuffer readOnlyBuffer = buffer.asReadOnlyBuffer();
```





### 直接缓冲区与间接缓冲区

##### 直接缓冲区

使用直接缓冲区拷贝文件，速度快，不安全，直接操作在物理内存上，同过创建物理内存映射文件的方式

因为物理内存一刷新正在执行输入输出的程序都会over

```
ByteBuffer.allocateDirect()	//可以创建直接缓冲区
isDirect()	是否是直接缓冲区
```

channel:通道的意思

```
        long begin = System.currentTimeMillis();
        //获取文件输入通道
        FileChannel inChannel = FileChannel.open(Paths.get("F:\\1.mp4"), StandardOpenOption.READ);
        //获取文件输出通道
        FileChannel outChannel = FileChannel.open(Paths.get("F:\\2.mp4"), StandardOpenOption.READ, StandardOpenOption.WRITE, StandardOpenOption.CREATE);
        //物理内存映射文件
        MappedByteBuffer inMapperBuff = inChannel.map(FileChannel.MapMode.READ_ONLY, 0, inChannel.size());
        //物理内存映射文件
        MappedByteBuffer outMapperBuff = outChannel.map(FileChannel.MapMode.READ_WRITE, 0, inChannel.size());
        System.out.println(outMapperBuff);  //java.nio.DirectByteBufferR[pos=0 lim=155750044 cap=155750044]
        byte[] bytes = new byte[inMapperBuff.limit()];

        inMapperBuff.get(bytes);	//将数据读到bytes数组中
        outMapperBuff.put(bytes);	//将bytes数组中的数据写入到outMapperBuff这个物理内存映射文件中

        long end = System.currentTimeMillis();
        System.out.println(end - begin);
        inChannel.close();
        outChannel.close();
```

##### 利用transferTo方法copy文件

也是属于直接缓冲区

这个好像比内存映射文件更快 

```
        long begin = System.currentTimeMillis();
        FileChannel in = FileChannel.open(Paths.get("f:/1.mp4"), StandardOpenOption.READ);
        FileChannel out = FileChannel.open(Paths.get("f:/2.mp4"), StandardOpenOption.READ, StandardOpenOption.WRITE, StandardOpenOption.CREATE);
        long l = in.transferTo(0, in.size(), out);
        System.out.println(l);
        long end = System.currentTimeMillis();
        System.out.println("话费的时间为:" + (end - begin));
```



##### 间接缓冲区

非直接缓冲区：通过 allocate() 方法分配缓冲区，将缓冲区建立在 JVM 的内存中

```
        long begin = System.currentTimeMillis();
        FileInputStream fileInputStream = new FileInputStream("f://1.mp4");
        FileOutputStream fileOutputStream = new FileOutputStream("f://2.mp4");
        //获取文件输入通道
        FileChannel inChannel = fileInputStream.getChannel();
        //获取文件输出通道
        FileChannel outChannel = fileOutputStream.getChannel();

        ByteBuffer allocate = ByteBuffer.allocate((int) inChannel.size());
        System.out.println(allocate);
        while (inChannel.read(allocate) != -1) {	//inChannel读取文件的数据写到缓冲区里
            System.out.println(allocate);   //三个是一样的
            allocate.flip();	//因为接下来要读取缓冲区的数据写到outChannel里，所以调用flip方法准备读数据
            outChannel.write(allocate); 
            allocate.clear();
        }
        long end = System.currentTimeMillis();
        System.out.println(end - begin);
        outChannel.close();
        inChannel.close();
        fileOutputStream.close();
        fileInputStream.close();
```

#### 分散读取与聚集写入

```
		RandomAccessFile raf1 = new RandomAccessFile("test.txt", "rw");
		// 1.获取通道
		FileChannel channel = raf1.getChannel();
		// 2.分配指定大小的指定缓冲区
		ByteBuffer buf1 = ByteBuffer.allocate(100);
		ByteBuffer buf2 = ByteBuffer.allocate(1024);
		// 3.分散读取
		ByteBuffer[] bufs = { buf1, buf2 };
		channel.read(bufs);
		for (ByteBuffer byteBuffer : bufs) {
			// 切换为读取模式
			byteBuffer.flip();
		}
		System.out.println(new String(bufs[0].array(), 0, bufs[0].limit()));
		System.out.println("------------------分算读取线分割--------------------");
		System.out.println(new String(bufs[1].array(), 0, bufs[1].limit()));
		// 聚集写入
		RandomAccessFile raf2 = new RandomAccessFile("2.txt", "rw");
		FileChannel channel2 = raf2.getChannel();
		channel2.write(bufs);

```

#### 字符集Charset

```
        Charset gbk = Charset.forName("GBK");
        //编码器,将字符串转为字节数组
        CharsetEncoder charsetEncoder = gbk.newEncoder();
        //解码器,将字节数组转为字符串
        CharsetDecoder charsetDecoder = gbk.newDecoder();

        CharBuffer charBuffer = CharBuffer.allocate(100);
        //向字符缓冲buffer里写入数据,然后将其编码
        charBuffer.put("hello world");
        charBuffer.flip();
        ByteBuffer byteBuffer = charsetEncoder.encode(charBuffer);

        byte[] array = byteBuffer.array();
        System.out.println(Arrays.toString(array));
        
        //charsetDecoder也可以将byteBuffer转换为charBuffer这里不做演示
```

#### 阻塞与非阻塞

##### 	什么是阻塞

```
阻塞概念:应用程序在获取网络数据的时候,如果网络传输很慢,那么程序就一直等着,直接到传输完毕。
```

##### 阻塞代码

```
package com.chuquwan.niotest;

import org.junit.Test;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.net.ServerSocket;
import java.nio.ByteBuffer;
import java.nio.channels.FileChannel;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.nio.file.Paths;
import java.nio.file.StandardOpenOption;

public class Test2 {

    @Test
    public void client() throws IOException {
        SocketChannel sChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 8080));
        FileChannel fChannel = FileChannel.open(Paths.get("f:/1.mp4"), StandardOpenOption.READ);

        ByteBuffer buffer = ByteBuffer.allocate(1024);

        while (fChannel.read(buffer) != -1) {
            buffer.flip();
            sChannel.write(buffer);
            buffer.clear();
        }
        sChannel.shutdownOutput();//客户端关闭套接字的输出流,提醒服务端不要阻塞在read方法

        int len = 0;
        while ((len = sChannel.read(buffer)) != -1) {
            System.out.println("收到服务器响应");
            System.out.println(buffer);
            byte[] array = buffer.array();
            System.out.println("服务器响应的内容:" + new String(array, 0, len));
        }

        fChannel.close();
        sChannel.close();
    }

    @Test
    public void server() throws IOException {
        ServerSocketChannel ssChannel = ServerSocketChannel.open();
        ssChannel.bind(new InetSocketAddress(8080));
        FileChannel fChannel = FileChannel.open(Paths.get("f:/2.mp4"), StandardOpenOption.WRITE, StandardOpenOption.CREATE);

        ByteBuffer buffer = ByteBuffer.allocate(1024);

        SocketChannel sChannel = ssChannel.accept();
        System.out.println("接收到客户端请求");
        while (sChannel.read(buffer) != -1) {	//如果客户端不调用shutdown方法的话，会一直阻塞在这里
            buffer.flip();
            fChannel.write(buffer);
            buffer.clear();
        }

        buffer.put("客户端你好".getBytes());
        buffer.flip();
        sChannel.write(buffer);
        System.out.println("给客户端发送响应完毕");

        sChannel.close();
        fChannel.close();
        ssChannel.close();

    }

}

```



##### 	什么是非阻塞

```
应用程序直接可以获取已经准备好的数据,无需等待.
IO为同步阻塞形式,NIO为同步非阻塞形式。NIO没有实现异步,在JDK1.7之后，升级了NIO库包
,支持异步费阻塞通讯模型NIO2.0(AIO)
```

```
package com.chuquwan.niotest;

import org.junit.Test;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.ArrayList;
import java.util.Iterator;
import java.util.List;

public class Test3 {


    @Test
    public void client() throws IOException {
        SocketChannel sChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 8080));
        //设置为非阻塞
        sChannel.configureBlocking(false);
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        buffer.put("你好啊服务器".getBytes());
        buffer.flip();
        sChannel.write(buffer);

        buffer.clear();

        sChannel.close();
    }

    @Test
    public void server() throws IOException {
        ServerSocketChannel ssChannel = ServerSocketChannel.open();
        ssChannel.bind(new InetSocketAddress(8080));
        ssChannel.configureBlocking(false);

        Selector selector = Selector.open();
        //第二个参数是通道监听的事件,当事件发生时,通道激活
        ssChannel.register(selector, SelectionKey.OP_ACCEPT);

        //轮训,知道选择器中有通道被事件激活时
        int selectNum = 0;
        while ((selectNum = selector.select()) > 0) {
            Iterator<SelectionKey> it = selector.selectedKeys().iterator();
            //依次取出it进行操作
            while (it.hasNext()) {
                SelectionKey sk = it.next();
                System.out.println(sk);
                //当sk是可接受,表示我们注册的服务端的接受事件已激活,但是客户端可能还尚未写完数据
                if (sk.isAcceptable()) {
//                    ServerSocketChannel ssChannel2 = (ServerSocketChannel) sk.channel();
                    //应该是相等的
//                    System.out.println(ssChannel == ssChannel2);  //true
                    SocketChannel sChannel = ssChannel.accept();
                    sChannel.configureBlocking(false);
                    //将客户端通道注册为读事件,客户端写完数据之后激活此事件
                    sChannel.register(selector, SelectionKey.OP_READ);
                } else if (sk.isReadable()) {
                    //客户端已写完,可读了
                    //获取客户端的通道
                    SocketChannel socketChannel = (SocketChannel) sk.channel();
                    ByteBuffer buffer = ByteBuffer.allocate(1024);
                    int len = 0;
                    while ((len = socketChannel.read(buffer)) > 0) {
                        buffer.flip();
                        System.out.println(new String(buffer.array(), 0, len));
                        buffer.clear();
                    }
                }
                //从迭代器中移出此SelectionKey
                it.remove();
            }
        }
    }

}

```





### 通道(Channel)

实现类主要:

##### 	FileChannel

##### 	SocketChannel

##### 	ServerSocketChannel

##### 	DatagramChannel

#### 获取通道方式

##### 	1.支持通道的类提供了getChannel方法

```
FileInputStream/FileOutputStrean
Socket
ServerSocket
DatagramSocket
```

##### 	2.JDK1.7	NIO2以后的静态方法open()

```
FileChannel.open()
SocketChannel.open()
ServerSocketChannel.open()
DatagramChannel.open()
```

##### 3.Files工具类的newByteChannel方法



### Selector

#### 	API

​		Selector open()	获取selector对象

​		int select()	阻塞，直到有事件发生

​		int select(long timeout)	阻塞一段时间

​		int selectNow()	如果没有，立刻返回

​		Set<SelectionKey> selectedKeys()	返回当前的SelectionKey集合









#### NIO非阻塞客户端服务器双向通讯

```
package com.chuquwan.niotest;

import org.junit.Test;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.*;
import java.util.Scanner;
import java.util.Set;

public class Test6 {
//客户端
    public static void main(String[] args) throws IOException {
        ByteBuffer sBuff = ByteBuffer.allocate(1024);
        ByteBuffer rBuff = ByteBuffer.allocate(1024);
        //不着急链接服务器
        SocketChannel sChannel = SocketChannel.open();
        sChannel.configureBlocking(false);

        Selector selector = Selector.open();

        sChannel.register(selector, SelectionKey.OP_CONNECT);
        //最好在注册之后连接服务器
        sChannel.connect(new InetSocketAddress("127.0.0.1", 8080));

        while (true) {
            selector.select();
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            for (SelectionKey sk : selectionKeys) {
                if (sk.isConnectable()) {
                    SocketChannel socketChannel = (SocketChannel) sk.channel();
                    //这俩方法不知道是啥意思，但是不加又不行
                    if (socketChannel.isConnectionPending()) {
                        socketChannel.finishConnect();
                        sBuff.clear();
                        sBuff.put("你好服务器".getBytes());
                        sBuff.flip();
                        socketChannel.write(sBuff);
                        new Thread() {
                            @Override
                            public void run() {
                                while (true) {
                                    try {
                                        sBuff.clear();
                                        InputStreamReader input = new InputStreamReader(System.in);
                                        BufferedReader br = new BufferedReader(input);
                                        String sendText = br.readLine();
                                        if (sendText.equals("exit"))
                                            break;
                                        sBuff.put(sendText.trim().getBytes("UTF-8"));
                                        sBuff.flip();
                                        socketChannel.write(sBuff);
                                    } catch (IOException e) {
                                        e.printStackTrace();
                                        break;
                                    }
                                }
                            }
                        }.start();
                    }
                    socketChannel.register(selector, SelectionKey.OP_READ);
                } else if (sk.isReadable()) {
                    SocketChannel socketChannel = (SocketChannel) sk.channel();
                    int read = socketChannel.read(rBuff);
                    rBuff.flip();
                    System.out.println(new String(rBuff.array(), 0, read));
                    rBuff.clear();
                    socketChannel.register(selector, SelectionKey.OP_READ);
                }
            }
            selectionKeys.clear();
        }

    }

 //服务器
    @Test
    public void Server() throws IOException {
        ByteBuffer sBuff = ByteBuffer.allocate(1024);
        ByteBuffer rBuff = ByteBuffer.allocate(1024);
        ServerSocketChannel ssChannel = ServerSocketChannel.open();
        ssChannel.configureBlocking(false);
        ssChannel.bind(new InetSocketAddress(8080));

        Selector selector = Selector.open();

        ssChannel.register(selector, SelectionKey.OP_ACCEPT);

        while (true) {
            try {
                selector.select();
                Set<SelectionKey> selectionKeys = selector.selectedKeys();
                for (SelectionKey sk : selectionKeys) {
                    if (sk.isAcceptable()) {
                        System.out.println("可接受");
                        SocketChannel sChannel = ssChannel.accept();
                        sChannel.configureBlocking(false);
                        sChannel.register(selector, SelectionKey.OP_READ | SelectionKey.OP_WRITE);
                    } else if (sk.isReadable()) {
                        System.out.println("可读");
                        SocketChannel socketChannel = (SocketChannel) sk.channel();
                        int len = 0;
                        try {
                            while ((len = socketChannel.read(rBuff)) > 0) {
                                System.out.println(len);
                                rBuff.flip();
                                System.out.println(new String(rBuff.array(), 0, len));
                                rBuff.clear();

                                sBuff.put("客户端你好".getBytes());
                                sBuff.flip();
                                socketChannel.write(sBuff);
                                sBuff.clear();
                            }
                            if (len == -1) {
                                sk.cancel();
                                socketChannel.close();
                            }
                        } catch (IOException e) {
                            sk.cancel();
                            socketChannel.close();
                        }
                    }
                }
                selectionKeys.clear();
            } catch (IOException e) {
                e.printStackTrace();
                continue;
            }
        }
    }
}
```



#### 简易聊天室

##### Server

```
package com.chuquwan.groupchat;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.net.Socket;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.ServerSocketChannel;
import java.nio.channels.SocketChannel;
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

/**
 * 群聊服务端
 * 监听9999端口
 */
public class Server {
    private static Map<SocketChannel, String> onlineUsers = new HashMap<>();

    private static final int PORT = 9999;

    private static ServerSocketChannel ssChannel;

    private static Selector selector;

    public Server() throws IOException {
        ssChannel = ServerSocketChannel.open();
        //设置为非阻塞
        ssChannel.configureBlocking(false);
        //绑定端口号
        ssChannel.bind(new InetSocketAddress(PORT));
        //获取选择器
        selector = Selector.open();
        //注册入选择器,注册接受连接事件
        ssChannel.register(selector, SelectionKey.OP_ACCEPT);
        System.out.println("服务器启动");
    }

    public static void listen() {
        try {
            //轮训
            while (true) {
                //选择器执行选择,会阻塞
                selector.select();
                //获取所有的事件激活的SelectionKeys
                Set<SelectionKey> selectionKeys = selector.selectedKeys();
                //遍历selectionKeys,对其进行处理
                for (SelectionKey key : selectionKeys) {
                    //如果客户端刚发出连接请求
                    if (key.isAcceptable()) {
                        SocketChannel socketChannel = ssChannel.accept();
                        //设置客户端套接字通道为非阻塞
                        socketChannel.configureBlocking(false);
                        //注册进选择器,事件为可读
                        socketChannel.register(selector, SelectionKey.OP_READ, ByteBuffer.allocate(1024));
                        //加入在线用户集合
                        onlineUsers.put(socketChannel, socketChannel.socket().getLocalAddress().toString() + ":" + socketChannel.socket().getPort());

                        System.out.println(onlineUsers.get(socketChannel) + "上线");
                        //可以读取客户端的数据
                    } else if (key.isReadable()) {
                        handle(key);
                    }

                    selectionKeys.remove(key);
                }
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) throws IOException {
        new Server().listen();
    }

    private static void broadCast(ByteBuffer buffer, int length, SocketChannel itself) {
        String msg = onlineUsers.get(itself) + "说:" + new String(buffer.array(), 0, length);
        System.out.println(msg);
        try {
            for (SocketChannel socketChannel : onlineUsers.keySet()) {
                if (socketChannel != itself) {
                    socketChannel.write(ByteBuffer.wrap(msg.getBytes()));
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static void handle(SelectionKey key) throws IOException {
        if (key == null)
            return;
        SocketChannel socketChannel = null;
        try {
            //获取缓冲组件
            ByteBuffer buffer = (ByteBuffer) key.attachment();
            //获取客户端套接字通道
            socketChannel = (SocketChannel) key.channel();
            //设置为非阻塞
            socketChannel.configureBlocking(false);
            //读
            int read = socketChannel.read(buffer);
            if (read > 0) {
                buffer.flip();
                broadCast(buffer, read, socketChannel);
                buffer.clear();
            } else if (read == -1) {
                System.out.println("正常离线");
                key.cancel();
                socketChannel.close();
                System.out.println(onlineUsers.get(socketChannel) + "离线");
            }

        } catch (IOException e) {
            System.out.println("异常离线");
            System.out.println(onlineUsers.get(socketChannel) + "离线");
            key.cancel();
            if (socketChannel != null)
                socketChannel.close();
            onlineUsers.remove(socketChannel);
        }
    }
}

```

##### Client

```
package com.chuquwan.groupchat;

import java.io.IOException;
import java.net.InetSocketAddress;
import java.nio.ByteBuffer;
import java.nio.channels.SelectionKey;
import java.nio.channels.Selector;
import java.nio.channels.SocketChannel;
import java.util.Scanner;
import java.util.Set;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;

public class Client {

    private static final int SERVER_PORT = 9999;
    private static final String ADDRESS = "127.0.0.1";

    private static SocketChannel socketChannel;

    private static Selector selector;

    private static ExecutorService executorService = null;

    public Client() throws IOException {
        socketChannel = SocketChannel.open();
        selector = Selector.open();

        socketChannel.configureBlocking(false);
        socketChannel.register(selector, SelectionKey.OP_CONNECT, ByteBuffer.allocate(1024));

        executorService = Executors.newFixedThreadPool(1);
    }

    public static void conn() throws IOException {

        InetSocketAddress inetSocketAddress = new InetSocketAddress(ADDRESS, SERVER_PORT);
        socketChannel.connect(inetSocketAddress);

        try {
            selector.select();
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            for (SelectionKey key : selectionKeys) {
                if (key.isConnectable()) {
                    socketChannel.finishConnect();
                    System.out.println("客户端连接服务器成功");
                    key.interestOps(SelectionKey.OP_WRITE | SelectionKey.OP_READ);
                }
                selectionKeys.remove(key);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }


        //读线程
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                try {
                    while (true) {
                        selector.select();
                        Set<SelectionKey> selectionKeys = selector.selectedKeys();
                        for (SelectionKey key : selectionKeys) {
                            if (key.isReadable()) {
                                System.out.println("readable");
                                ByteBuffer buffer = (ByteBuffer) key.attachment();
                                SocketChannel socketChannel = (SocketChannel) key.channel();
                                int read = socketChannel.read(buffer);
                                buffer.flip();
                                System.out.println(new String(buffer.array(), 0, read));
                                buffer.clear();
                            }
                            selectionKeys.remove(key);
                        }
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }).start();


        //写线程
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNext()) {
            String msg = scanner.next();
            if ("exit".equals(msg))
                return;
            try {
                socketChannel.write(ByteBuffer.wrap(msg.getBytes()));
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }


    public static void main(String[] args) throws IOException {
        new Client().conn();
    }
}


```



### SelectionKey

#### API

​	channel()		获取对应的通道

​	interestOps()		重新设置事件类型

​	attachment()		可以获取channel注册时添加的组件

```
channel.register(Selector,int,Object)	第三个是组件
```

​	cancel()		如果对应的SocketChannel关闭了，那么使用此方法关闭对应的SelectionKey



### 零拷贝

​	DMA拷贝和CPU拷贝

​	零拷贝是零CPU拷贝

#### 	transferTo

#### 	sendFile