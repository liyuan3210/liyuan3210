# spring cloud微服务

1.数据结构与算法（常用部分）
2.设计模式（常用部分）
3.微服务（形成笔记，概念理清楚）
4.mysql（调优）
5.分布式事物，登录授权jwt，代码生成（框架）

资料：

https://github.com/liyuan3210/data/tree/master/drawio

## 一.分布式事物

### 1.基础理论



### 2.分布式事务（中间件比较）



### 3.案例启动

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

## 二.微服务spring cloud

