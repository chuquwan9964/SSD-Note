

# Netty

Netty 是一个基于 JAVA NIO 类库的异步通信框架，它的架构特点是：异步非阻塞、基于事件驱动、高性能、高可靠性和高可定制性。

#### Maven坐标

```
<!-- https://mvnrepository.com/artifact/io.netty/netty-all -->
<dependency>
    <groupId>io.netty</groupId>
    <artifactId>netty-all</artifactId>
    <version>4.1.16.Final</version>
</dependency>

```



### 线程模型

#### 	Rector

​		由选择器Selector，dispatcher组成

​		选择器负责监听事件，dispatcher负责分发任务，你懂我的意思吧

#### 	单Rector单线程

​		缺点：

​			只有一个线程，性能太低，无法发挥多核CPU的优势

​			线程如果意外终止，将会造成严重后果

​		

​		优点：

​			模型简单

​		

​		适用场景：

​			客户端数量有限，业务处理非常快速

​		

```
无多线程的NIO客户服务端发送数据，详见NIO笔记简易聊天室
```



#### 	单Rector多线程

​		优点：

​			充分发挥多核CPU的能力

​		缺点：

​			多线程，必然要产生数据的安全问题

​			reactor处理所有的事件监听和响应，在单线程中运行，无法保证高并发的效率问题

```
将业务处理单独分配到一个线程中执行
```



#### 	主从Rector多线程

​	就是netty的模型（有点难呀...2020/01/23	20:51）





#### 简单案例(Netty)

##### 	Server

```
package com.chuquwan.netty;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.util.CharsetUtil;

import javax.print.attribute.standard.NumberUp;

public class NettyServer {

    public static void main(String[] args) throws InterruptedException {
        //bossGroup和workerGroup里的NioEventLoop数量默认为本机CPU核心数*2
        NioEventLoopGroup bossGroup = new NioEventLoopGroup();
        NioEventLoopGroup workerGroup = new NioEventLoopGroup(1);
        try {

            //创建服务器端的启动对象，配置参数
            ServerBootstrap bootstrap = new ServerBootstrap();

            //使用链式编程来进行设置
            bootstrap.group(bossGroup, workerGroup) //设置两个线程组
                    .channel(NioServerSocketChannel.class) //使用NioSocketChannel 作为服务器的通道实现
                    .option(ChannelOption.SO_BACKLOG, 128) // 设置线程队列得到连接个数
                    .childOption(ChannelOption.SO_KEEPALIVE, true) //设置保持活动连接状态
//                    .handler(null) // 该 handler对应 bossGroup , childHandler 对应 workerGroup
                    .childHandler(new ChannelInitializer<SocketChannel>() {//创建一个通道初始化对象(匿名对象)
                        //给pipeline 设置处理器
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            System.out.println("客户socketchannel hashcode=" + ch.hashCode()); //可以使用一个集合管理 SocketChannel， 再推送消息时，可以将业务加入到各个channel 对应的 NIOEventLoop 的 taskQueue 或者 scheduleTaskQueue
                            ch.pipeline().addLast(new NettyServerHandler());
                        }
                    }); // 给我们的workerGroup 的 EventLoop 对应的管道设置处理器

            System.out.println(".....服务器 is ready...");

            ChannelFuture channelFuture = bootstrap.bind(6668).sync();

            channelFuture.channel().closeFuture().sync();

        }finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }
}

class NettyServerHandler extends ChannelInboundHandlerAdapter {
    @Override
    /**
     * 读的方法
     */
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {

        System.out.println(ctx);
        ByteBuf byteBuf = (ByteBuf)msg;
        System.out.println("客户端发送的消息是:"+byteBuf.toString(CharsetUtil.UTF_8));
        System.out.println("客户端地址是:"+ctx.channel().remoteAddress());
        //将某一耗时任务任务加入任务队列异步执行,这样下面的方法就会得到执行机会,否则下面的方法会一直等待此耗时方法执行完毕,导致效率降低
        ctx.channel().eventLoop().execute(new Runnable() {
            @Override
            public void run() {
                System.out.println("我要阻塞了");
                while (true);
            }
        });
    }

    @Override
    /**
     * 回复消息的方法
     */
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
        System.out.println("给客户端发送数据");
        ctx.writeAndFlush(Unpooled.copiedBuffer("hello 客户端",CharsetUtil.UTF_8));
    }
}

```

##### 	Client

```
package com.chuquwan.netty;

import io.netty.bootstrap.Bootstrap;
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.ChannelInboundHandlerAdapter;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.util.CharsetUtil;

public class NettyClient {

    public static void main(String[] args) throws InterruptedException {
        NioEventLoopGroup eventExecutors = new NioEventLoopGroup();
        Bootstrap bootstrap = new Bootstrap();

        bootstrap.group(eventExecutors)
                .channel(NioSocketChannel.class)
                .handler(new ChannelInitializer<SocketChannel>() {
                    @Override
                    protected void initChannel(SocketChannel socketChannel) throws Exception {
                        socketChannel.pipeline().addLast(new NettyClientHandler());
                    }
                });

        ChannelFuture channelFuture = bootstrap.connect("127.0.0.1", 6668).sync();
        channelFuture.channel().closeFuture().sync();
    }
}

class NettyClientHandler extends ChannelInboundHandlerAdapter {
    @Override
    /**
     * 当通道就绪是就会触发该方法
     */
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        System.out.println("client"+ctx);
        ctx.writeAndFlush(Unpooled.copiedBuffer("hello server", CharsetUtil.UTF_8));
    }

    @Override
    /**
     * 当通道有读取事件时会触发
     */
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        System.out.println(ctx);
        ByteBuf byteBuf = (ByteBuf)msg;
        System.out.println("服务器发送的消息是:"+byteBuf.toString(CharsetUtil.UTF_8));
        System.out.println("服务器地址是:"+ctx.channel().remoteAddress());
    }
}

```



##### 任务队列

解决某一耗时任务造成线程阻塞的问题

###### 	taskQueue自定义任务

```
class NettyServerHandler extends ChannelInboundHandlerAdapter {
    @Override
    /**
     * 读的方法
     */
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {

        System.out.println(ctx);
        ByteBuf byteBuf = (ByteBuf)msg;
        System.out.println("客户端发送的消息是:"+byteBuf.toString(CharsetUtil.UTF_8));
        System.out.println("客户端地址是:"+ctx.channel().remoteAddress());
         //将某一耗时任务任务加入任务队列异步执行,这样下面的方法就会得到执行机会,否则下面的方法会一直等待此耗时方法执行完毕,导致效率降低
         //一个单例的线程池
        ctx.channel().eventLoop().execute(new Runnable() {
            @Override
            public void run() {
                System.out.println("我要阻塞了");
                while (true);
            }
        });
    }
    //下面的输出会与上面的线程异步执行
    System.out.println("继续执行2020/1/24   20:33除夕");

    @Override
    /**
     * 回复消息的方法
     */
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
        System.out.println("给客户端发送数据");
        ctx.writeAndFlush(Unpooled.copiedBuffer("hello 客户端",CharsetUtil.UTF_8));
    }
}
```

