# hadoop介绍

## 一．基础

```
HDFS分布式存储
	1.对大文件线性切割成相同大小的块(block),分散在集群中.
	2.block可以设置备份数,副本数不要超过节点数(默认block 128Mb,存1MB数据,只占用1Mb空间,不会占用128MB)
	3.block是可以调整的
	4.已上传的block副本可以调整,大小不能调整
	5.可以append追加数据
	
	副本放置策略:
		第一个副本:放置在上传文件的DN;如果是集群外提交,
		则随机挑选一台磁盘不太满,cpu不太忙的节点
		第二个副本:放置在于第一个副本不同的机架的节点上
		第三个副本:与第二个副本相同机架的节点
		更多副本:随机节点
	
	架构模型：
		文件元数据MetaData，文件数据	
		.元数据
		.数据本身
		（主）NameNode节点保存文件元数据：单节点   posix
		（从）DataNode节点保存文件Block数据：多节点
		DataNode与NameNode保持心跳，提交Block列表
		HdfsClient与NameNode交互元数据信息
		HdfsClient与DataNode交互文件Block数据(cs)
		DataNode利用服务器本地文件系统存储数据块
----------------------------------------------------------------------------------------------------
mapreduce1.png
	block > split
	1:1
	N:1
	1:N
	↓
	split > map(默认一个block对应一个map,人为可以切片很小)
	1:1
	↓
	map > reduce(map是reduce输入)
	N:1
	N:N
	1:1
	1:N
	↓
	group(key) > parttion
	1:1
	N:1
	N:N
	1:N>违背原语
	↓
	parttion > outputfile

	分区算法:
		使用hash+取模算法。key,value分区。有3个reduce，算出分区parttion0,parttion1,parttion2

mapreduce2.png


简介
hadoop1.x.png

client做的事情:
	1.split切片,计算切片清单
	2.取出切片位置信息,位置信息判断验证

MRv1角色：
	JobTracker
		核心，主，单点
		调度所有的作业
		监控整个集群的资源负载
	TaskTracker
		从，自身节点资源管理
		和JobTracker心跳，汇报资源，获取Task
	Client
		作业为单位
		规划作业计算分布
		提交作业资源到HDFS
		最终提交作业到JobTracker
	弊端：
		JobTracker：负载过重，单点故障
		资源管理与计算调度强耦合，其他计算框架需要重复实现资源管理
		不同框架对资源不能全局管理



YARN
yarn.png
	ResourceManager:集群资源管理
	NodeManager：节点资源管理
	
	客户端1请求RM,RM为客户 客户端1 随机为客户端1创建一个活动的appMaster应用程序调度(类似job Tracker)，
客户端1appMaster向RM申请资源,申请到资源后由appMaster进行调度。


MRv2：On YARN
YARN：解耦资源与计算
	ResourceManager
		主，核心
		集群节点资源管理
	NodeManager
		与RM汇报资源
		管理Container生命周期
		计算框架中的角色都以Container表示
	Container：【节点NM，CPU,MEM,I/O大小，启动命令】
		默认NodeManager启动线程监控Container大小，超出申请资源额度，kill
		支持Linux内核的Cgroup
MR ：
	MR-ApplicationMaster-Container
	作业为单位，避免单点故障，负载到不同的节点
	创建Task需要和RM申请资源（Container）
	Task-Container
Client：
	RM-Client：请求资源创建AM
	AM-Client：与AM交互

ResourceManager做HA



MapReduce编程模型
↓
java,ruby,python(支持多语言编写业务处理)
↓
YARN(集群资源管理系统)
↓
Interactive SQL(交互式SQL)	Interactive processing(迭代处理)	Stream processing(流处理)	Search(搜索)

关系型数据库和Mapreduce

网格计算:适应计算密集型作业,通常因为带宽瓶颈不得不闲下来等数据
志愿计算:适应计算时间远超单位数据传输时间

hadoop相关开源项目
数据格式:avro,parquet
数据摄取:flume,sqoop
数据处理:pig,Hive,crunch,spark
存储:Hbase
协作:zookeeper

----------------------------------------------------------------------------------------------------

Hadoop通常处理少量大型文件更容易.如果有大量小文件,先需要经过预处理(将多个文件拼接成一个单独的文件)

传统处理按行存储数据工具是awk

map准备数据, reduce处理数据

编写驱动运行hadoop程序


横向扩展
1. 数据流
job一个执行单元
↓
MapReduce程序(输入数据,配置信息)
↓
分成若干任务(每个任务对应一个map与reduce)
↓
YARN(集群调度)


1.1 分片处理(在HDFS存储数据块的节点上运行map任务)
HDFS默认块大小128MB(如果分片太小,那么管理分片的总时间和构建map任务总时间将决定整个执行时间)
在存储分片的节点上运行map可获得最佳性能,因为可以节省宝贵宽带资源

map任务将其输出写入本地硬盘,而非HDFS,因为map输出是中间结果(作业一旦完成即可删除),
该中间结果由reduce任务处理后才产生最终输出结果。如果把map存储到HDFS会进行备份及占用网络资源

reduce不具备本地化优势,单个reduce通常来自所有mapper输出,reduce输出通常存储在HDFS中,可实现可靠存储

1.2 combiner函数
尽量避免map和reduce任务传递

1.3 运行分布式mapReduce作业

1.4 Hadoop streaming
Hadoop提供MapReduce的API,允许使用非java的其他语言编写map和reduce.
hadoop streaming使用unix标准流作为hadoop和应用程序之间的接口


分布式框架比较
批处理框架 hadoop,yarn (分钟级别)
微批处理框架 spark,stream (秒级别) 家族框架(协同作战),稳定性改进中
实时流式处理框架 strorm (毫秒级别),tiwitter开源比较独立,已经很稳定


hadoop是否过时？？？
https://blog.csdn.net/weixin_34119545/article/details/86056746
http://www.kuqin.com/database/20120715/322528.html
----------------------------------------------------------------------------------------------------
1.hdfs设计

数据块:默认128Mb,加入存入1MB数据,实际只占用1Mb空间,不会占用128MB

namenode与datanode
namenode:维护文件系统整棵树内所有的文件和目录,管理多个datanode
datanode:工作节点,管理文件系统命名空间
理论上一旦namenode损坏,所有数据都将丢失

高可用机制
1.nfs实时备份
2.运行一个辅助namenode

namenode在内存中保存文件系统中每个文件和每个数据块的引用关系,
这意味着在超大集群文件系统来说内存将限制系统横向扩展的瓶颈

故障切换与规避


2.命令接口
从本地文件系统将一个文件复制到HDFS
hadoop fs -copyFromLocal file hdfs://localhost/user/tom/file

3.文件系统
不同方式的访问
webhdfs
ftp
azure
...

4.java接口访问
从hadoop url读取数据
写入数据
查询数据
删除数据

5.数据流
HDFS读取open
客户端找到namenode
↓
namenode返回数据块datanode地址给客户端
↓
客户端通过datanode地址直接读取数据块信息
↓
最后完成文件读取


HDFS写入create
客户端请求namenode创建新文件
↓
namenode返回客户端datanode写入信息
↓
客户端根据datanode信息开始写入数据
↓
完成一个数据块写入后(datanode会以管线异步方式复制副本)
↓
完成写入


6.一致模型
HDFS为了性能考虑牺牲了POSIX要求
hflush()后写入的数据均达到datanode,对所有新的reader均可见,
但不保证已将数据写入到磁盘(只保存在datanode内存,如果数据中心断电,可能会出现数据丢失)

hsync()能够保证写入到磁盘(性能开销大)

上面两个方法应该根据实际情况进行选择


7.通过distcp并行复制
常见使用实例是在两个HDFS集群间传送数据

----------------------------------------------------------------------------------------------------

YARN
hadoop2支持YARN,分布式计算框架,支持其他的分布式计算模式,很少直接用于用户代码,如下层次结构

aplication(MapReduce,spark,tez,...)
↓
YARN
↓
HDFS and HBase


两类服务：
resource manager(资源管理器,管理node manager)
↓
node manager(节点管理器)


yarn 与 MapReduce1相比有更高的性能及扩展
MapReduce1:40000(这个节点后性能会出现瓶颈)
yarn:100000(这个节点后性能会出现瓶颈)


yarn调度
FIFO调度,容量调度,公平调度

----------------------------------------------------------------------------------------------------

IO操作

HDFS的数据完整性
？

压缩
？

序列化
？

----------------------------------------------------------------------------------------------------
开发部分
...

----------------------------------------------------------------------------------------------------

hadoop BOOK
https://github.com/tomwhite/hadoop-book/tree/master/input/ncdc/all
```

