# netty

```
官网：
http://netty.io/

B站netty课程
https://www.bilibili.com/video/av33707223/?p=3

马士兵IO模型与netty
https://www.bilibili.com/video/av53759412?from=search&seid=8156417420041097818

netty5.x用到了jdk1.8后的forkJoin线程特性,由于性能表现不是很特殊,
分支维护困难问题,在master上删除了5.x分支

example源码实例
解压..\jar\all-in-one\netty-all-4.1.16.Final-sources.jar打开example目录

·google RPC框架 protocol-buffers (夸语言的),类似java里面的rmi
stub 
skeleton 骨架
执行流程：
对象序列化(服务端)
↓
网络传输
↓
反序列化对象(客户端)
↓
客户端执行

·apache的thrift RPC框架 类似(protocol-buffers)夸语言的


linux网络IO模型
1.阻塞IO模型
2.非阻塞IO模型
3.IO复用模型
4.信号驱动IO模型
5.异步IO模型


通信模型
1.BIO模型
一个客户端请求服务端创建一个线程

2.伪异步IO模型?
ByteBuffer 封装很繁琐

3.NIO编程(JDK1.4后)
一个(单线程)或多个(reactor线程池)管理轮询(询问),服务端连接并管理连接读写

4.AIO编程(JDK1.7)
一个(单线程)或多个(reactor线程池)异步模式(有连接os操作系统会主动告诉)，
使用了观察者模式(回调函数),服务端连接并管理连接读写

有AIO为什么还会有NIO?
·在netty里面甚至没有用AIO,用的是NIO.
·AIO,NIO在linux底层都是用的epoll实现,
很多情况AIO多了一层轮询封装(epoll本身是),
所以netty使用NIO封装
·AIO与netty模型一样
·windows AIO是自己单独实现的,win使用AIO会比linux效率高
·netty只关心linux实现,NIO实现
```