如果有多个耗时任务，那么耗时任务串行运行，因为NioEventLoop继承了SingleThreadEventLoop继承了SingleThreadEventExecutor，还记得吗???

单线程线程池，只有一个线程



###### scheduledTaskQueue

```
eventLoop.schedule(new Runnable() {	
    @Override
    public void run() {
    	System.out.println("延时任务");
    }
},1, TimeUnit.SECONDS);	

虽然是异步执行的，但是内部的执行器还是eventLoop的executor，也就是说还是那个单例的线程池，如果上面有
        eventLoop.execute(new Runnable() {
            @Override
            public void run() {
                System.out.println("我要阻塞了");
                try {
                    Thread.sleep(2000);
                    System.out.println("阻塞完毕");
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
的话，还是得等上面的代码执行完毕才能执行（因为共用一个单例的线程池）
```



###### 推送业务

可以使用一个集合管理 SocketChannel， 再推送消息时，可以将业务加入到各个channel 对应的 NIOEventLoop 的 taskQueue 或者 scheduleTaskQueue

```
bootstrap.group(bossGroup, workerGroup) //设置两个线程组
    .channel(NioServerSocketChannel.class) //使用NioSocketChannel 作为服务器的通道实现
    .option(ChannelOption.SO_BACKLOG, 128) // 设置线程队列得到连接个数
    .childOption(ChannelOption.SO_KEEPALIVE, true) //设置保持活动连接状态
	.childHandler(new ChannelInitializer<SocketChannel>() {//创建一个通道初始化对象(匿名对象)
    @Override
    //这个可能就是客户端连接进来了，让workerGroup注册客户端通道的方法
    protected void initChannel(SocketChannel ch) throws Exception {
		System.out.println(Thread.currentThread().getName());
        //只要有客户端连接进来就会执行
        System.out.println("客户socketchannel hashcode=" + ch.hashCode());
        ch.eventLoop().execute(new Runnable() {
            @Override
            public void run() {
                for (int i = 0; i < 5; i++) {
                	System.out.println("给来的客户端推送业务");
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
				}
			}
		});
		ch.pipeline().addLast(new NettyServerHandler());
	}
}); // 给我们的workerGroup 的 EventLoop 对应的管道设置处理器
```



###### 设置ChannelFutureListener来监听是否绑定成功

```
        ChannelFuture channelFuture = serverBootstrap.bind(8888).sync();

        channelFuture.addListener(new ChannelFutureListener() {
            @Override
            public void operationComplete(ChannelFuture future) throws Exception {
                boolean success = future.isSuccess();
                if (success) {
                    System.out.println("监听8888端口成功");
                } else {
                    System.out.println("监听8888端口失败");
                }
            }
        });
```



#### 自己编写HTTP服务器

```
package netty.http;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.http.*;
import io.netty.util.CharsetUtil;


public class HttpServer {

    public static void main(String[] args) {
        NioEventLoopGroup bossGroup = new NioEventLoopGroup(1);
        NioEventLoopGroup workerGroup = new NioEventLoopGroup();

        try {
            ServerBootstrap serverBootstrap = new ServerBootstrap();

            serverBootstrap.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .childHandler(new MyChannelInitializer());

            ChannelFuture channelFuture = serverBootstrap.bind(8888).sync();

            channelFuture.addListener((future -> {
                if (future.isSuccess()) {
                    System.out.println("监听8888端口成功");
                } else {
                    System.out.println("监听8888端口失败");
                }
            }));

            channelFuture.channel().closeFuture().sync();


        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }

    }
}

class HttpServerHandler extends SimpleChannelInboundHandler<HttpObject> {
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, HttpObject msg) throws Exception {
        if (msg instanceof HttpRequest) {
            System.out.println("客户端地址:" + ctx.channel().remoteAddress());

            //回复客户端信息
            ByteBuf content = Unpooled.copiedBuffer("hello 我是服务器", CharsetUtil.UTF_8);
            DefaultFullHttpResponse response = new DefaultFullHttpResponse(HttpVersion.HTTP_1_1, HttpResponseStatus.OK, content);

            //设置返回数据类型为文本类型
            response.headers().set(HttpHeaderNames.CONTENT_TYPE, "text/html;charset=utf-8");
            response.headers().set(HttpHeaderNames.CONTENT_LENGTH, content.readableBytes());

            ctx.writeAndFlush(response);

        }
    }
}

class MyChannelInitializer extends ChannelInitializer<SocketChannel> {
    @Override
    protected void initChannel(SocketChannel ch) throws Exception {
        System.out.println("客户端来连接了:" + ch.remoteAddress());
        ChannelPipeline pipeline = ch.pipeline();
        pipeline.addLast("MyHttpServerCodec", new HttpServerCodec());
        pipeline.addLast(new HttpServerHandler());
    }
}

```



### Netty核心组件



#### ChannelOption

##### 	ChannelOption.SO_BACKLOG

​		对应 TCP/IP 协议 listen 函数中的 backlog 参数，用来初始化服务器可连接队列大小。服务端处理客户端		连接请求是顺序处理的，所以同一时间只能处理一个客户端连接。多个客户端来的时候，服务端将不能处理的客户端连接请求放在队列中等待处理，backlog 参数指定了队列的大小。

##### 	ChannelOption.SO_KEEPALIVE

​		一直保持连接活动状态

#### pipeLine

​	双向链表，里面是ChannelHandlerContext的集合，ChannelHandlerContext里有ChannelHandler

#### ChannelHandlerContext

​	ChannelHandler的上下文，可以用它得到channel，pipeline，和channelHandler

```
ChannelFuture close()，关闭通道
ChannelOutboundInvoker flush()，刷新
ChannelFuture writeAndFlush(Object msg) ， 将 数 据 写 到 ChannelPipeline 中 当 前
ChannelHandler 的下一个 ChannelHandler 开始处理（出站）
```

#### ChannelHandler

​	通道事件的处理者



#### Unpooled

​	一个工具类，用来初始化ByteBuf

​	ByteBuf buffer(int capacity)

​	ByteBuf copiedBuffer(CharSequence string, Charset charset)

```
ByteBuf buffer = Unpooled.buffer(10);
UnpooledByteBufAllocator$InstrumentedUnpooledUnsafeHeapByteBuf(ridx: 0, widx: 0, cap: 10)
ByteBuf buffer1 = Unpooled.buffer();	//默认是256
UnpooledByteBufAllocator$InstrumentedUnpooledUnsafeHeapByteBuf(ridx: 0, widx: 0, cap: 256)
```



##### 	ByteBuf

​		相当于NIO中的ByteBuffer，只不过比ByteBuffer高级一点，读的时候不需要类似于flip()这样的操作

​		内部有一个byte数组

​		readIndex	当前可读的字节的下标

​		writeIndex	当前可写的字节的下标

​		capacity		数组的总容量



#### Netty的聊天室

##### 	\r&\n

​		

| 符号 |  意义  |
| :--: | :----: |
|  \r  | 回车符 |
|  \n  | 换行符 |

