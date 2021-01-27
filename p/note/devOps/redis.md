# redis

## 一．安装

1.windows安装

```
官网:
https://redis.io
可以下载源码make安装

wondows下载
https://github.com/microsoftarchive/redis
>redis on windows>release page
>Redis-x64-3.2.100.zip
解压安装

启动(默认端口6379)
redis-server redis.windows.conf

安装服务
redis-server --service-install redis.windows-service.conf --loglevel verbose
命令行执行完后
>
服务列表会新增一个Redis名字的服务
详细命令查看Windows Service Documentation文档

连接命令
redis-cli -h 127.0.0.1 -p 6379 -a password

常用命令
卸载服务：redis-server --service-uninstall
开启服务：redis-server --service-start
停止服务：redis-server --service-stop

远程访问配置(修改redis.windows-service.conf,当前应用的配置文件)
bind 127.0.0.1 改为 bind 0.0.0.0

密码设置
密码查询：config get requirepass
密码设置：config set requirepass "123"

java代码访问(redis驱动支持jedis-2.9.0.jar)
 	//连接本地的 Redis 服务
	Jedis jedis = new Jedis("192.168.80.135",6379);
	jedis.auth("123");
	System.out.println("连接成功");
	//查看服务是否运行
	System.out.println("服务正在运行: "+jedis.ping());
	//根据key获取value
	System.out.println("服务正在运行: "+jedis.get("h"));

	//设置失效时间
	//设置30天后失效
	jedis .setex(TOKEN_KEY+userid,60*60*24*30,token);


存储list与map
http://840327220.iteye.com/blog/2274812
	
连接模式
切片式,非切片式
```

2.linux安装(redis-5.0.10.tar.gz)

```
1.下载redis-5.0.10.tar.gz
tar -xzvf redis-5.0.10.tar.gz	//解压
mkdir /data && mkdir /data/soft && mkdir /data/soft/redis	//创建安装目录

2.配置安装,cd到redis-5.0.10.tar.gz解压跟目录
yum install -y make gcc		//源码安装需要的环境
make PREFIX=/data/soft/redis install

3.配置环境变量.bashrc:
export REDIS_HOME=/data/soft/redis
export PATH=$PATH:$REDIS_HOME/bin

4.使用服务安装工具安装redis服务实例
cd utils  //从解压目录cd到utils目录
./install_server.sh	//床架一个redis实例,可以创建多个
    [root@centos8 utils]# ./install_server.sh
    Welcome to the redis service installer
    This script will help you easily set up a running redis server

    Please select the redis port for this instance: [6379] 
    Selecting default: 6379
    Please select the redis config file name [/etc/redis/6379.conf] 
    Selected default - /etc/redis/6379.conf
    Please select the redis log file name [/var/log/redis_6379.log] 
    Selected default - /var/log/redis_6379.log
    Please select the data directory for this instance [/var/lib/redis/6379] 
    Selected default - /var/lib/redis/6379
    Please select the redis executable path [] /data/soft/redis/bin/redis-server	//自定义安装路径
    Selected config:
    Port           : 6379
    Config file    : /etc/redis/6379.conf
    Log file       : /var/log/redis_6379.log
    Data dir       : /var/lib/redis/6379
    Executable     : /data/soft/redis/bin/redis-server
    Cli Executable : /data/soft/redis/bin/redis-cli
    Is this ok? Then press ENTER to go on or Ctrl-C to abort.
    Copied /tmp/6379.conf => /etc/init.d/redis_6379
    Installing service...
    Successfully added to chkconfig!
    Successfully added to runlevels 345!
    Starting Redis server...
    Installation successful!

service redis_6379 status	//查看状态

5.远程访问配置与密码（vi /etc/redis/6379.conf）
bind 127.0.0.1 改为 bind 0.0.0.0
requirepass foobared 	//注释拿掉改成自己密码

6.配置redis启动脚本（否则重启会报Waiting for Redis to shutdown ...）
#配置密码后还要修改启动文件vi /etc/init.d/redis_6379
$CLIEXEC -p $REDISPORT shutdown
替换成
$CLIEXEC -a "123456" -p $REDISPORT shutdown


安装redis-6.0问题：
Please take a look at the provided example service unit files in this directory, and adapt and install them. Sorry!

//注释掉utils/install_server.sh文件如下内容
#_pid_1_exe="$(readlink -f /proc/1/exe)"
#if [ "${_pid_1_exe##*/}" = systemd ]
#then
#	echo "This systems seems to use systemd."
#	echo "Please take a look at the provided example service unit files in this directory, and adapt and install them. Sorry!"
#	exit 1
#fi
#unset _pid_1_exe

就可以执行./install_server.sh安装了
```

其它问题：

```
1.密码问题：
https://blog.csdn.net/qq_42815754/article/details/83827375
https://www.cnblogs.com/userzf/p/12874472.html
命令设置的密码重启后失效，一般不使用这种方式

2.redis 默认rdb aof：
https://www.cnblogs.com/chy18883701161/p/11075123.html
存储	：rdb(快照)	aof(日志)
如果rdb,aof都开启，redis只会拿aof进行恢复
4.0前是增量的，命令抵消的（独立的aof命令处理恢复）
但是4.0后aof包含rdb全量与增加记录些的操作(快)

redis内存数据库三个级别(NO,always,每秒)
aof默认关闭（appendonly no），开启有三个级别
(appendfsync   always,everysec,no)
```