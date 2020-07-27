# rocketmq

##　一．安装

2m-2s-async:2主2从异步

2m-2s-sync:2主2从同步

2m-noslave:2主没有从

 ## 二．架构

### 消息模式：

1.DefaultMQPushConsumer (push)

2.DefaultMQPullConsumer (pull)

rocketmq push与pull???

### 消息类型：

1.普通消息

2.顺序消息（实现有序消费）

3.事物消息

4.订阅消息(消费端)

## 三．存储

![](img/rocketmq-1.png)

高效存储：使用顺序读写，系统内核零拷贝技术

nameServer	,	producer	,	consumer 是无状态的(可以动态添加)

​	master(可写，可读)，slaver（读取），

刷盘方式（这里指发送消息的方式，还有主从复制又分为同步与异步）：

​	异步：producer发送消息给broker,不管落盘,返回结果给poducer（有数据丢失情况）技术技术技术

​	同步：producer发送消息给broker,落盘master才返回给producer

主从复制(分同步，异步)

​	异步：通常指master节点同步数据到slave节点的方式

​	同步：通常指master节点同步数据到slave节点的方式

​	当master挂掉后为了保证消费者完整消费slave数据，需要做同步主从复制

通常生产建议异步刷盘，同步复制

![](img/rocketmq-2.png)

通常有三个文件:

commitLog

consumerQueue（可以通过commitLog文件恢复）

indexFIle	



```
官网:http://rocketmq.apache.org/

安装实例
https://www.jianshu.com/p/4a275e779afa

配置环境变量
ROCKETMQ_HOME	D:\soft\rocketmq

启动NAMESERVER(进入bin目录)
start mqnamesrv.cmd

启动BROKER
start mqbroker.cmd -n 127.0.0.1:9876 autoCreateTopicEnable=true
注意：
打开runbroker.cmd，然后将‘%CLASSPATH%’加上英文双引号。
set "JAVA_OPT=%JAVA_OPT% -cp %CLASSPATH%"//这一行技术
保存并重新执行start语句


插件部署
https://github.com/apache/rocketmq-externals.git
下载完后rocketmq-externals\rocketmq-console\src\main\resources\application.properties
修改两项:
server.port=8081	//插件工程端口
rocketmq.config.namesrvAddr=127.0.0.1:9876		//此处mq地址

编译
进入‘\rocketmq-externals\rocketmq-console’文件夹，执行‘mvn clean package -Dmaven.test.skip=true’，编译生成

启动
java -jar rocketmq-console-ng-1.0.0.jar

浏览器访问
http://127.0.0.1:8081
```
