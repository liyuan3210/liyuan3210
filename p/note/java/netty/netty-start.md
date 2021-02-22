## 三.Netty模型介绍

## Reactor模式(线程模型)

1）单Reactor单线程

2）单Reactor多线程

3）主从Reactor多线程（netty从此模型优化）

AIO使用了Proactor模式，对比Proactor与Reactor模式？？？

https://blog.csdn.net/KingCat666/article/details/77685608

## 1.模型介绍

![](img/netty-3.1.png)

channel与pipeline是相互包含关系

## 2.simple实例（根据简单实例分析）

netty.simple包下面

## 3.taskqueue

当请求执行的任务时间比较长事就需要放到线程队列里面，保证后面请求能够顺利执行不阻塞（因为为netty是基于nio,nio是单线程基于事件循环的）

三种task典型使用场景：

１）用户程序自定义的普通任务（把线程放到workGroup task任务队列里面）
２）用户自定义定时任务
３）非当前Reactor线程调用Channel各种方法？？？

## 4.异步FutureListener机制/channelHandler类

4.1>future与channelFuture

4.2>channelHandler及其实现类？？？(出站，入站)

4.3>pipeline和channelPipleline

## 5.服务程序http入门（过滤指定资源）

浏览器每一次请求刷新产生不同的pipeline

```
ch.pipeline().addLast(new XXXHandler());
ch.pipeline().addFirst(new XXXHandler());
区别？？？=
```

pipeline 和　channelPipeline???

EventLoop组件

实例：netty.http

### 6.unpooled类buffer使用???

类似NIO中的ByteBuffer，但有区别。

实例：netty.buf

### 7.群聊系统实现.(思考点对点私聊实现???)

实例：netty.groupchat

### 8.心跳机制实例

当出现程序不可预料的连接断开时，需要心跳检测客户端是否还保持链接(并做相应业务处理)

MyServer启动后需要启动一个客户端连接验证(本例使用groupchat/GroupChatClient.java)

实例：netty.heartbeat

### 9.websocket

http协议升级websocket协议

实例：netty.websocket

### 10. google protoBuff编码解码(效率高解决跨语言问题)

**编码与解码：**

客户端（业务数据--->编码）===二进制码===>服务端（解码--->业务数据）

**netty自身提供编码与解码器，但会出现如下问题：**

*无法跨语言  *序列化性能太低 *序列化体积太大(是二进制五倍多)

引出方案protoBuff与使用流程：

编写.proto文件===>使用命令生成java文件(相关语言的类文件)

生成java文件命令：protoc.exe --java_out=. Student.proto

**实例：**netty.codec，netty.codec2

### 11.入站与出站机制

**编码与解码：**
数据发送需要编码，数据接收时需要解码



**出站与入站：**
数据出站与入站handler,相对客户端程序/服务端程序，对应不同ChannelPipeline,原理都如下：
当服务器有数据流向客户端称为入站，当客户端有数据流向服务端称出站：
1.channelInboundHandler(入站)
2.channelOutboundHandler(出站)
通常在OutboundHandler里面是编码器，InboundHandler里面是解码器

**客户端程序/服务端程序流程：**

![](img/encoder_decoder_process.png)



**注意：**

发送出站数据类型必须与实际处理接受的数据类型一致，否则会跳过处理



**常用编解码器：**

根据官方api文档查看常用编解码器有哪些，怎么使用？？？



**添加log4j支持：**

（方便查看调试日志）



**实例：**netty.inboundhandlerandoutboundhandler

```
实例要求:  
使用自定义的编码器和解码器来说明Netty的handler 调用机制
客户端发送long -> 服务器
服务端发送long -> 客户端
```



### 12.tcp粘包拆包解决方案

**为什么要有粘包，拆包：**
由于不可能知道远程节点是否会一次性发送一个完整信息，就会出现粘包，拆包。
这个类会对入站数据进行缓冲，直到它准备好被处理

实例：

1.演示tcp粘包，拆包（问题演示），netty.tcp

读取的信息很随机

2.粘包，拆包解决方案：netty.protocoltcp

```
TCP 粘包和拆包解决方案
*.使用自定义协议 + 编解码器 来解决
*.关键就是要解决 服务器端每次读取数据长度的问题, 这个问题解决，就不会出现服务器多读或少读数据的问题，从而避免的TCP 粘包、拆包 。

看一个具体的实例:
*.要求客户端发送 5 个 Message 对象, 客户端每次发送一个 Message 对象
*.服务器端每次接收一个Message, 分5次进行解码， 每读取到 一个Message , 会回复一个Message 对象 给客户端.
```

