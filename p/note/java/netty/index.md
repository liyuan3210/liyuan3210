# netty

课程

https://www.bilibili.com/video/BV1DJ411m7NR?from=search&seid=17920255675398502421

资源

链接: https://pan.baidu.com/s/1yMAtTG36W3ekVO_zqDnkew 提取码: his7

## 一．概述

[old笔记](netty.md)

### 1.BIO,NIO,AIO模型

#### 1.1）BIO模型

同步并阻塞(传统阻塞型),一个连接服务端创建一个线程处理

#### 1.2）java NIO模型(JDK1.4后提供nio)

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

##### 1.3）java AIO模型(没流行起来)

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

### 2.NIO核心三大组件Buffer,Channel,Selector

#### 2.1）Buffer(缓冲区)

NIO程序－＞Buffer缓冲区－＞文件

四个重要属性：

​	Capacity	容量

​	Limit	限制读取长度

​	Position	下一个要读取的位置

​	Mark	标记

程序是直接操作Buffer的，不能直接操作文件

实例：

1.简单Buffer操作

#### 2.2）Channel（通道）

可以想成一个连接

实例：

1.本地文件写数据

2.本地文件读数据

3.使用一个buffer完成文件读取

4.使用transferFrom完成文件拷贝

5.类型化（读出的类型必须按照写入的类型来读出），只读（只能读取不能写入否则报异常）

6.ＭappedByteBuffer（可以让文件直接在内存中修改，同步到文件由ＮＩＯ完成）

7.scattering 和gathering（分散和聚合）？？？

#### 2.3）Selector（选择器）



