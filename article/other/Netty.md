# Netty

<!-- TOC -->

- [Netty](#netty)
  - [参考站点](#%e5%8f%82%e8%80%83%e7%ab%99%e7%82%b9)
  - [业务线程，io线程如何使用](#%e4%b8%9a%e5%8a%a1%e7%ba%bf%e7%a8%8bio%e7%ba%bf%e7%a8%8b%e5%a6%82%e4%bd%95%e4%bd%bf%e7%94%a8)
  - [理解nio线程模型](#%e7%90%86%e8%a7%a3nio%e7%ba%bf%e7%a8%8b%e6%a8%a1%e5%9e%8b)
  - [LengthFieldBasedFrameDecoder](#lengthfieldbasedframedecoder)
  - [《Netty权威指南（第二版）》 作者：李林峰 2015.4](#netty%e6%9d%83%e5%a8%81%e6%8c%87%e5%8d%97%e7%ac%ac%e4%ba%8c%e7%89%88-%e4%bd%9c%e8%80%85%e6%9d%8e%e6%9e%97%e5%b3%b0-20154)
    - [第一部分](#%e7%ac%ac%e4%b8%80%e9%83%a8%e5%88%86)
    - [第二部分 （netty 开发）](#%e7%ac%ac%e4%ba%8c%e9%83%a8%e5%88%86-netty-%e5%bc%80%e5%8f%91)
  - [4种网络IO模型](#4%e7%a7%8d%e7%bd%91%e7%bb%9cio%e6%a8%a1%e5%9e%8b)
    - [epoll与IOCP](#epoll%e4%b8%8eiocp)
    - [Reactor模式 vs. Preactor模式](#reactor%e6%a8%a1%e5%bc%8f-vs-preactor%e6%a8%a1%e5%bc%8f)

<!-- /TOC -->


## 参考站点


- [源码之下无秘密 ── 做最好的 Netty 源码分析教程](https://segmentfault.com/a/1190000007282628)
- [Netty系列之Netty线程模型](http://www.infoq.com/cn/articles/netty-threading-model#mainLogin)
- [《Netty in Action》中文版—目录](http://ifeve.com/netty-in-action/)
 

## 业务线程，io线程如何使用

**时间可控的简单业务直接在IO线程上处理**

如果业务非常简单，执行时间非常短，不需要与外部网元交互、访问数据库和磁盘，不需要等待其它资源，则建议直接在业务ChannelHandler中执行，不需要再启业务的线程或者线程池。避免线程上下文切换，也不存在线程并发问题。

**复杂和时间不可控业务建议投递到后端业务线程池统一处理**

对于此类业务，不建议直接在业务ChannelHandler中启动线程或者线程池处理，建议将不同的业务统一封装成Task，统一投递到后端的业务线程池中进行处理。

过多的业务ChannelHandler会带来开发效率和可维护性问题，不要把Netty当作业务容器，对于大多数复杂的业务产品，仍然需要集成或者开发自己的业务容器，做好和Netty的架构分层。


**业务线程避免直接操作ChannelHandler**


对于ChannelHandler，IO线程和业务线程都可能会操作，因为业务通常是多线程模型，这样就会存在多线程操作ChannelHandler。为了尽量避免多线程并发问题，建议按照Netty自身的做法，通过将操作封装成独立的Task由NioEventLoop统一执行，而不是业务线程直接操作，相关代码如下所示：


``` java
private void ex(ChannelHandlerContext ctx){

    if (ctx.executor().inEventLoop()) {
        try {
            doFlush(ctx);
        }catch (Exception e){
            logger.error("异常：",e);
        }
    } else {
        ctx.executor().execute(() -> {
            try {
                doFlush(ctx);
            }catch (Exception e){
                logger.error("异常：",e);
            }
        });
    }
}
```

如果你确认并发访问的数据或者并发操作是安全的，则无需多此一举，这个需要根据具体的业务场景进行判断，灵活处理。


## 理解nio线程模型

**用编辑器打断点的方式测试netty的reactor线程模式是否为nio非阻塞**

第一个客户端访问的时候在服务端的handle中打断点，然后放开断点，接着第二个客户端和第三个客户端访问服务端就阻塞了，这个跟主从Reactor的线程模型是冲突的，其实该冲突是编辑器打断点的问题，第一个客户端连上后在服务端打好断点截住，如果一直不释放，即使把这个断点取消，第二个客户端在相同的位置还是会阻塞，第三个客户端也会阻塞的。


目前测试看来，是否阻塞跟自定义的编码器解码器没有关系。


**如何测试netty的reactor线程模式是否为nio非阻塞**

用三个客户端去访问服务端，服务端的handle根据客户端的消息自主判断进行阻塞，比如当客户端发送的消息中包含`1001`字符串的时候进行阻塞，让线程休眠，代码如下所示

``` java
@Override
	public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
		logger.info(Thread.currentThread().getId()+"收到的消息"+msg);
		boolean boo;
		logger.info("msg 中是否包含1001字符串：{}",boo=msg.toString().contains("1001"));
		if (boo) {
			logger.info("阻塞包含字符串1001的线程 ...");
			Thread.sleep(Long.MAX_VALUE);
		}
		logger.info("服务器-消息写出");
		ChannelFuture future = ctx.writeAndFlush("{\"header\":{\"msgType\":\"/getToken\"}}"); 
	}
```

客户端去访问服务端的时候需要两种（多种也可以，根据服务端的自主判断规则定义）不同的消息访问服务端，第一种包含`1001`字符串消息，第二种不包含`1001`字符串消息，代码如下所示

``` java
@Test
    public void test1001() throws IOException, InterruptedException{
        YiyiMessage msg = new YiyiMessage();
        YiyiHeader head = new YiyiHeader();
        head.setMsgType(ReqRespStatus.MsgType.GET_TOKEN.value());
        msg.setHeader(head);
        msg.setMsg("nio-test-1001");


        Client client=new Client();
        client.start();
        Thread.sleep(5000);
        System.out.println("aaa");
        RunConsole.sendYiyiMsg(client.getHandler(), msg);
        Thread.sleep(Long.MAX_VALUE);
    }

```

``` java
@Test
    public void test1002() throws IOException, InterruptedException{
        YiyiMessage msg = new YiyiMessage();
        YiyiHeader head = new YiyiHeader();
        head.setMsgType(ReqRespStatus.MsgType.GET_TOKEN.value());
        msg.setHeader(head);
        msg.setMsg("nio-test-1002");


        Client client=new Client();
        client.start();
        Thread.sleep(5000);

        System.out.println(client.getHandler());
        RunConsole.sendYiyiMsg(client.getHandler(), msg);
        Thread.sleep(Long.MAX_VALUE);
    }
```


**如果netty的工作线程数为2，那接收进来消息是如何分配给具体的线程，规则是怎样的**

``` java
EventLoopGroup workerGroup = new NioEventLoopGroup(2);
```

按以上方法多次测试的结果来看，是 **轮询匹配**，也就是顺序匹配，具体是否有其他规则需要详细查阅相关资料，所以先放一放。



## LengthFieldBasedFrameDecoder

- {0,0,0,0,0,0,0,0},8个字节，从右往左位数依次递增，右边第一个为最低位，左边第一个为最高位。
- {0,0,0,0,0,1,0,0} = 65536 ，使用两个Byte字节代表消息长度，则最大长度需要小于65536个字节。   
- {0,0,0,0,1,0,0,0} = 16777216 ， 使用三个Byte字节代表消息长度，则最大长度需要小于16777216个字节。
- {0,0,0,1,0,0,0,0} = 4294967296 ， 使用四个Byte字节代表消息长度，则最大长度需要小于4294967296个字节。


> 65536字节(b)=64千字节(kb)   
    16777216字节(b)=16兆字节(mb)  
    2147483647字节(b)=2千兆字节(gb)  
    4294967296字节(b)=4千兆字节(gb)  



## 《Netty权威指南（第二版）》 作者：李林峰  2015.4

### 第一部分

- 1. 同步阻塞式I/IO    BIO
- 2. 伪异步I/O  通过线程池处理多个客户端的请求接入，可以避免线程资源耗尽的问题。
- 3. NIO编程 
- 3.1 缓冲区 Buffer
- 3.2 通道 Channel （它就像自来水管一样，它是双向的）
- 3.3 多路复用器 Selector
- 4. AIO 编程
说明：NIO 2.0 引入了新的异步通道的概念，并提供了异步文件通道和异步套件字通道的实现。


### 第二部分 （netty 开发）

1. netty 解决TCP 粘包/拆包 导致的半包读写问题

1.1 lineBasedFrameDecoder + StringDecoder   以换行符为结束标志的解码器 （"\n"或者"\r\n"）
1.2 DelimiterBasedFrameDecoder 以分隔符作为码流的结束标示的消息编码
1.3 FixedLengthFrameDecoder 固定长度解码器，能够按照指定的长度对消息进行自动解码
1.4.1 LengthFieldPrepender 编码器之前加，将在ByteBuf之前增加2（可配置）个字节的消息长度字段
1.4.2 LengthFieldBasedFrameDecoder 解码器之前加，接收的永远是整包

2. netty 私有协议栈开发

1.1 客户端
1.1.1 LoginAuthReqHandler 
1.1.2 HeartBeatReqHandler

1.2 服务端
1.2.1 LoginAuthRespHandler


## 4种网络IO模型


### epoll与IOCP

在《Unix环境高级编程》中介绍了以下4种IO模型（实际不止4种，但常用的就这4种）：

- 阻塞IO： read/write的时候，阻塞调用
- 非阻塞IO： read/write，没有数据，立马返回，轮询
- IO复用：read/write一次都只能监听一个socket，但对于服务器来讲，有成千上完个socket连接，如何用一个函数，可以监听所有的socket上面的读写事件呢？这就是IO复用模型，对应linux上面，就是select/poll/epoll3种技术。
- 异步IO：linux上没有，windows上对应的是IOCP。

### Reactor模式 vs. Preactor模式

相信很多人都听说过网络IO的2种设计模式，关于这2种模式的具体阐述，可以自行google之。

在此处，只想对这2种模式做一个“最通俗的解释“：

Reactor模式：主动模式，所谓主动，是指应用程序不断去轮询，问操作系统，IO是否就绪。Linux下的select/poll/epooll就属于主动模式，需要应用程序中有个循环，一直去poll。
在这种模式下，实际的IO操作还是应用程序做的。

Proactor模式：被动模式，你把read/write全部交给操作系统，实际的IO操作由操作系统完成，完成之后，再callback你的应用程序。Windows下的IOCP就属于这种模式，再比如C++ Boost中的Asio库，就是典型的Proactor模式。