```
在Windows中：

'\r' 回车，回到当前行的行首，而不会换到下一行，如果接着输出的话，本行以前的内容会被逐一覆盖；

'\n' 换行，换到当前位置的下一行，而不会回到行首；
```

```
Unix系统里，每行结尾只有“<换行>”，即"\n"；Windows系统里面，每行结尾是“<回车><换行>”，即“\r\n”；Mac系统里，每行结尾是“<回车>”，即"\r"；。一个直接后果是，Unix/Mac系统下的文件在Windows里打开的话，所有文字会变成一行；而Windows里的文件在Unix/Mac下打开的话，在每行的结尾可能会多出一个^M符号。
```

```
System.out.println("hello\n12345");	
输出结果:
hello
12345

System.out.println("hello\r12345");
输出结果:
12345		//把hello覆盖了
```



##### 	Server

```
package netty.groupchat;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.buffer.ByteBuf;
import io.netty.channel.*;
import io.netty.channel.group.ChannelGroup;
import io.netty.channel.group.DefaultChannelGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.string.StringDecoder;
import io.netty.handler.codec.string.StringEncoder;
import io.netty.util.concurrent.Future;
import io.netty.util.concurrent.GenericFutureListener;
import io.netty.util.concurrent.GlobalEventExecutor;

import java.nio.charset.Charset;
import java.text.SimpleDateFormat;
import java.util.Date;
import java.util.HashMap;
import java.util.Map;

public class Server {


    private EventLoopGroup bossGroup;
    private EventLoopGroup workerGroup;

    private static final int PORT = 8888;
    private ServerBootstrap serverBootstrap;

    public Server() {
        init();
    }

    public void init() {
        bossGroup = new NioEventLoopGroup(1);
        workerGroup = new NioEventLoopGroup();
        serverBootstrap = new ServerBootstrap();
    }

    public void start() {
        try {
            serverBootstrap.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline pipeline = ch.pipeline();
                            //加入相关handler
                            //这里为什么要加这两个编码解码器呢？请看client下面的讲解
                            pipeline.addLast("decoder", new StringDecoder());
                            pipeline.addLast("encoder", new StringEncoder());
                            pipeline.addLast(new ServerHandler());

                        }
                    });

            serverBootstrap.bind(PORT).sync().addListener(new GenericFutureListener<Future<? super Void>>() {
                @Override
                public void operationComplete(Future<? super Void> future) throws Exception {
                    if (future.isSuccess())
                        System.out.println("服务器启动成功,正在监听" + PORT + "端口\r\n");
                    else
                        System.out.println("服务器启动失败\r\n");
                }
            });

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        Server server = new Server();
        server.start();
    }

}

class ServerHandler extends SimpleChannelInboundHandler<String> {

    private static ChannelGroup channels = new DefaultChannelGroup(GlobalEventExecutor.INSTANCE);
    private static SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");

    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        String msg = "[客户端]------" + ctx.channel().remoteAddress().toString().substring(1) + "------[上线了]\r\n";
        System.out.println(msg);
        channels.writeAndFlush(msg);
        channels.add(ctx.channel());
    }

    @Override
    public void channelInactive(ChannelHandlerContext ctx) throws Exception {
        String msg = "[客户端]******" + ctx.channel().remoteAddress().toString().substring(1) + "******[下线了]\r\n";
        System.out.println(msg);
        channels.writeAndFlush(msg);
    }

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, String msg) throws Exception {
        System.out.println(msg);
        //获取发送信息的channel
        Channel channel = ctx.channel();
        channels.forEach((c) -> {
            if (c != channel)
                c.writeAndFlush(channel.remoteAddress() + "[" + sdf.format(new Date()) + "]" + "说:" + msg + "\r\n");
        });
    }
}


```



##### 	Client

```
package netty.groupchat;

import io.netty.bootstrap.Bootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.string.StringDecoder;
import io.netty.handler.codec.string.StringEncoder;

import java.util.Scanner;

public class Client {

    private final int PORT;
    private final String HOST;

    public Client(int port, String host) {
        this.PORT = port;
        this.HOST = host;
    }

    public void conn() {
        EventLoopGroup eventGroup = new NioEventLoopGroup();
        try {

            Bootstrap bootstrap = new Bootstrap();
            bootstrap.group(eventGroup)
                    .channel(NioSocketChannel.class)
                    .handler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline pipeline = ch.pipeline();
                            //加入相关handler
                            pipeline.addLast("decoder", new StringDecoder());
                            pipeline.addLast("encoder", new StringEncoder());
                            pipeline.addLast(new ClientHandler());
                        }
                    });


            ChannelFuture channelFuture = bootstrap.connect(HOST, PORT).sync();
            Channel channel = channelFuture.channel();
            Scanner scanner = new Scanner(System.in);

            while (scanner.hasNext()) {
                String msg = scanner.nextLine();
                if ("exit".equals(msg))
                    break;
                channel.writeAndFlush(msg + "\r\n");
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            eventGroup.shutdownGracefully();
        }
    }

    public static void main(String[] args) {
        new Client(8888, "127.0.0.1").conn();
    }
}

class ClientHandler extends SimpleChannelInboundHandler<String> {
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, String msg) throws Exception {
        System.out.println(msg.trim());
    }
}

```



##### 为什么加编码解码器

```
pipeline.addLast("decoder", new StringDecoder());
pipeline.addLast("encoder", new StringEncoder());

因为我们的handler是继承了SimpleChannelInboundHandler<String>，泛型String就表示方法channelRead0读到的通道中的信息类型，那么肯定要有编码解码器来将数据转换成String，如果没有编码解码器，就不会走这个方法，也就不管你写多少数据，那也是读不到的
```



##### 私聊



##### 心跳检测机制

```
package netty.groupchat.heartbeat;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.logging.LoggingHandler;
import io.netty.handler.timeout.IdleStateHandler;

import java.net.Socket;
import java.util.concurrent.TimeUnit;

public class Server {

    public static void main(String[] args) {
        NioEventLoopGroup bossGroup = new NioEventLoopGroup();
        NioEventLoopGroup workerGroup = new NioEventLoopGroup();

        ServerBootstrap bootstrap = new ServerBootstrap();

        try {
            bootstrap.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .handler(new LoggingHandler())
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline pipeline = ch.pipeline();
                            /**
                             * readerIdleTime:如果3秒钟之内，服务器没有对此通道发生读事件，就会发送心跳检测包
                             * writerIdleTime:5s之内，服务器没有对此通道发生写事件，就发送心跳检测包
                             * allIdleTime: 7s之内服务器没有发生读写事件，就发送心跳检测包
                             	如果发生了空闲事件，则此处理器将交给下一个处理器的userEventTriggered								方法执行
                             */
                            pipeline.addLast(new IdleStateHandler(3, 5, 7, TimeUnit.SECONDS));
                            pipeline.addLast(new HeartBeatServerHandler());
                        }
                    });

            ChannelFuture channelFuture = bootstrap.bind(8888).sync();
            channelFuture.addListener(new ChannelFutureListener() {
                @Override
                public void operationComplete(ChannelFuture future) throws Exception {
                    if (future.isSuccess())
                        System.out.println("listen on port 8888 ......");
                    else
                        System.out.println("boot failed");
                }
            });

            channelFuture.channel().closeFuture().sync();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }

    }

}

class HeartBeatServerHandler extends ChannelInboundHandlerAdapter {
    @Override
    public void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception {
    	if (evt instanceof IdleStateEvent) {
            IdleStateEvent stateEvent = (IdleStateEvent) evt;
            switch (stateEvent.state()){
                case READER_IDLE:
                    System.out.println("读空闲");
                    break;
                case WRITER_IDLE:
                    System.out.println("写空闲");
                    break;
                case ALL_IDLE:
                    System.out.println("读写空闲");
                    break;
            }
        }
    }
}

```



