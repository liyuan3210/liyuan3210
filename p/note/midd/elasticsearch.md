# elasticsearch

官网下载
https://www.elastic.co/

### 一。docker 安装

```
安装elasticsearch(核心)：
1.拉取镜像
docker pull elasticsearch:7.13.1
2.运行镜像(单实例)
docker run -d -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.13.1

安装elasticsearch-analysis-ik(ik分词器),ik测试安装：
https://blog.csdn.net/qq_33612228/article/details/107563446

elasticsearch安装:
http://chenzhijun.me/2017/12/01/elasticsearch-install/
```

### 二。linux下安装

###### 1.elasticsearch安装

```
1.根据官网下载elasticsearch-7.10.2-linux-x86_64.tar.gz
    https://www.elastic.co/downloads/elasticsearch
    找到官网每个版本对应的jdk版本（？？？），7.10.2高版本jdk18不支持（？？？）
    $ tar -xzvf elasticsearch-7.10.2-linux-x86_64.tar.g//解压
问题(jdk问题)：
    Exception in thread “main” java.lang.UnsupportedOperationException: The Security Manager is deprecated and will be removed in a future release
    at java.base/java.lang.System.setSecurityManager(System.java:416)
    at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:82)
    更换jdk(JAVA_HOME)，查看官方版本与jdk对应表

2.启动
	跨域问题（否则插件无法访问）：
	跨域问题，在ES 安装目录的 conf 文件夹下配置config/elasticsearch.yml
        http.cors.enabled: true 
        http.cors.allow-origin: "*"
        network.host: 0.0.0.0
	2.1).本机单个项目启动多个节点（配置文件在命令行）
        ./elasticsearch -E path.data=data1 -E path.logs=log1 -E node.name=node1 -E cluster.name=cluster_es
        ./elasticsearch -E path.data=data2 -E path.logs=log2 -E node.name=node2 -E cluster.name=cluster_es
        ./elasticsearch -E path.data=data3 -E path.logs=log3 -E node.name=node3 -E cluster.name=cluster_es
        还要配置config/elasticsearch.yml:
        https://www.jianshu.com/p/ac5816776204
        cluster.initial_master_nodes: ["node1", "node2", "node3"]
	2.2).多个项目启动多个节点（生产推荐）
        ./elasticsearch	-d	//后台启动
        ./elasticsearch	-d	//后台启动
        ./elasticsearch -d 	//后台启动
        配置文件elasticsearch.yml
        cluster.name: elasticsearch-es		//集群名称,放开修改
        node.name:node1				//当前集群节点名字,放开
        network.host:192.168.1.1	//本机ip,放开修改
        http.port:9200				//客户端与客户端通信端口,放开

3.启动后浏览器访问
	http://127.0.0.1:9200

配置问题：
1。启动elasticsearch报错 [1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
	https://www.cnblogs.com/whyblogs/p/15089451.html
错误原因（没用）：
    elasticsearch用户拥有的内存权限太小，至少需要262144；
    (1)切换到root用户：
    su root
    (2)输入命令（临时生效）：
    sysctl -w vm.max_map_count=262144
    检查修改结果：
    sysctl -a|grep vm.max_map_count
    (3)修改 /etc/sysctl.conf文件(永久生效)
    vim /etc/sysctl.conf
    vm.max_map_count=262144
    (4)使其生效
    sysctl -p

2.配置jvm堆大小config/jvm.options(不能太小)
-Xms1g
-Xmx1g

3.[2023-03-31T13:17:31,982][WARN ][o.e.t.TcpTransport       ] [node3] exception caught on transport layer [Netty4TcpChannel{localAddress=/127.0.0.1:9302, remoteAddress=/127.0.0.1:45286}], closing connection
java.lang.IllegalStateException: transport not ready yet to handle incoming requests
	at org.elasticsearch.transport.TransportService.onRequestReceived(TransportService.java:952) ~[elasticsearch-7.10.2.jar:7.10.2]
	at org.elasticsearch.transport.InboundHandler.handleRequest(InboundHandler.java:164) ~[elasticsearch-7.10.2.jar:7.10.2]
	at org.elasticsearch.transport.InboundHandler.messageReceived(InboundHandler.java:107) ~[elasticsearch-7.10.2.jar:7.10.2]
	at org.elasticsearch.transport.InboundHandler.inboundMessage(InboundHandler.java:89) ~[elasticsearch-7.10.2.jar:7.10.2]
	at org.elasticsearch.transport.TcpTransport.inboundMessage(TcpTransport.java:700) [elasticsearch-7.10.2.jar:7.10.2]
	at org.elastics

-------------------------------------OLD
一。安装
单台机器启动多个实例
https://blog.csdn.net/jeffli1993/article/details/99893914

node1	node2	node3

1.创建es用户,不能以root用户安装启动

2.分发后配置elasticsearch.yml
cluster.name: liyuan-es		//集群名称,放开修改
node.name:node1				//当前集群节点名字,放开
network.host:192.168.1.1	//本机端口,放开修改
http.port:9200				//客户端与客户端通信端口,放开
防脑裂
discovery.zen.ping.multicast.enabled:false		//默认多播模式(有多少节点加几台,不知道节点数量情况时)
discovery.zen.ping.unicast.hosts:["192.168.1.1","192.168.1.2","192.168.1.3"]	//直接指定节点数量
discovery.zen.ping_timeout:120s
client.transport.ping_timeout:60s

3.scp分发,分发后修改如下节点配置项
node.name:nodex
network.host:192.168.1.x
```

