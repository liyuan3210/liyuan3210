# netty

```
课程:
https://www.bilibili.com/video/BV1DJ411m7NR?from=search&seid=17920255675398502421
资源链接: https://pan.baidu.com/s/1yMAtTG36W3ekVO_zqDnkew 提取码: his7
百度云盘：liyuandf账号:云网盘>Course>java>【Java】Netty,韩顺平(全116讲)
代码：https://github.com/liyuan3210/java/tree/master/netty/netty_base_atguigu
原始资料：https://gitee.com/liyuan3210/book_source/netty/netty_atguigu
```

## 一.内容

### 1.bio,nio,aio介绍（三种io方式）

##### 1）.BIO模型

同步并阻塞(传统阻塞型),一个连接服务端创建一个线程处理

##### 2）.java NIO模型(JDK1.4后提供nio)

同步非阻塞，服务模式为一个线程处理多个请求（连接），客户端请求都会注册到多路复用器上，多路复用轮询到连接有IO请求就进行处理

```
一个(单线程)或多个(reactor线程池)管理轮询(询问),服务端连接并管理连接读写
```

NIO三大核心组件：

1>.Selector（１个选择器）

2>.Channel（多个通道）

3>.Buffer（多个缓冲区）

##### 3）.java AIO模型(没流行起来)

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

##### 三种io方式:

1.FileOutputStream	基础普通io

2.BufferedOutputStream  buffer文件IO

3.FileChannel nio文件io（NIO文件读取）

##### BIO模型实例：

```
com.liyuan3210.netty.bio.BIOServer			
```

### 2.nio入门(NIO核心三大组件Buffer,Channel,Selector)

<img src="img/nio-2.1.png" style="zoom: 50%;" />

##### 1）.Buffer(缓冲区)

NIO程序－＞Buffer缓冲区－＞文件

​	数据存储在hb数组里面

四个重要属性：

​	Capacity	容量

​	Limit	限制读取长度

​	Position	下一个要读取的位置

​	Mark	标记

程序是直接操作Buffer的，不能直接操作文件

**实例**：

1.BasicBuffer.java	//intbuffer读写数据

**java包**：java.nio

##### 2）.Channel（通道）

可以想成一个连接

**实例：**

1.本地文件写数据	//NIOFileChannel01.java

2.本地文件读数据	//NIOFileChannel02.java

3.使用一个buffer完成文件读取 	//NIOFileChannel03.java

4.使用transferFrom完成文件拷贝	//NIOFileChannel04.java

5.类型化（读出的类型必须按照写入的类型来读出），只读（只能读取不能写入否则报异常） //NIOByteBufferPutGet.java

6.创建只读buffer   //ReadOnlyBuffer.java

6.ＭappedByteBuffer（可以让文件直接在内存中修改（堆外内存），操作系统不需要拷贝一次，同步到文件由ＮＩＯ完成）？？？//MappedByteBufferTest.java

7.scattering 和gathering,读，写创建buffer数组（分散和聚合）？？？	//ScatteringAndGatheringTest.java

**java包**：java.channel

**常用的channel有**：

FileChannel，DatagramChannel，ServerSocketChannel与SocketChannel

##### 3）.Selector（选择器）

能够检测多个通道上是否有事件发生，多个Channel注册到Selector，如果Channel有事件发生，便获取事件对事件进行处理

**实例：**

1.聊天系统	//**NIOServer.java与NIOClient.java（简单nio）**

2.聊天系统	//**groupchat包下（NIO实现聊天工具）**

3.零拷贝案例分析  //zerocopy包下

java零拷贝有mmap(内存映射)，sendFile

*mmap适合小数据量读写，sendFile适合大文件传输

*mmap需要4次上下文切换，3次数据拷贝，sendFile需要3次上下文切换,最少2次数据拷贝

*sendFIle可以利用DMA方式，减少cpu拷贝，mmap则不能(必须从内核拷贝到socket缓冲区)

零拷贝实例：com.liyuan3210.netty.nio.zerocopy目录下（传统方式与零拷贝比较）

### [3.netty入门](netty-start.md)(netty核心及类关系)

## 二.IM即时聊天工具

```
聊天工具
一。V1.0需求功能：
	1.能注册/注销用户（考虑后期扩展三方登录...）
	2.能添加好友，删除好友
	3.能与好友点对点聊天
	4.能新建/删除群
	5.能进行群聊
	* 6.考虑后期扩展能分享动态（文字，图片，视频）

二。技术架构
	前端（架构待定）：
		vue,nodejs...
	后端：
		spring boot + netty
	中间件：
		zookeeper,redis
	数据库：
		mysql
	1）client客户端(不同设备，应用场景提供不同的API)
		websocket,java客户端,...
	2）server端
		*一期版本规划完成能简单加好友，能实现点对点聊天，还能新建群进行群聊基本功能。
		*重点是整个系统服务端能横向扩展，系统功能稳定
		*实现tcp/udp,websocket协议
		

三。参考文献
	IM架构参考推荐：
	https://blog.csdn.net/liaojiamin0102/article/details/129442399

	netty服务端集群参考教程
	https://www.bilibili.com/video/BV1ca411m713

	IM系统社群
	http://www.52im.net/

	netty集群解决方案
	https://zhuanlan.zhihu.com/p/458892150
	http://www.52im.net/thread-3816-1-1.html

	web视频聊天
	https://www.cnblogs.com/components/p/15330756.html

	开源代码
	https://github.com/nicoliuli/chat			//服务端
	https://github.com/JackJiang2011/MobileIMSDK		//客户端

	集群netty需要解决的问题
	1.zk监听通知机制解决nettyserver挂了问题
	2.点对点在不同nettyserver处理方法
	3.给不在线好友发消息，好友怎么读到消息（线存到数据库，用户上线时服务端查询一次）
	
四。数据结构
	1.用户表user
	2.用户与好友关系表
	3.用户群表
		群名称，群创建者
	4.群与用户关系表
		群名称，用户id（成员），角色(0普通用户,1群创建者,2:管理员)
		
	5.用户与好友消息表
		发送者，接收者，消息内容，是否已读（独立的功能）
		
	6.群聊消息表
		群名称，发送者，消息内容，
		
	7.群聊消息成员已读状态表（独立的功能）
		群聊消息id,用户,是否已读,是否发送者,
```



