# rocketmq

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
set "JAVA_OPT=%JAVA_OPT% -cp %CLASSPATH%"//这一行
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

