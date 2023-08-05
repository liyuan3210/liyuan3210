# netty

```
课程:
https://www.bilibili.com/video/BV1DJ411m7NR?from=search&seid=17920255675398502421
资源链接: https://pan.baidu.com/s/1yMAtTG36W3ekVO_zqDnkew 提取码: his7
百度云盘：liyuandf账号:共享资源>course>java>【Java】Netty,韩顺平(全116讲)
代码：https://github.com/liyuan3210/java/tree/master/netty/netty_base_atguigu
原始资料：https://gitee.com/liyuan3210/book_source/netty/netty_atguigu
```

## 一.章节

[1.bio,nio,aio介绍](bio-nio-aio.md)

三种io方式

[2.nio入门](nio.md)

buffer	channel	selector梳理？？？

[3.netty入门](netty-start.md)

netty核心及类关系？？？

## IM即时聊天工具

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