#### web socket长连接

Web Socket是基于Http协议的，如若不懂，一定看以下链接

<https://blog.csdn.net/qq_28090573/article/details/89084459>

```
websocket请求头

GET /chat HTTP/1.1
Host: server.example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
Sec-WebSocket-Protocol: chat, superchat
Sec-WebSocket-Version: 13
Origin: http://example.com
```

web socket实现长连接全双工通信，也就是说服务器可以主动发送数据给客户端，这是AJAX做不到的，也是HTTP协议做不到的。（AJAX就是基于HTTP协议的）



##### server

```
package netty.websocket;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.http.HttpObjectAggregator;
import io.netty.handler.codec.http.HttpServerCodec;
import io.netty.handler.codec.http.websocketx.TextWebSocketFrame;
import io.netty.handler.codec.http.websocketx.WebSocketServerProtocolHandler;
import io.netty.handler.logging.LogLevel;
import io.netty.handler.logging.LoggingHandler;
import io.netty.handler.stream.ChunkedWriteHandler;

import java.time.LocalTime;
import java.util.Date;

public class Server {

    public static void main(String[] args) throws InterruptedException {
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup();

        ServerBootstrap serverBootstrap = new ServerBootstrap();

        try {
            serverBootstrap.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .handler(new LoggingHandler(LogLevel.INFO))
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline pipeline = ch.pipeline();
                            //HTTP请求的编解码器
                            pipeline.addLast(new HttpServerCodec());
                            pipeline.addLast(new ChunkedWriteHandler());
                            pipeline.addLast(new HttpObjectAggregator(8192));
                            /**
                             * WebSocketServerProtocolHandler 核心功能是将 http协议升级为 ws协议 , 保持长连接
                             *  状态码101
                             *  对应的协议URL是:ws://localhost:8888/chat
                             *
                             */
                            pipeline.addLast(new WebSocketServerProtocolHandler("/chat"));
                            //添加自己的处理器
                            pipeline.addLast(new MyWebSocketHandler());
                        }
                    });

            ChannelFuture channelFuture = serverBootstrap.bind(8888).sync();
            channelFuture.addListener((future -> {
                if (future.isSuccess())
                    System.out.println("监听成功");
            }));
            channelFuture.channel().closeFuture().sync();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }

    }

}

class MyWebSocketHandler extends SimpleChannelInboundHandler<TextWebSocketFrame> {

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, TextWebSocketFrame msg) throws Exception {
        System.out.println(ctx.channel().remoteAddress().toString().substring(1) + "发来了消息:" + msg.text());
        ctx.channel().writeAndFlush(new TextWebSocketFrame("收到消息" + LocalTime.now()));
    }
}

```



##### hello.html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<script>
    var socket;
    //判断当前浏览器是否支持websocket
    if(window.WebSocket) {
        //go on
        socket = new WebSocket("ws://127.0.0.1:8888/chat");
        //相当于channelReado, ev 收到服务器端回送的消息
        socket.onmessage = function (ev) {
            var rt = document.getElementById("responseText");
            rt.value = rt.value + "\n" + ev.data;
        }

        //相当于连接开启(感知到连接开启)
        socket.onopen = function (ev) {
            var rt = document.getElementById("responseText");
            rt.value = "连接开启了.."
        }

        //相当于连接关闭(感知到连接关闭)
        socket.onclose = function (ev) {

            var rt = document.getElementById("responseText");
            rt.value = rt.value + "\n" + "连接关闭了.."
        }
    } else {
        alert("当前浏览器不支持websocket")
    }

    //发送消息到服务器
    function send(message) {
        if(!window.socket) { //先判断socket是否创建好
            return;
        }
        if(socket.readyState == WebSocket.OPEN) {
            //通过socket 发送消息
            socket.send(message)
        } else {
            alert("连接没有开启");
        }
    }
</script>
    <form onsubmit="return false">
        <textarea name="message" style="height: 300px; width: 300px"></textarea>
        <input type="button" value="发生消息" onclick="send(this.form.message.value)">
        <textarea id="responseText" style="height: 300px; width: 300px"></textarea>
        <input type="button" value="清空内容" onclick="document.getElementById('responseText').value=''">
    </form>
</body>
</html>
```



#### Protobuf

​	Protobuf的客户端和服务端不要求采用同一语言编写，支持多种语言，因为他在通道中传输的字节数组经过了他的封装，使得服务器不管是什么语言，都可以对其进行解封并且包装对象

##### 	传输单一对象

```
思路:就是在出站时，对字节数组进行封装，规定如果字节为8，那么后面就读一个int类型数据，如果字节为18，就读一串字符串
```

###### 		Server

```
package netty.protobuf;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.protobuf.ProtobufDecoder;
import io.netty.handler.codec.string.StringDecoder;

public class NettyServer {
    public static void main(String[] args) throws Exception {


        //创建BossGroup 和 WorkerGroup
        //说明
        //1. 创建两个线程组 bossGroup 和 workerGroup
        //2. bossGroup 只是处理连接请求 , 真正的和客户端业务处理，会交给 workerGroup完成
        //3. 两个都是无限循环
        //4. bossGroup 和 workerGroup 含有的子线程(NioEventLoop)的个数
        //   默认实际 cpu核数 * 2
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup(); //8


        try {
            //创建服务器端的启动对象，配置参数
            ServerBootstrap bootstrap = new ServerBootstrap();

            //使用链式编程来进行设置
            bootstrap.group(bossGroup, workerGroup) //设置两个线程组
                    .channel(NioServerSocketChannel.class) //使用NioSocketChannel 作为服务器的通道实现
                    .option(ChannelOption.SO_BACKLOG, 128) // 设置线程队列得到连接个数
                    .childOption(ChannelOption.SO_KEEPALIVE, true) //设置保持活动连接状态
//                    .handler(null) // 该 handler对应 bossGroup , childHandler 对应 workerGroup
                    .childHandler(new ChannelInitializer<SocketChannel>() {//创建一个通道初始化对象(匿名对象)
                        //给pipeline 设置处理器
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {


                            ChannelPipeline pipeline = ch.pipeline();
                            //在pipeline加入ProtoBufDecoder
                            //指定对哪种对象进行解码
                            pipeline.addLast("decoder", new ProtobufDecoder(StudentPOJO.Student.getDefaultInstance()));
                            pipeline.addLast(new NettyServerHandler());
                        }
                    }); // 给我们的workerGroup 的 EventLoop 对应的管道设置处理器

            System.out.println(".....服务器 is ready...");

            //绑定一个端口并且同步, 生成了一个 ChannelFuture 对象
            //启动服务器(并绑定端口)
            ChannelFuture cf = bootstrap.bind(6668).sync();

            //给cf 注册监听器，监控我们关心的事件

            cf.addListener(new ChannelFutureListener() {
                @Override
                public void operationComplete(ChannelFuture future) throws Exception {
                    if (cf.isSuccess()) {
                        System.out.println("监听端口 6668 成功");
                    } else {
                        System.out.println("监听端口 6668 失败");
                    }
                }
            });


            //对关闭通道进行监听
            cf.channel().closeFuture().sync();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }

    }

}

