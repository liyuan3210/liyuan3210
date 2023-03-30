# spring

## [一.spring源码分析](spring_src.md)
```
spring源码分析
```
## [二.spring方案](jwt_spring_security.md)

```
1).jwt
2).mybatisPlus
3).spring_security
```
## [三.微服务](spring_cloud.md)

```
源码：https://github.com/liyuan3210/microservices
```
### [四.微服务分布式事物](#)

```
课程：https://www.bilibili.com/video/BV1FJ411A7mV?from=search&seid=9644786464389956787
链接: https://pan.baidu.com/s/1rX3kbHaD9LF4-xRMo-J04w 提取码: qq64
```

### 分布式数据库事物

1).2pc协议(数据库层面的，每个数据库都有自己的2pc协议)
2).XA方案（基于数据库XA协议，实现的2pc吗，又称XA方案）资源锁要等两阶段结束才释放
3).seata阿里的开源框架（不需要数据库支持XA协议，只要求支持本地数据库事物提交即可，不需要长时间占用连接资源）
	seata支持AT（2pc）和 TCC 模式

###### 1.基础理论



###### 2.分布式事务（中间件比较）



###### 3.案例启动

实例：https://github.com/liyuan3210/java/tree/master/spring/dtx_parent

**详细见课间资料**

银行转账实例，张三转账给李四，操作两个库

###### 3.1>基于2PC的seata分布式事物

1>.首先要准备seata服务端环境，解压seata-server-0.7.1.zip包，执行如下命令：

```
sh seata-server.sh -p 8888 -m file
```

2>.创建两个库bank1,bank2,并导入要使用的表undo_log（seata使用的表），account_info（业务表）

3>.拉取项目，配置工程

配置数据库application-local.yml两个工程分别指向两个库bank1,bank2。

配置seata，首先在解压包seata-server-0.7.1.zip拷贝两个文件（registry.conf，registry.type）到项目resources下，修改file里面的service{...}信息

###### 3.2>.基于TCC的Hmily的事物

注意：

使用Hmily时要注意三种异常处理 **空回滚，幂等，悬挂**

1>.首先需要创建hmily库，用于存储hmily框架记录的数据。

2>.然后创建两个库bank1,bank2，并两个库同时创建try、confirm、cancel三张日志表

