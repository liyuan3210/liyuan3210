# hadoop2.x安装



```
hadoop解决的问题
	1.HDFS分布式文件系统
	2.分布式计算
	3.hbase分布式数据库
官网：
http://hadoop.apache.org/

官方文档：
http://hadoop.apache.org/docs/r2.8.5/

环境配置安装:
http://www.cnblogs.com/wuxun1997/p/6847950.html

2.官网下载解压hadoop
	sbin:启动或停止hadoop服务的脚本
	bin:对hadoop相关服务(HDFS,YARN)进行操作的脚本
	etc:hadoop的配置文件目录
	share:hadoopd的依赖jar包和文档，文档可以被删掉的
	lib:Hadoop本地库.对数据进行压缩解压缩功能的
```

## 一．环境配置

```
1.java,hadoop环境配置(/etc/profile)
	export JAVA_HOME=/home/liyuan/soft/jre1.8.0_162
	export HADOOP_HOME=/home/liyuan/soft/hadoop-3.1.2
	export PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH    
	export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$CLASSPATH

	命令生效:source /etc/profile
	
	查看hadoop版本命令
	hadoop version
	
	2.配置节点hosts
	vi /etc/hosts
	192.168.122.90 node1
	192.168.122.22 node2
	192.168.122.24 node3
	192.168.122.23 node4

	3.ssh免密登录
	生成免密登录密钥对
		ssh-keygen -t rsa -P ""
	配置/etc/ssh/sshd_config
		AuthorizedKeysFile		%h/.ssh/authorized_keys //注释拿掉
	生成authorized_keys文件(.ssh目录的权限必须是700,authorized_keys文件权限必须是600)
		cat ../.ssh/id_rsa.pub >> ../.ssh/authorized_keys
	最后把.ssh目录分发到各个节点
	
	注意
	a>.上面生成配置好后,就可以复制虚拟机节点,启动后就可以相互免密登录了
	b>.可以通过scp向其它节点发送文件
	
	实例搭建
	https://www.cnblogs.com/mymelody/p/9441177.html
```

## 二．伪分布式安装

```
https://hadoop.apache.org/docs/r2.8.5/hadoop-project-dist/hadoop-common/SingleCluster.html
	进入../hadoop/etc/hadoop目录
	1>hadoop-env.sh
		有可能访问不到,最好写本地路径如下形式:
		export JAVA_HOME=/home/liyuan/soft/jre1.8.0_162
		
	2>core-site.xml
		很多配置都默认在底层包里面，core-site.xml用于用户自定义配置
		<configuration>
			<!-- 配置hdfs的namenode(老大)地址 -->
			<property>
				<name>fs.defaultFS</name>
				<value>hdfs://node1:9000</value>
			</property>
			//hadoop.tmp.dir问题(hdfs-site里面,在namenode下面),所以要改变目录(自动创建hdtmp目录)
			<property>
				<name>hadoop.tmp.dir</name>
				<value>/home/liyuan/soft/hd2</value>
			</property>
		</configuration>
		
	3>hdfs-site.xml
		<configuration>
			<!-- 副本存几份 -->
			<property>
				<name>dfs.replication</name>
				<value>1</value>
			</property>
			<property>		//secondary文件合并工作
				<name>dfs.namenode.secondary.http-address</name>	
				<value>node1:50090</value>
			</property>
		</configuration>
		
	格式化(首次配置启动)	
	hdfs namenode -format
	
	启动hdfs
	start-dfs.sh
	
	停止
	stop-dfs.sh
	
	浏览器访问默认端口(HDFS)50070
	http://localhost:50070
```

## 三．全分布式安装