```

###### ServerHandler

```
package netty.protobuf;

import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.*;
import io.netty.util.CharsetUtil;

import java.nio.charset.Charset;

/*
说明
1. 我们自定义一个Handler 需要继续netty 规定好的某个HandlerAdapter(规范)
2. 这时我们自定义一个Handler , 才能称为一个handler
 */
//public class NettyServerHandler extends ChannelInboundHandlerAdapter {
public class NettyServerHandler extends SimpleChannelInboundHandler<StudentPOJO.Student> {


    //读取数据实际(这里我们可以读取客户端发送的消息)
    /*
    1. ChannelHandlerContext ctx:上下文对象, 含有 管道pipeline , 通道channel, 地址
    2. Object msg: 就是客户端发送的数据 默认Object
     */
    @Override
    public void channelRead0(ChannelHandlerContext ctx, StudentPOJO.Student msg) throws Exception {

        //读取从客户端发送的StudentPojo.Student


        System.out.println("客户端发送的数据 id=" + msg.getId() + " 名字=" + msg.getName());
    }


    //读取数据实际(这里我们可以读取客户端发送的消息)
    /*
    1. ChannelHandlerContext ctx:上下文对象, 含有 管道pipeline , 通道channel, 地址
    2. Object msg: 就是客户端发送的数据 默认Object
     */
//    @Override
//    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
//
//        //读取从客户端发送的StudentPojo.Student
//
////        StudentPOJO.Student student = (StudentPOJO.Student) msg;
//
////        System.out.println("客户端发送的数据 id=" + student.getId() + " 名字=" + student.getName());
//
//        System.out.println(msg);
//        System.out.println(((ByteBuf) msg).toString(Charset.forName("UTF-8")));
//
//    }

    //数据读取完毕
    @Override
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {

        //writeAndFlush 是 write + flush
        //将数据写入到缓存，并刷新
        //一般讲，我们对这个发送的数据进行编码
        ctx.writeAndFlush(Unpooled.copiedBuffer("hello, 客户端~(>^ω^<)喵1", CharsetUtil.UTF_8));
    }

    //处理异常, 一般是需要关闭通道

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        ctx.close();
    }
}

```





###### Client

```
package netty.protobuf;

import io.netty.bootstrap.Bootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.protobuf.ProtobufEncoder;
import io.netty.handler.codec.string.StringEncoder;

public class NettyClient {
    public static void main(String[] args) throws Exception {

        //客户端需要一个事件循环组
        EventLoopGroup group = new NioEventLoopGroup();


        try {
            //创建客户端启动对象
            //注意客户端使用的不是 ServerBootstrap 而是 Bootstrap
            Bootstrap bootstrap = new Bootstrap();

            //设置相关参数
            bootstrap.group(group) //设置线程组
                    .channel(NioSocketChannel.class) // 设置客户端通道的实现类(反射)
                    .handler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline pipeline = ch.pipeline();
                            //在pipeline中加入 ProtoBufEncoder
                            pipeline.addLast("encoder", new ProtobufEncoder());
                            pipeline.addLast(new NettyClientHandler()); //加入自己的处理器
//                            pipeline.addLast(new StringEncoder());
//                            pipeline.addLast(new NettyClientHandler2());
                        }
                    });

            System.out.println("客户端 ok..");

            //启动客户端去连接服务器端
            //关于 ChannelFuture 要分析，涉及到netty的异步模型
            ChannelFuture channelFuture = bootstrap.connect("127.0.0.1", 6668).sync();
            //给关闭通道进行监听
            channelFuture.channel().closeFuture().sync();
        } finally {

            group.shutdownGracefully();

        }
    }
}

public class NettyClientHandler extends ChannelInboundHandlerAdapter {

    //当通道就绪就会触发该方法
    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {

        //发生一个Student 对象到服务器

        StudentPOJO.Student student = StudentPOJO.Student.newBuilder().setId(4).setName("智多星 吴用").build();
        //Teacher , Member ,Message
        ctx.writeAndFlush(student);
    }

    //当通道有读取事件时，会触发
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {

        ByteBuf buf = (ByteBuf) msg;
        System.out.println("服务器回复的消息:" + buf.toString(CharsetUtil.UTF_8));
        System.out.println("服务器的地址： " + ctx.channel().remoteAddress());
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        cause.printStackTrace();
        ctx.close();
    }
}

```

###### 		Student.proto

```
syntax = "proto3"; //版本
option java_outer_classname = "StudentPOJO";//生成的外部类名，同时也是文件名
//protobuf 使用message 管理数据
message Student { //会在 StudentPOJO 外部类生成一个内部类 Student， 他是真正发送的POJO对象
    int32 id = 1; // Student 类中有 一个属性 名字为 id 类型为int32(protobuf类型) 1表示属性序号，不是值
    string name = 2;
}
```



##### 	传输多个对象

```
思路:其实本质上还是一个对象，只不过这个对象里定义多个不同的对象，然后通过这个对象的一个属性来规定其包装的是哪一个对象
```

###### 		Server

```
package netty.protobuf2;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.protobuf.ProtobufDecoder;

