# kafka

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

