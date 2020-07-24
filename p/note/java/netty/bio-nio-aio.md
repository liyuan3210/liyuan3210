# 1.BIO,NIO,AIO模型

## 1.BIO模型

同步并阻塞(传统阻塞型),一个连接服务端创建一个线程处理

## 2.java NIO模型(JDK1.4后提供nio)

同步非阻塞，服务模式为一个线程处理多个请求（连接），客户端请求都会注册到多路复用器上，多路复用轮询到连接有IO请求就进行处理

```
一个(单线程)或多个(reactor线程池)管理轮询(询问),服务端连接并管理连接读写
```

NIO三大核心组件：

SERVER(服务端)

Thread（1个线程）

Selector（１个选择器）

Channel（多个通道）

Buffer（多个缓冲区）

## 3.java AIO模型(没流行起来)

异步非阻塞，采用了Proactor模式，简化了程序编写，有效的请求才启动线程，特点是先由操作系统完成后才通知服务端程序启动线程去处理，一般适用于连接数较多且连接时间较长的应用1

```
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

## 三种io方式:

1.FileOutputStream	基础普通io

2.BufferedOutputStream  buffer文件IO

3.FileChannel nio文件io（NIO文件读取）