```
环境
							NN		SNN		DN
	node1(主)				X
	node2(secondary)				X		X
	node3									X
	node4									X
	
	配置
		hadoop-env.sh不变
		core-site.xml不变
		hdfs-site.xml
			dfs.replication	共3台机器,副本可以设置为2
			dfs.namenode.secondary.http-address	可以设置secondary为66机器(非namenode节点)
			其它配置(非必配置项目)
				1>namenode,datanode路径指定
					dfs.namenode.name.dir 指定 NameNode 数据存放路径；
					dfs.datanode.data.dir 指定 DataNode 数据存放路径；
					注意，要提前创建好/home/hadoop/data/dfs 文件夹)
					<property>
						<name>dfs.namenode.name.dir</name>
						<value>/home/liyuan/namenode</value>
					</property>
					<property>
						<name>dfs.datanode.data.dir</name>
						<value>/home/liyuan/datanode</value>
					</property>
				2>如果在/etc/hosts(ip 别名)配置,就不需要下面的配置
					<property>		
						<name>dfs.namenode.datanode.registration.ip-hostname-check</name>
						<value>false</value>
					</property>
			
	slave配置(也可以配置node1上去)
		node2
		node3
		node4
		
	分发
		使用scp命令
			scp -r /home/liyuan/soft/hadoop-3.1.2  liyuan@node2:/home/liyuan/soft/

	启动
		格式化(头一次启动时)
		bin/hdfs namenode -format

		启动
		sbin/start-dfs.sh

		停止
		sbin/stop-dfs.sh

		浏览器访问默认端口(HDFS)50070
		http://localhost:50070

	命令
	hdfs dfs -mkdir /tool	//上传文件
	hdfs dfs -put hadoop-2.8.5.tar.gz /tool

	问题：
	1.DisallowedDatanodeException: Datanode
	https://blog.csdn.net/qqpy789/article/details/78189335

	2.ip变动问题(ubuntu18静态Ip设置)

	3.(core-site.xml)配置hadoop.http.staticuser.user 值配置为 liyuan
	?
```

## 四．HA配置

