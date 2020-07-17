## 三.Netty模型介绍

## Reactor模式(线程模型)

1）单Reactor单线程

2）单Reactor多线程

3）主从Reactor多线程（netty从此模型优化）

AIO使用了Proactor模式，对比Proactor与Reactor模式？？？

https://blog.csdn.net/KingCat666/article/details/77685608

## 1.模型介绍

![](img/netty-3.1.png)

## 2.simple实例（根据简单实例分析）

## 3.taskqueue

三种task典型使用场景：

１）用户程序自定义的普通任务（把线程放到workGroup task任务队列里面）
２）用户自定义定时任务
３）非当前Reactor线程调用Channel各种方法？？？

#### 4.4异步FutureListener机制

## 4.5服务程序http入门（过滤指定资源）

浏览器每一次请求刷新产生不同的pipeline

```
ch.pipeline().addLast(new XXXHandler());
ch.pipeline().addFirst(new XXXHandler());
区别？？？=
```

pipeline 和　channelPipeline???