public class NettyServer {
    public static void main(String[] args) throws Exception {


        //创建BossGroup 和 WorkerGroup
        //说明
        //1. 创建两个线程组 bossGroup 和 workerGroup
        //2. bossGroup 只是处理连接请求 , 真正的和客户端业务处理，会交给 workerGroup完成
        //3. 两个都是无限循环
        //4. bossGroup 和 workerGroup 含有的子线程(NioEventLoop)的个数
        //   默认实际 cpu核数 * 2
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup(); //8



        try {
            //创建服务器端的启动对象，配置参数
            ServerBootstrap bootstrap = new ServerBootstrap();

            //使用链式编程来进行设置
            bootstrap.group(bossGroup, workerGroup) //设置两个线程组
                    .channel(NioServerSocketChannel.class) //使用NioSocketChannel 作为服务器的通道实现
                    .option(ChannelOption.SO_BACKLOG, 128) // 设置线程队列得到连接个数
                    .childOption(ChannelOption.SO_KEEPALIVE, true) //设置保持活动连接状态
//                    .handler(null) // 该 handler对应 bossGroup , childHandler 对应 workerGroup
                    .childHandler(new ChannelInitializer<SocketChannel>() {//创建一个通道初始化对象(匿名对象)
                        //给pipeline 设置处理器
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {


                            ChannelPipeline pipeline = ch.pipeline();
                            //在pipeline加入ProtoBufDecoder
                            //指定对哪种对象进行解码
                            pipeline.addLast("decoder", new ProtobufDecoder(MyDataInfo.MyMessage.getDefaultInstance()));
                            pipeline.addLast(new NettyServerHandler());
                        }
                    }); // 给我们的workerGroup 的 EventLoop 对应的管道设置处理器

            System.out.println(".....服务器 is ready...");

            //绑定一个端口并且同步, 生成了一个 ChannelFuture 对象
            //启动服务器(并绑定端口)
            ChannelFuture cf = bootstrap.bind(6668).sync();

            //给cf 注册监听器，监控我们关心的事件

            cf.addListener(new ChannelFutureListener() {
                @Override
                public void operationComplete(ChannelFuture future) throws Exception {
                    if (cf.isSuccess()) {
                        System.out.println("监听端口 6668 成功");
                    } else {
                        System.out.println("监听端口 6668 失败");
                    }
                }
            });


            //对关闭通道进行监听
            cf.channel().closeFuture().sync();
        }finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }

    }

}

```

###### ServerHandler

```
package netty.protobuf2;

import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.util.CharsetUtil;

/*
说明
1. 我们自定义一个Handler 需要继续netty 规定好的某个HandlerAdapter(规范)
2. 这时我们自定义一个Handler , 才能称为一个handler
 */
//public class NettyServerHandler extends ChannelInboundHandlerAdapter {
public class NettyServerHandler extends SimpleChannelInboundHandler<MyDataInfo.MyMessage> {


    //读取数据实际(这里我们可以读取客户端发送的消息)
    /*
    1. ChannelHandlerContext ctx:上下文对象, 含有 管道pipeline , 通道channel, 地址
    2. Object msg: 就是客户端发送的数据 默认Object
     */
    @Override
    public void channelRead0(ChannelHandlerContext ctx, MyDataInfo.MyMessage msg) throws Exception {

        //根据dataType 来显示不同的信息

        MyDataInfo.MyMessage.DataType dataType = msg.getDataType();
        if(dataType == MyDataInfo.MyMessage.DataType.StudentType) {

            MyDataInfo.Student student = msg.getStudent();
            System.out.println("学生id=" + student.getId() + " 学生名字=" + student.getName());

        } else if(dataType == MyDataInfo.MyMessage.DataType.WorkerType) {
            MyDataInfo.Worker worker = msg.getWorker();
            System.out.println("工人的名字=" + worker.getName() + " 年龄=" + worker.getAge());
        } else {
            System.out.println("传输的类型不正确");
        }


    }



//    //读取数据实际(这里我们可以读取客户端发送的消息)
//    /*
//    1. ChannelHandlerContext ctx:上下文对象, 含有 管道pipeline , 通道channel, 地址
//    2. Object msg: 就是客户端发送的数据 默认Object
//     */
//    @Override
//    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
//
//        //读取从客户端发送的StudentPojo.Student
//
//        StudentPOJO.Student student = (StudentPOJO.Student) msg;
//
//        System.out.println("客户端发送的数据 id=" + student.getId() + " 名字=" + student.getName());
//    }

    //数据读取完毕
    @Override
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {

        //writeAndFlush 是 write + flush
        //将数据写入到缓存，并刷新
        //一般讲，我们对这个发送的数据进行编码
        ctx.writeAndFlush(Unpooled.copiedBuffer("hello, 客户端~(>^ω^<)喵1", CharsetUtil.UTF_8));
    }

    //处理异常, 一般是需要关闭通道

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        ctx.close();
    }
}

```

​	



###### 	Client

```
package netty.protobuf2;

import io.netty.bootstrap.Bootstrap;
import io.netty.channel.ChannelFuture;
import io.netty.channel.ChannelInitializer;
import io.netty.channel.ChannelPipeline;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.protobuf.ProtobufEncoder;

public class NettyClient {
    public static void main(String[] args) throws Exception {

        //客户端需要一个事件循环组
        EventLoopGroup group = new NioEventLoopGroup();


        try {
            //创建客户端启动对象
            //注意客户端使用的不是 ServerBootstrap 而是 Bootstrap
            Bootstrap bootstrap = new Bootstrap();

            //设置相关参数
            bootstrap.group(group) //设置线程组
                    .channel(NioSocketChannel.class) // 设置客户端通道的实现类(反射)
                    .handler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline pipeline = ch.pipeline();
                            //在pipeline中加入 ProtoBufEncoder
                            pipeline.addLast("encoder", new ProtobufEncoder());
                            pipeline.addLast(new NettyClientHandler()); //加入自己的处理器
                        }
                    });

            System.out.println("客户端 ok..");

            //启动客户端去连接服务器端
            //关于 ChannelFuture 要分析，涉及到netty的异步模型
            ChannelFuture channelFuture = bootstrap.connect("127.0.0.1", 6668).sync();
            //给关闭通道进行监听
            channelFuture.channel().closeFuture().sync();
        }finally {

            group.shutdownGracefully();

        }
    }
}

public class NettyClientHandler extends ChannelInboundHandlerAdapter {

    //当通道就绪就会触发该方法
    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {

        //随机的发送Student 或者 Workder 对象
        int random = new Random().nextInt(3);
        MyDataInfo.MyMessage myMessage = null;

        if(0 == random) { //发送Student 对象

            myMessage = MyDataInfo.MyMessage.newBuilder().setDataType(MyDataInfo.MyMessage.DataType.StudentType).setStudent(MyDataInfo.Student.newBuilder().setId(5).setName("玉麒麟 卢俊义").build()).build();
        } else { // 发送一个Worker 对象

            myMessage = MyDataInfo.MyMessage.newBuilder().setDataType(MyDataInfo.MyMessage.DataType.WorkerType).setWorker(MyDataInfo.Worker.newBuilder().setAge(20).setName("老李").build()).build();
        }

        ctx.writeAndFlush(myMessage);
    }

    //当通道有读取事件时，会触发
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {

        ByteBuf buf = (ByteBuf) msg;
        System.out.println("服务器回复的消息:" + buf.toString(CharsetUtil.UTF_8));
        System.out.println("服务器的地址： "+ ctx.channel().remoteAddress());
    }

    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        cause.printStackTrace();
        ctx.close();
    }
}

```

###### 		Student.proto

```
syntax = "proto3";
option optimize_for = SPEED; // 加快解析
option java_package="com.atguigu.netty.codec2";   //指定生成到哪个包下
option java_outer_classname="MyDataInfo"; // 外部类名, 文件名

//protobuf 可以使用message 管理其他的message
message MyMessage {

    //定义一个枚举类型
    enum DataType {
        StudentType = 0; //在proto3 要求enum的编号从0开始
        WorkerType = 1;
    }

    //用data_type 来标识传的是哪一个枚举类型
    DataType data_type = 1;

    //表示每次枚举类型最多只能出现其中的一个, 节省空间
    oneof dataBody {
        Student student = 2;
        Worker worker = 3;
    }

}


