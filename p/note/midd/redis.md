
# redis

资料：

```
百度云盘/liyuandf:共享资源->公共网络资料->redis-mashibing
代码：https://github.com/liyuan3210/java/tree/master/demo/java-demo-redis
```


## redis常用数据类型
```
原理讲解redis实现基于epoll

1.常见redis类型使用
	list ，set，hash，sorted_set，skiplist
	命令帮助使用:help 
	学习站点redis.cn

管道（pipelin）：
yum install -y nc
echo -e "set k1 99\nincr k2\n getk2" | nc localhost:6379
减少网络交互流量

发布、订阅:
publish ooxx hello	//发布
subscribe ooxx		//各个客户端进行订阅
数据类型sorted set

布隆过滤器：
需要加载redisbloom模块，
$ redis-server --loadmodule /path/redisbloom.so 
解决客户端搜索后台数据库没有的商品，减少服务端查询数据库的次数
BF.ADD ooxx abc	//向redis添加数据库已有的数据
BF.EXISTS ooxx abc	//验证客户端数据abc有没有
CF.DEL ooxx	//删除

2.redis内存回收策略：
    1>.key值设置有效期
    2>.回收策略
        redis使用的内存使用最大限制:maxmemory <bytes>
        
        算法：
        	LRU(Least Recently Used):最近最少使用（最长时间）
        	LFU(Least Frequently Used):最不经常使用（最少次）
        	random:随机
        	TTL(Time to live):生存时间
        
        回收策略：		
            noeviction: 当内存限制达到,再向redis添加数据，会直接返回错误。
            allkeys-lru:使用LRU算法。最少使用的缓存数据将被丢弃。
            volatile-lru:类似使用LRU-K算法。拥有两个队列，一个是缓存队列，一个是过期队列，丢弃数据从过期队列开始。
            allkeys-random:随机回收缓存数据。
            volatile-random:随机回收过期队列的缓存数据。
            volatile-ttl:回收过期队列的缓存数据，而且优先回收存活时间(ttl)较短的缓存数据。
```
## 常用功能

```

```




## 持久化

```
动态设置参数
> config set

配置(rdb)
rdbcompression yes	//开启压缩

rdb,aof:
 	4.0版本之前是单独的rdb,aof,数据恢复是重写记录的reids指令，时间长了会导致恢复时间很长
 	4.0版本后rdb与aof一起，增量的从rdb快照执行重写
 
1).rdb:
	手动触发：
	save命令(主进程阻塞)，bgsave（主进fork新进程进行磁盘持久化）
	自动触发：
	vi 6379.conf
	//rdb文件名称
	dbfilename dump.rdb	//文件名称
	dir /var/lib/redis/6379	//文件存的目录
	//关闭快照
	save ""		//关闭快照
	//实际用的是bgsave
	save [时间] [操作数]	//两个有一个满足触发快照
	save 60 100		//60秒没达到100，走下面规则
	save 120 10		//120秒没满足10，走下面规则
	save 360 5		//360秒没满足5，没有规则就执行
2）.aof:日志，丢失数据少(只记录命令写入与修改)
	rdb与aof可以同时开，如果开启aof，只会用aof来恢复
	aof配置：
		appendonly no	//默认关闭，打开你改为yes
		appendfilename "appendonly.aof"
		//aof三个级别always（一直刷盘）,everysec（每秒种落一次盘）,no（buffer满了才刷盘）
		appendfsync everysec  
		//aof太大，有重写aof压缩过程，手动重写命令：bgrewriteaof，自动配置如下
		auto-aof-rewrite-percentage 100	//表示当前aof空间与上一次重写aof空间的比值
		auto-aof-rewrite-min-size 64mb	//重写最小体积64mb后发生rewrite
		no-appendsync-on-rewrite no	//reis抛出子进程，要不要跟子进程争抢？？？
rdb,aof混合持久化(默认开启)：
		aof-use-rdb-preamble yes  //混合模式，通过rdb增量aof来恢复数据
		重启会判断是否包含rdb,如果包含加载rdb文件再加载aof
```

## 分布式锁

```
3.分布式锁
	setnx只能设置不存在的key，否则设置不成功
	-> setnx lock 1
	
	3.1）单机锁
	3.2）lua脚本实现锁
```

## 事物

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


watch:
MULTI事务前可以wathc k1一个值，如果k1值改变，整个事务是失败的，

```
## LUA脚本

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
