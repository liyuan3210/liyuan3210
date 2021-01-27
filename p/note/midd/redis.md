# redis

## 一．redis脚本

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

## 二．事物

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

