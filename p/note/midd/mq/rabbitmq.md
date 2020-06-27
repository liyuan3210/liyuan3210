# rabbitmq

```
http://www.erlang.org
下载安装(运行环境):
otp_win64_20.1.exe
配置环境变量:
ERLANG_HOME:D:\soft\java\erlang\erl9.1
PATH:;%ERLANG_HOME%\bin		//只安装rabbitmq貌似可以不加

http://www.rabbitmq.com
下载rabbitmq-server-windows-3.6.12.zip解压安装
配置环境变量(spring cloud实践过程中并没有配置此环境变量):
RABBITMQ_BASE:D:\soft\java\rabbitmq_server-3.6.12
PATH:;%RABBITMQ_BASE%\sbin	//貌似可以不加,但每次运行服务需要到根目录下执行

cd进入sbin目录
启动rabbitmq服务:
rabbitmq-server.bat

启动web管理插件
rabbitmq-plugins.bat enable rabbitmq_management

web访问:
http://localhost:15672
账户:guest
密码:guest
guest默认账户,远程不能登录

命令添加xxx用户
rabbitmqctl add_user xxx pwd

设置用户角色:
rabbitmqctl set_user_tags {username} {tag ...}
Tag可以为 administrator,monitoring,management

命令改密码:
rabbimqctl change_password {username} {newpassword}


hello word
http://blog.csdn.net/lmj623565791/article/details/37607165
http://zyjustin9.iteye.com/blog/2030790

实例
http://blog.csdn.net/u012592062/article/details/51910955
原理介绍
https://www.cnblogs.com/diegodu/p/4971586.html

spring 整合 rabbitmq
https://www.cnblogs.com/s648667069/p/6401463.html

spring boot 整合 rabbitmq
http://blog.csdn.net/liaokailin/article/details/49559571


https://www.cnblogs.com/boshen-hzb/p/6841982.html

spring boot 整合 1.5.3 direct模式
http://blog.csdn.net/u010753907/article/details/72902755

原理：


交换器的规则有：
direct （直连） 默认模式
topic （主题） 匹配队列
headers （标题）用的比较少
fanout （分发）分发。
RPC	(微服务RPC通讯)
```