message Student {
    int32 id = 1;//Student类的属性
    string name = 2; //
}
message Worker {
    string name=1;
    int32 age=2;
}
```



#### 入站和出站

##### 	入站

​		对应的handler就是ChannelOutboundHandler

​		什么是入站？相对于客户端和服务器而言，入站就是信息从外面到客户端或服务器的内部，也就是数据从字节到其他复杂类型

​		入站对于handler的调用是正序的，handler的执行顺序是handler的添加顺序

##### 	出站

​		对应的handler就是ChannelInboundHandler

​		所谓出站，就是数据从客户端或服务器里面出去，也就是数据从复杂类型到字节

​		出站对于handler的调用是逆序的，handler的执行顺序是handler的添加顺序的逆序



##### 	自定义编解码器

###### 		Server

```
package netty.handlercallprocess;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.buffer.ByteBuf;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.ByteToMessageDecoder;

import java.nio.charset.Charset;
import java.util.List;

public class Server {

    public static void main(String[] args) {
        EventLoopGroup bossGroup = new NioEventLoopGroup(1);
        EventLoopGroup workerGroup = new NioEventLoopGroup();

        try {
            ServerBootstrap serverBootstrap = new ServerBootstrap();
            serverBootstrap.group(bossGroup, workerGroup);
            serverBootstrap.channel(NioServerSocketChannel.class);
            serverBootstrap.childHandler(new ChannelInitializer<SocketChannel>() {
                @Override
                protected void initChannel(SocketChannel ch) throws Exception {
                    ChannelPipeline pipeline = ch.pipeline();
                    pipeline.addLast(new ByteToLongDecoder());
                    pipeline.addLast(new ServerHandler());
                }
            });

            ChannelFuture channelFuture = serverBootstrap.bind(8888).sync();
            channelFuture.channel().closeFuture().sync();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }
}

/**
 * 这个是入站的解码器，将对面传输的数据由字节转换为其他类型
 */
class ByteToLongDecoder extends ByteToMessageDecoder {

    @Override
    protected void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) throws Exception {
        System.out.println(in.readableBytes());
        System.out.println(in.toString(Charset.forName("utf-8")));
        if (in.readableBytes() >= 8) {
            out.add(in.readLong());
        }
    }
}

class ServerHandler extends SimpleChannelInboundHandler<Long> {

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, Long msg) throws Exception {
        System.out.println("msg:" + msg);
    }
}

```



###### 		Client

```
package netty.handlercallprocess;

import io.netty.bootstrap.Bootstrap;
import io.netty.buffer.ByteBuf;
import io.netty.buffer.Unpooled;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.MessageToByteEncoder;

import java.nio.charset.Charset;

public class Client {

    public static void main(String[] args) throws InterruptedException {
        EventLoopGroup loopGroup = new NioEventLoopGroup();

        try {
            Bootstrap bootstrap = new Bootstrap();

            bootstrap.group(loopGroup)
                    .channel(NioSocketChannel.class)
                    .handler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline pipeline = ch.pipeline();
                            //增加一个自定义的编码器encoder
                            pipeline.addLast(new LongToByteEncoder());
                            //增加一个自定义处理器
                            pipeline.addLast(new ClientHandler());
                        }
                    });

            ChannelFuture channelFuture = bootstrap.connect("127.0.0.1", 8888).sync();
            channelFuture.channel().closeFuture().sync();
        } finally {
            loopGroup.shutdownGracefully();
        }

    }
}

class LongToByteEncoder extends MessageToByteEncoder<Long> {

    @Override
    protected void encode(ChannelHandlerContext ctx, Long msg, ByteBuf out) throws Exception {
        if (msg != null)
            out.writeLong(msg);
        System.out.println("编码了");
    }
}

class ClientHandler extends SimpleChannelInboundHandler<Long> {
    @Override
    protected void channelRead0(ChannelHandlerContext ctx, Long msg) throws Exception {

    }

    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
//        ctx.writeAndFlush(8888L);
        ctx.writeAndFlush(Unpooled.copiedBuffer("abcdabcdabcdabcd", Charset.forName("UTF-8")));
    }
}

```



#### TCP粘包拆包

​	当客户端连续给服务器发送多条数据时，会出现粘包或拆包现象

​	粘包

​		接收端只收到一个数据包，由于TCP是不会出现丢包的，所以这一个数据包中包含了发送端发送的两个数据包的信息，这种现象即为粘包。这种情况由于接收端不知道这两个数据包的界限，所以对于接收端来说很难处理

​	拆包

​		这种情况有两种表现形式，接收端收到了两个数据包，但是这两个数据包要么是不完整的，要么就是多出来一块，这种情况即发生了拆包和粘包。这两种情况如果不加特殊处理，对于接收端同样是不好处理的



解决办法，自定义协议，规定数据包的边界



```
粘包、拆包发生原因
发生TCP粘包或拆包有很多原因，现列出常见的几点，不全面

1、要发送的数据大于TCP发送缓冲区剩余空间大小，将会发生拆包。

2、待发送数据大于MSS（最大报文长度），TCP在传输前将进行拆包。

3、要发送的数据小于TCP发送缓冲区的大小，TCP将多次写入缓冲区的数据一次发送出去，将会发生粘包。

4、接收数据端的应用层没有及时读取接收缓冲区中的数据，将发生粘包。
```





##### Server

```
package netty.tcp.solution;

import io.netty.bootstrap.ServerBootstrap;
import io.netty.buffer.ByteBuf;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioServerSocketChannel;
import io.netty.handler.codec.ByteToMessageDecoder;

import java.util.List;

/**
 * TCP粘包拆包解决办法之服务器
 */
public class Server {

    public static void main(String[] args) throws Exception {
        EventLoopGroup bossGroup = new NioEventLoopGroup();
        EventLoopGroup workerGroup = new NioEventLoopGroup();

        try {
            ServerBootstrap serverBootstrap = new ServerBootstrap();
            serverBootstrap.group(bossGroup, workerGroup)
                    .channel(NioServerSocketChannel.class)
                    .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            System.out.println("有客户端来了");
                            ChannelPipeline pipeline = ch.pipeline();
                            //自定义message解码器
                            pipeline.addLast(new MyByteToMessageDecoder());
                            //自定义handler处理器
                            pipeline.addLast(new ServerHandler());
                        }
                    });

            ChannelFuture channelFuture = serverBootstrap.bind(8888).sync();
            channelFuture.channel().closeFuture().sync();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }

    }
}

class ServerHandler extends SimpleChannelInboundHandler<Message> {

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, Message msg) throws Exception {
        System.out.println(msg);
    }
}

class MyByteToMessageDecoder extends ByteToMessageDecoder {

