# zookeeper

```
1.解压apache-zookeeper-3.6.2-bin.tar.gz到/opt目录
tar -xzvf apache-zookeeper-3.6.2-bin.tar.gz

2.配置环境变量
export ZOOKEEPER_HOME=/opt/apache-zookeeper-3.6.2-bin
export PATH $ZOOKEEPER_HOME/bin:$PATH

3.根据模板创建zoo.cfg文件
cp zoo_sample.cfg zoo.cfg

4.需要配置vi /etc/hosts
192.168.1.135 test1
192.168.1.155 test2
192.168.1.98 test3

5.配置zoo.cfg
dataDir=/opt/zookeeper/data/zkdata
dataLogDir=/opt/zookeeper/data/zklog
server.1=test1:2888:3888
server.2=test2:2888:3888
server.3=test3:2888:3888

6.创建数据与日志目录
mkdir /opt/zookeeper/data&&mkdir /opt/zookeeper/data/zkdata&&mkdir /opt/zookeeper/data/zklog

7.scp分发包
scp /etc/hosts root@test2:/etc/hosts						//各个节点分发hosts包
scp /etc/profile root@test2:/etc/profile					//各个节点分发环境变量
scp -r /opt/zookeeper root@test1:/opt/		//分发zk安装包

8.各个节点创建myid
echo 1 > /opt/zookeeper/data/zkdata/myid	//echo值各个节点不能一样，要与server.1配置文件一致

9.启动
	zkServer.sh start
	zkServer.sh stop
	
	zookpper本机启动多实例
	zkServer.sh　start ../conf/zoo1.cfg
	验证
	zkServer.sh status	//查看主从
```

## 一．安装

1.windows安装

```
http://zookeeper.apache.org/
解压zookeeper-3.4.10.tar.gz安装

新建zookeeper目录
我们现在配置3台机器,分别为3台机器在svn目录下新建z1,z2,z3目录并在里面新建
数据(dataDir),日志(dataLogDir)目录.

新建myid
分别在3台机器dataDir目录下新建myid文件(内容为实例.cfg配置文件里面server.后面的数字)

配置修改zoo.cfg
机器1(zoo1.cfg):
tickTime=2000
initLimit=10
syncLimit=5
dataDir=D:\\svn\\z1\\dataDir
dataLogDir=D:\\svn\\z1\\dataLogDir
clientPort=2181
server.1=localhost:2287:3377
server.2=localhost:2288:3378
server.3=localhost:2289:3379

机器2(zoo2.cfg):
tickTime=2000
initLimit=10
syncLimit=5
dataDir=D:\\svn\\z2\\dataDir
dataLogDir=D:\\svn\\z2\\dataLogDir
clientPort=2182
server.1=localhost:2287:3377
server.2=localhost:2288:3378
server.3=localhost:2289:3379

机器3(zoo3.cfg):
tickTime=2000
initLimit=10
syncLimit=5
dataDir=D:\\svn\\z3\\dataDir
dataLogDir=D:\\svn\\z3\\dataLogDir
clientPort=2183
server.1=localhost:2287:3377
server.2=localhost:2288:3378
server.3=localhost:2289:3379

配置解释
server.A=B:C:D	其中 A 是一个数字，表示这个是第几号服务器；B 是这个服务器的 ip 地址；
C 表示的是这个服务器与集群中的 Leader 服务器交换信息的端口；D 表示的是万一集群中的 Leader 服务器挂了，
需要一个端口来重新进行选举，选出一个新的 Leader，而这个端口就是用来执行选举时服务器相互通信的端口。
如果是伪集群的配置方式，由于 B 都是一样，所以不同的 Zookeeper 实例通信端口号不能一样，所以要给它们分配不同的端口号。

配置启动实例
拷贝重命名bin目录下zkServer.cmd文件,分别配置修改下面三个实例
zkServer1.cmd
zkServer2.cmd
zkServer3.cmd

zkServer修改内容:
set ZOOMAIN=org.apache.zookeeper.server.quorum.QuorumPeerMain
set ZOOCFG=..\conf\zoo1.cfg		//(添加此行内容,指向不同机器配置文件)
echo on
//默认会读zoo.cnf配置文件

启动实例:
分别运行
zkServer1.cmd
zkServer2.cmd
zkServer3.cmd

注意:
挂掉一半就无法工作了

客户端登录
zkCli.cmd -server 127.0.0.1:2181
```

2.linux安装

```
解压配置环境变量
	 
	ZOOKEEPER_HOME=/home/liyuan/soft/zookeeper-3.4.13
	PATH $ZOOKEEPER_HOME/bin:$PATH

	source /etc/profile

	配置conf
	zoo_sample.cfg为zoo.cfg
	cp zoo_sample.cfg zoo.cfg

	修改(指定目录)
	dataDir=/home/liyuan/soft/zk
	dataLogDir=/home/liyuan/soft/zklog
	server.1=node2:2888:3888
	server.2=node3:2888:3888
	server.3=node4:2888:3888

	分发
	scp -r /home/liyuan/soft/zookeeper-3.4.13 liyuan@node3:/home/liyuan/soft/
	scp -r /home/liyuan/soft/zookeeper-3.4.13 liyuan@node4:/home/liyuan/soft/

	分别在三台机器/home/liyuan/soft/zk目录中创建myid文件并输入id内容值
	(每台机器值对应server.值)
	echo 1 > /home/liyuan/soft/zk/myid

	启动
	zkServer.sh start
	zkServer.sh stop
	
	zookpper本机启动多实例
	zkServer.sh　start ../conf/zoo1.cfg

	验证
	zkServer.sh status	//查看主从
```

## 三．问题

```
二.zookeeper客户端命令,watches事件


三.应用场景
1.集群管理
2.统一命名服务
3.分布式配置管理
4.分布式消息队列
5.分布式锁
6.分布式通知协调等

实例
https://www.cnblogs.com/dennisit/p/4340746.html

官方实例
https://www.cnblogs.com/haippy/archive/2012/07/20/2600077.html

永久回掉
http://www.xuebuyuan.com/912071.html

实例
https://blog.csdn.net/itbuluoge/article/details/46943249

CountDownLatch写法


项目导入jar包(压缩包):
zookeeper-3.4.10.jar
jline-0.9.94.jar
log4j-1.2.16.jar
netty-3.10.5.Final.jar
slf4j-api-1.6.1.jar
slf4j-log4j12-1.6.1.jar

四.客户端命令
查看leader
zkServer.cmd status

登录
zkCli.cmd -server 127.0.0.1:2181

显示(列出项)
ls /

创建znode
create /zk_key "zk_value"		//持久
create -s /seq "zk_value"		//顺序
create -e /zk_key "zk_value"	//临时
create /zk_key/dir "zk_key dir"		//创建子项

移除znode
rmr /zk_key	//移除包含的子项

获取数据
get /zk_key
get /seq0000000001				//访问顺序必须输入完整路径

监视watch
get /zk_key 1

设置数据
set /zk_key "set data"

检查状态
stat /zk_key					//状态描述指定的znode的元数据。它包含时间戳，版本号，ACL，数据长度和子znode等细项。


高并发开发
synchronizer/同步器/ThreadPool,executor


cap理论(分布式理论)
Consistency	一致性
Availability 服务可用性
partition Tolerance 服务可扩展性
三者只能得到两样

paxos算法(分布式基石)

搜索引擎
Elasticsearch
Logstash
Kibana
beats

神经网络
imagenet //一个比赛
tensorflow//google神经网络库

算法类别
神经网络NN(neural network)
KNN(k个nearest neighbors)
```