###### 2.kibana安装

```
二。kibana安装
1.kibana解压安装启动
$ ./kibana 

默认配置config/kibana.yml文件：
#elasticsearch.hosts: ["http://localhost:9200"]	//默认

2.启动后浏览器访问
http://127.0.0.1:5601
右上角dev_tool工具
```

###### 3.head插件安装

```
三。head安装
1.首先解压安装nodejs，并安装grunt
$ npm install -g grunt-cli
$ grunt -version

2.下载head插件
https://github.com/mobz/elasticsearch-head

3.修改Gruntfile.js文件
connect: {
			server: {
				options: {
					hostname: '*',
					port: 9100,
					base: '.',
					keepalive: true
				}
			}
		}
4.运行
$ npm install 
$ npm run start

5.访问
http://127.0.0.1:9100
```



### 原理

```
elasticsearch是免费的,一些插件需要license
https://www.jianshu.com/p/5f5ca85e2a7e

下载
https://www.elastic.co/cn/downloads/elasticsearch

查看下载历史版本(点击"past releases")
https://www.elastic.co/cn/downloads/past-releases#elasticsearch

版本选择问题
https://www.jianshu.com/p/c5c3e834c028
https://blog.csdn.net/qq_16038125/article/details/88879294	//版本特性

一.原理
elasticsearch(底层实现原理)
↓
lucene(搜索关键字定位)
↓
倒排索引(拆词建立倒排索引)


二.正排索引 与 倒排索引:
	我是中国人（1）
	中国是全球人口最多的国家,中国人也最多（2）

	正排索引>直接拆词搜索定位 如下
		1:  我 ,  是,, 中国,中国人
		2:  中国,是,全球….

	倒排索引>如下
		1，我 （1:1）｛0｝
		2，中国 (1:1) {2},(2:2){0，15}
		
	为什么建立倒排索引后就查询速度快?
	因为字,单词数量是有限的,有限的单词组成无限的文章数据,通过有限的
单词索引建立查询定位


三.elasticsearch文档封装(快速定位查找)
按照elasticsearch格式形式提供文档
document文档 ：object
	content:是否保存,是否索引,是否分词
	size:同上
	path:c:/text.txt 
输入源:非文件:关系数据库中
↓
lucene处理
过滤,分词->建立倒排索引
↓
创建document并创建唯一标识符
id唯一
content:可以不保存(节约空间)
path:必须保存

四.架构
单lucene计算是有问题的,一般以集群工作
先评估硬件单节点lucene计算能力,再评估未来3年业务上升能力
再决定部署多少台机器.

承载数据方式（sharding碎片方式）,横向扩展,达到负载效果
每一个节点都是一个主节点,每一个节点都有要处理的数据

分布式分发原则(哈希取模,哈希是document的id,模是取机器数量)
随机派发一个节点为代理,代理向各个节点发送命令,
各个节点处理完后进行汇聚,汇聚后由代理进行统一返回结果。

每个节点处理能力不能相差太大(这个要对环境网络做测试)

底层做主从,主的做倒排索引与计算,从做数据备份,分担主查询压力

lucene片一旦确认,后面就改不了了

分lucene片问题?

第三方插件(copy到plugins目录下面)
中文分词验证
https://blog.csdn.net/feinifi/article/details/114079973
分词器
elasticsearch-analysis-ik-1.8.0:(倒排索引,中文分词器)
https://github.com/medcl/elasticsearch-analysis-ik
vi plugin-descriptor.properties
elasticsearch.version=x.x.x(对应elasticsearch版本)


elasticsearch-head：
https://github.com/mobz/elasticsearch-head
http://ip:9200/_plugin/head/


database(数据库)	index(索引库)
table(表)			type(类型)
row(行)				document(文档)
column(列)			field(字段)

六.RESTAPI
创建库,表,新增数据可以通过REST接口进行访问操作

七.项目demo

组件使用
https://blog.csdn.net/qq_31289187/article/details/84843044
https://github.com/elasticjob/elastic-job-lite

elasticsearch-sql客户端
https://github.com/NLPchina/elasticsearch-sql
---------------------------
ElasticSearch架构

elasticsearch基于lucene（全文检索工具包）框架实现

倒排索引组成：
1.单词词典（term dictionary）
	基于b+tree实现，用来保存分词（ik中文分词）后的单词列表，关联文档id
2.倒排列表（posting list）
	记录了文档id,位置（单词在文档位置），偏移量






```