```
环境
				NN-1		NN-2		DN		ZK		ZKFC		JNN
	node1(主)	X										X			X
	node2(备)				X			X		X		X			X
	node3								X		X					X	
	node4								X		X	
	
	解释:
		zk: zookeeper(可以单独部署)
		ZKFC: 必须与namenode在一起,健康检查namenode(active)
		JNN: 存储共享日志信息(集群存在,信息同步)

	QJM(NFS已过时)
	
	hadoop-env.sh(不用改变)
	
	core-site.xml修改
		<configuration>
			<property>
				<name>fs.defaultFS</name>
				<value>hdfs://mycluster2</value>
			</property>
			<property>
				<name>hadoop.tmp.dir</name>
				<value>/home/liyuan/soft/ha2</value>
			</property>
			<property>
				<name>ha.zookeeper.quorum</name>
				<value>node2:2181,node3:2181,node4:2181</value>
			</property>
		</configuration>
	
	hdfs-site.xml修改
		<configuration>
			<!-- 把这个secondary配置去掉
				<property>		//secondary文件合并工作
					<name>dfs.namenode.secondary.http-address</name>	
					<value>node2:50090</value>
				</property>
			-->
			<property>
				<name>dfs.nameservices</name>
				<value>mycluster2</value>
			</property>
			<property>
				<name>dfs.ha.namenodes.mycluster2</name>
				<value>nn1,nn2</value>
			</property>
			<property>
				<name>dfs.namenode.rpc-address.mycluster2.nn1</name>
				<value>node1:8020</value>
			</property>
			<property>
				<name>dfs.namenode.rpc-address.mycluster2.nn2</name>
				<value>node2:8020</value>
			</property>
			
			<property>
				<name>dfs.namenode.http-address.mycluster2.nn1</name>
				<value>node1:50070</value>
			</property>
			<property>
				<name>dfs.namenode.http-address.mycluster2.nn2</name>
				<value>node2:50070</value>
			</property>
			
			<property>
				<name>dfs.namenode.shared.edits.dir</name>
				<value>qjournal://node1:8485;node2:8485;node3:8485/mycluster2</value>
			</property>
			
			<property>
				<name>dfs.client.failover.proxy.provider.mycluster2</name>
				<value>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider</value>
			</property>
			
			//namenode1由于网络原因触发zk事件namenode2变为active,当namenode1网络恢复会有两个namenode
			//首先会强制切换另外一个为standby,然后提升备选namenode
			//切换状态时候需要一个隔离方法,远程控制对法机器变为standby,所以需要指定id_rsa
			<property>
				<name>dfs.ha.fencing.methods</name>
				<value>sshfence</value>
			</property>
			<property>
				<name>dfs.ha.fencing.ssh.private-key-files</name>
				<value>/home/liyuan/.ssh/id_rsa</value>
			</property>
			
			<property>	//Journal edits日志存储路径
				<name>dfs.journalnode.edits.dir</name>
				<value>/home/liyuan/soft/ha2/journalnode</value>
			</property>
			
			<property>	//自动故障转移
				<name>dfs.ha.automatic-failover.enabled</name>
				<value>true</value>
			</property>
		</configuration>
	
	分发
		scp core-site.xml hdfs-site.xml liyuan@node2:`pwd`
		scp core-site.xml hdfs-site.xml liyuan@node3:`pwd`
		scp core-site.xml hdfs-site.xml liyuan@node4:`pwd`
		
	HA格式化
		两种
			1.之前有过格式化元数据信息,最简单同步执行(备节点执行)
			hdfs namenode -initializeSharedEdits
			2.重新格式化一个namenode节点(当前使用的)
			hdfs namenode -bootstrapStandby
			
	1.启动journalnode(每个节点node1,node2,node3手动启动)
		hadoop-daemon.sh start journalnode
	2.node1格式化(主节点任意选择一台格式化)
		hdfs namenode -format
		查看集群id
		cat soft/ha3/dfs/name/current/V*
	3.node1执行启动namenode
	  hadoop-daemon.sh start namenode
	  也可以使用hdfs --daemon start
	4.node2执行同步
	  hdfs namenode -bootstrapStandby
		再查看集群Id(与node1相同)
		cat soft/ha3/dfs/name/current/V*
	5.node1格式化zk,创建注册目录(前提让zk集群启动起来)
	  hdfs zkfc -formatZK
	6.启动集群(再次启动hdfs集群启动此命令)
		start-dfs.sh(包含启动两个namenode)
	7.测试
	干掉node1(active状态的节点),观测切换情况
	hadoop-daemon.sh stop namenode
	启动node1
	hadoop-daemon.sh start namenode
	干掉node2 zkfc
	hdfs --daemon stop zkfc
	
	
问题:
	1.namenode.secondary过时了？
```

## 五．YARN配置HA

```
环境
				NN-1		NN-2		DN		ZK		ZKFC		JNN			RS			NM
	node1(主)	X										X			X
	node2(备)				X			X		X		X			X						X
	node3								X		X					X			X			X
	node4								X		X								X			X
	
	必须配置的(不管是单点，集群，HA都需要配置的)
		mapred-site.xml修改(可以配置本机):
			<configuration>
				<property>
					<name>mapreduce.framework.name</name>
					<value>yarn</value>
				</property>
			</configuration>
		yarn-site.xml修改(拉取数据服务):
			<configuration>
				<property>
					<name>yarn.nodemanager.aux-services</name>
					<value>mapreduce_shuffle</value>
				</property>
			</configuration>
	HA文档：
		http://hadoop.apache.org/docs/r2.8.5/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html
	HA配置：
		yarn-site.xml修改:
			<property>
			  <name>yarn.resourcemanager.ha.enabled</name>
			  <value>true</value>
			</property>
			<property>
			  <name>yarn.resourcemanager.cluster-id</name>
			  <value>cluster1</value>
			</property>
			<property>
			  <name>yarn.resourcemanager.ha.rm-ids</name>
			  <value>rm1,rm2</value>
			</property>
			<property>
			  <name>yarn.resourcemanager.hostname.rm1</name>
			  <value>node3</value>
			</property>
			<property>
			  <name>yarn.resourcemanager.hostname.rm2</name>
			  <value>node4</value>
			</property>
			<property>
			  <name>yarn.resourcemanager.webapp.address.rm1</name>
			  <value>node3:8088</value>
			</property>
			<property>
			  <name>yarn.resourcemanager.webapp.address.rm2</name>
			  <value>node4:8088</value>
			</property>
			<property>
			  <name>yarn.resourcemanager.zk-address</name>
			  <value>node2:2181,node3:2181,node4:2181</value>
			</property>
	分发:
		scp mapred-site.xml yarn-site.xml liyuan@node2:`pwd`
		scp mapred-site.xml yarn-site.xml liyuan@node3:`pwd`
		scp mapred-site.xml yarn-site.xml liyuan@node4:`pwd`
		
	复用slaves文件
	
	启动：
		在node1执行启动(再次启动yarn集群启动此命令)
			start-yarn.sh(包含启动两个resourcemanager)	//启动yarn
		在node3,node4分别执行启动resourcemanager
			yarn-daemon.sh start resourcemanager
			
	测试YARN的HA
		在node3或node4执行下面命令,查看active状态切换
			yarn-daemon.sh stop resourcemanager	
			
	查看yarn浏览器:
	http://192.168.157.168:8088
	
	测试hadoop例子程序
		cd ../share/hadoop/mapreduce
		运行
		hadoop jar hadoop-mapreduce-examples-xxxxx.jar wordcount  /hdfs文件(数据目录或文件) /root/不存在或空的目录
		
		hadoop jar hadoop-mapreduce-examples-2.8.5.jar wordcount /data /root/不存在或空的目录
```

