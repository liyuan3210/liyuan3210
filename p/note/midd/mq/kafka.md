# kafka

## 1.kafka架构图

![](img/kafka-1.png)

## 2.kafka版本及包：

1.kafka_2.11-0.11.0.2.tgz	2.11表示scala版本，11.0.2是kafka版本

2.kafka-manageer-1.3.3.15 与　kafkaOffsetMontor-assemtbly-0.46

配置：config/server.properties

启动：bin/kafka-server-start.sh -daemon config/server.properties

## 3.基础命令：

1.查看当前所有topic

bin/kafka-topics.sh --zookeeper ip:2128 --list

2.创建topic

bin/kafka-topics.sh --zookeeper ip:2128 --create --topic first --partitions 3 --replication-factor 2

3.删除topic

bin/kafka-topics.sh --zookeeper ip:2128  --delete --topic first

4.查看topic详情

bin/kafka-topics.sh --zookeeper ip:2128 --describe --topic first

查看partitiong 与　replication还有 Isr???

ＩＳＲ　：和Leader保持同步的副本集合

5.发送消息

bin/kafka-console-producer.sh --broker-list ip:9092（可以有多个） --topic first

6.消费消息

bin/kafka-console-consumer.sh  --topic first --bootstrap-server  --broker-list ip:9092   --from-beginning(可无，从头消费)

7.修改分区数(partitions只能递增不能递减)

bin/kafka-topics.sh --zookeeper ip:2128 --alter --topic first --partitions 6

## 4.运行流程及存储机制：

![](img/kafka-2.png)

![](img/kafka-3.png)

## 5.分区：

分区原因：方便集群扩展，提高并发
![](img/kafka-2.png)![](img/kafka-4.png)

## 6.可靠性和ISR：

![](img/kafka-5.png)

![](img/kafka-6.png)

ACKS:

​			acks=0:broker一旦收到消息就返回ack（有丢数据风险）

​			acks=1:leader落盘成功后返回ack（有丢数据风险）

​			acks=-1:等待leader,follower都落盘成功后返回ack（有数据重复，在所有同步完成后，在返回ack之前leader发生故障，会产生数据重复问题）

LEO,HW:

​				为了保证leader与follower数据同步(不管数据缺少与重复)，当leader挂掉后，重新选举到leader后，follower同步时会截取ＨＷ后面的数据

Exactly once(保证数据的唯一性):

​			生产者加上enable.idempotence属性设置为true(kafka会自动设置acks为－１)



```
http://kafka.apache.org
下载kafka_2.12-0.11.0.1.tgz解压安装

把config文件夹copy到bin/windows下面

启动zookeeper
zookeeper-server-start config/zookeeper.properties

启动kafka
kafka-server-start config/server.properties
使用默认的zookeeper不需要任何修改.
如果要使用指定的zookeeper,需要修改server.properties文件的zookeeper.connect属性(指定好ip:port)


创建topic(貌似会报错)
kafka-topics --create --zookeeper localhost:2181 --replication-factor 1 --partition 1 --topic test
查看topic(貌似会报错)
kafka-topics --list --zookeeper localhost:2128


如果不适用kafka-topics来创建,直接使用下面的内容进行消息创建时也会自动创建topic(使用下面的命令控制台发送,接收消息)

创建消息生产者(输入字符消息)：
kafka-console-producer --broker-list localhost:9092 --topic test

创建消息消费者(显示接受的消息):
kafka-console-consumer --zookeeper localhost:2181 --topic test --from-beginning

查看topic
kafka-topics --list --zookeeper localhost:2181


kafka教程
https://www.w3cschool.cn/apache_kafka/apache_kafka_quick_guide.html
```