    @Override
    protected void decode(ChannelHandlerContext ctx, ByteBuf in, List<Object> out) throws Exception {
        System.out.println("decoding");
        if (in.isReadable() && in.readableBytes() >= 4) {
            int len = in.readInt();
            if (in.readableBytes() < len) {
                System.out.println("服务器可读的字节数小于此包的长度，需要等待客户端再发数据");
                return;
            }
            byte[] content = new byte[len];
            in.readBytes(content, 0, len);
            Message message = new Message();
            message.setLen(len);
            message.setContent(content);
            out.add(message);
        }
    }
}

```



##### Client

```
package netty.tcp.solution;

import io.netty.bootstrap.Bootstrap;
import io.netty.buffer.ByteBuf;
import io.netty.channel.*;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.SocketChannel;
import io.netty.channel.socket.nio.NioSocketChannel;
import io.netty.handler.codec.MessageToByteEncoder;

public class Client {

    public static void main(String[] args) throws Exception {
        EventLoopGroup eventLoopGroup = new NioEventLoopGroup();
        try {
            Bootstrap bootstrap = new Bootstrap();
            bootstrap.group(eventLoopGroup)
                    .channel(NioSocketChannel.class)
                    .handler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            ChannelPipeline pipeline = ch.pipeline();
                            //自定义编码器
                            pipeline.addLast(new MyMessageToByteEncoder());
                            pipeline.addLast(new ClientHandler());
                        }
                    });

            ChannelFuture sync = bootstrap.connect("127.0.0.1", 8888).sync();
            sync.channel().closeFuture().sync();
        } finally {
            eventLoopGroup.shutdownGracefully();
        }
    }
}

class ClientHandler extends SimpleChannelInboundHandler<Message> {

    @Override
    /**
     * 连续发送10个数据给服务器，使用自定义协议
     */
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        for (int i = 0; i < 10; i++) {
            Message message = new Message();
            byte[] content = ("hello,server" + i).getBytes();
            int len = content.length;
            message.setContent(content);
            message.setLen(len);
            ctx.writeAndFlush(message);
        }
    }

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, Message msg) throws Exception {

    }
}

class MyMessageToByteEncoder extends MessageToByteEncoder<Message> {

    @Override
    protected void encode(ChannelHandlerContext ctx, Message msg, ByteBuf out) throws Exception {
        System.out.println("encoding");
        out.writeInt(msg.getLen());
        out.writeBytes(msg.getContent());
    }
}

```



##### Message

```
package netty.tcp.solution;

import java.util.Arrays;

public class Message {

    private int len;    //标志本包的内容长度
    private byte[] content; //本包的数据

    public int getLen() {
        return len;
    }

    public void setLen(int len) {
        this.len = len;
    }

    public byte[] getContent() {
        return content;
    }

    public void setContent(byte[] content) {
        this.content = content;
    }

    @Override
    public String toString() {
        return "Message{" +
                "len=" + len +
                ", content=" + Arrays.toString(content) +
                '}';
    }
}

```



### 源码分析

##### 	服务器启动流程

###### NioEventLoopGroup

```
	EventLoopGroup bossGroup = new NioEventLoopGroup(1);

	EventLoopGroup workerGroup = new NioEventLoopGroup();

	创建工作组，参数1表示此工作组下的EventLoop只有一个，每个EventLoop就表示一个单独的处理线程，不加参数表示默认EventLoop数量是CPU核数*2

```

![58087225773](H:\笔记\images\1580872257730.png)



![58087228147](H:\笔记\images\1580872281470.png)

![58087231091](H:\笔记\images\1580872310918.png)

```
Provider可以提供ServerSocketChannel，它是NIO对象，Netty底层就要调用此对象


```





![58087233975](H:\笔记\images\1580872339750.png)

![58087242031](H:\笔记\images\1580872420319.png)

![58087243381](H:\笔记\images\1580872433814.png)

```
nThreads == 0 ? DEFAULT_EVENT_LOOP_THREADS : nThreads		
判断传入的nthread数量，如果不等于0就按照自己传入的，如果等于0或者没传就用DEFAULT_EVENT_LOOP_THREADS
```

![58087251479](H:\笔记\images\1580872514793.png)

![58087257069](H:\笔记\images\1580872570690.png)

![58087261624](H:\笔记\images\1580872616243.png)

```
最后来到了上面的方法，因为太长，所以抽几个重要的地方
```

![58087298980](H:\笔记\images\1580872989801.png)

```
判断上面传入的executor是否为空，如果为空，new一个默认的
executor是一个线程池对象(可以这样考虑，只不过默认的线程池是单线程的)，用户可以自定义传入的executor
```



![58087266024](H:\笔记\images\1580872660241.png)

```
children是个数组，如下图，长度设置为了nThreads，也就是我们可以传入的线程数
```

![58087267871](H:\笔记\images\1580872678713.png)

![58087273162](H:\笔记\images\1580872731621.png)

```
上图对children数组进行了初始化，newChild方法如下
```

![58087279539](H:\笔记\images\1580872795397.png)

```
也就是说，EventLoopGroup里面有多个EventLoop，每个EventLoop为一个执行线程
```







###### ServerBootStrap

```
上面的初始化工作完成，接下来执行下面的方法
```

![58087321894](H:\笔记\images\1580873218943.png)



![58087324952](H:\笔记\images\1580873249529.png)

```
空构造，他爹也是空构造，但是初始化了很多类成员变量
childGroup是传入workerGroup
他爹的group是传入的bossGroup
childOptions是传入的TCP属性
```



```
上面的ServerBootstrap初始化完毕，他就是一个启动类，接下来看下面
```

![58087341304](H:\笔记\images\1580873413045.png)

```
一步一步讲解
```

![58087344060](H:\笔记\images\1580873440603.png)

![58087345737](H:\笔记\images\1580873457373.png)

![58087346917](H:\笔记\images\1580873469172.png)

```
b.group就是给启动类的childGroup属性和他爹的group属性赋值
```







![58087359145](H:\笔记\images\1580873591452.png)

![58087360927](H:\笔记\images\1580873609272.png)

![58087363229](H:\笔记\images\1580873632296.png)



```
上面的方法给channelFactory属性赋值，此属性用于反射创建传入的类对象，NioServerSocketChannel.class
```





![58087370180](H:\笔记\images\1580873701806.png)

![58087371711](H:\笔记\images\1580873717118.png)

![58087373375](H:\笔记\images\1580873733758.png)



```
.option方法简单，就是给一个hashMap集合添加值，就是TCP参数
```



![58087378029](H:\笔记\images\1580873780296.png)

![58087379675](H:\笔记\images\1580873796751.png)

```
AbstractBootstrap的方法，也就是ServerBootStrap的爹，给handler属性赋值
```



![58087389547](H:\笔记\images\1580873895478.png)



![58087390775](H:\笔记\images\1580873907759.png)



```
大同小异，ServerBootStrap的方法
```



###### bind

```
此方法是核心方法，有两个子核心方法，initAndRegister和dobind0
此方法用来注册channel至selector和给channel绑定端口，并开启一个无限循环程序来检测有无任务发生
```



![58087398161](H:\笔记\images\1580873981612.png)

![58087413312](H:\笔记\images\1580874133122.png)







![58087416125](H:\笔记\images\1580874161256.png)