## 六．dev

```
20190808环境启动步骤:
		1.在hosts配置ip对应的节点node,并scp分发
		2.首先要启动zk服务node2,node3,node4
		3.分布式启动hdfs
				start-dfs.sh(包含启动namenode)
		4.分布式启动yarn
				start-yarn.sh
	20190808hadoop目录结构(soft目录下面):
		hd2(hadoop2.x HDFS文件目录)
		hd3(hadoop3.x HDFS文件目录)
		ha2(hadoop2.x yarn计算文件目录)
		ha3(hadoop3.x yarn计算文件目录)
		
----------------------------------------------------------------------------------------------------			
	4>.mapred-site.xml(副本mapred-site.xml.template可配可不配)
		<configuration>
			<!-- 指定mapreduce编程模型运行在YARM上 -->
			<property>
				<name>mapreduce.framework.name</name>
				<value>yarn</value>
			</property>
		</configuration>
		
	5.yarn-site.xml
		<configuration>
			<!-- 指定YARM的老大(resourcemanager的地址) -->
			<property>
			<name>yarn.resourcemanager.hostname</name>
			<value>localhost</value>
			</property>
			<!-- mapreduce执行shuffle时获取数据的方式 -->
			<property>
			<name>yarn.nodemanager.aux-services</name>
			<value>mapreduce_shuffle</value>
			</property>
			<!-- 
			<property>
			<name>yarn.nodemanager.aux-services</name>
			<value>mapreduce_shuffle</value>
			</property>
			<property>
			<name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
			<value>org.apache.hadoop.mapred.ShuffleHandler</value>
			</property>
			 -->
		</configuration>

5.对HDFS进行初始化(格式化HDFS)
		bin目录下执行
		hdfs namenode -format
	
6.启动服务(两种方式)
		//jps命令
		1.一般不建议这样启动
		start-all.sh 启动所有服务(HDFS,YARN)
		
		2.分别执行下面两条命令
		start-dfs.sh//运行时需要输入ssl密码(可以配置成免密码形式 )
		
		浏览器访问默认端口(HDFS)50070
		http://localhost:50070
		
		start-yarn.sh
		浏览器访问默认端口(YARN)8088
		http://localhost:8088
		
		3.查看后台进程是否启动成功
		jps查看后台进程:
		
		NodeManager
		ResourceManager
		SecondaryNameNode
		DataNode
		NameNode

windows下安装
http://blog.csdn.net/n1007530194/article/details/49473667


ubuntu
http://blog.csdn.net/monkeys2012/article/details/51972622
```
