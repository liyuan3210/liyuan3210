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

2.linux安装

make PREFIX=/home/liyuan/soft/redis-6.0.3 install

## 二．redis脚本

```
基于Lua解释器

示例1：
语法：
EVAL script numkeys key [key ...] arg [arg ...]

script： 参数是一段 Lua 5.1 脚本程序。脚本不必(也不应该)定义为一个 Lua 函数。
numkeys： 用于指定键名参数的个数。
key [key ...]： 从 EVAL 的第三个参数开始算起，表示在脚本中所用到的那些 Redis 键(key)，这些键名参数可以在 Lua 中通过全局变量 KEYS 数组，用 1 为基址的形式访问( KEYS[1] ， KEYS[2] ，以此类推)。
arg [arg ...]： 附加参数，在 Lua 中通过全局变量 ARGV 数组访问，访问的形式和 KEYS 变量类似( ARGV[1] 、 ARGV[2] ，诸如此类)。

例子:
EVAL "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second
eval "return {KEYS[1],ARGV[1]}" 1 k1 kval1
注意：
KEYS,ARGV必须大写


示例2：
载入并执行脚本
语法：
EVALSHA sha1 numkeys key [key ...] arg [arg ...] 

例子:
SCRIPT LOAD "return 'hello moto'"							//载入
"232fd51614574cf0867b83d384a5e898cfd24e5a"

EVALSHA "232fd51614574cf0867b83d384a5e898cfd24e5a" 0		//执行
"hello moto"

示例3：
校验脚本是否在缓存中
语法：
SCRIPT EXISTS sha1 [sha1 ...]
例子:
SCRIPT LOAD "return 'hello moto'"							//载入
SCRIPT EXISTS 232fd51614574cf0867b83d384a5e898cfd24e5a		//返回1
SCRIPT FLUSH     											//清空缓存
SCRIPT EXISTS 232fd51614574cf0867b83d384a5e898cfd24e5a		//返回0


示例4：
杀死当前正在运行的 Lua 脚本
SCRIPT KILL
```

## 三．事物

```
Redis 事务可以一次执行多个命令， 并且带有以下三个重要的保证：
批量操作在发送 EXEC 命令前被放入队列缓存。
收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余的命令依然被执行。
在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中。

一个事务从开始到执行会经历以下三个阶段：
开始事务。
命令入队。
执行事务。

实例
先以 MULTI 开始一个事务， 然后将多个命令入队到事务中， 最后由 EXEC 命令触发事务

127.0.0.1:6379> MULTI			//开启事务
OK
127.0.0.1:6379> set a1 1
QUEUED
127.0.0.1:6379> set a2 2
QUEUED
127.0.0.1:6379> eval "return {KEYS[1],argv[1]}" 1 k1 kvalue1		//报错打点
QUEUED
127.0.0.1:6379> set a3 3
QUEUED
127.0.0.1:6379> exec			//执行命令
1) OK
2) OK
3) (error) ERR Error running script (call to f_b6de017ad219f1a815722491f21b083101c0e32d): @enable_strict_lua:15: user_script:1: Script attempted to access unexisting global variable 'argv'
4) OK
127.0.0.1:6379>
127.0.0.1:6379> get a3			//上面命令异常后,剩下的命令依然执行
"3"


1.事务中任意命令执行失败，其余的命令依然被执行。
2.但在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中


命令行执行eval
redis-cli.exe --eval luaTest.lua 1 k1 kval1

```
