# java多线程高并发

```
官网api：
https://docs.oracle.com/javase/8/docs/api/index.html
示例源码：
https://github.com/liyuan3210/java/juc
```

## [一．线程，synchronized基础](1-thread-sync.md)

代码：com.liyuan3210.juc.c00	，	com.liyuan3210.juc.c01

```
synchronized升级，CAS,volatile,AtomicXXX原理
```

## [二．concurrent多线程类](2-concurrent.md)

代码：com.liyuan3210.juc.c02

```
ReentrantLock，CountDownLatch，CyclicBarrier．．．线程同步容器，基于AQS实现
```

## [三.生产消费容器demo,读源码规则](3-concurrent.md)

代码：com.liyuan3210.juc.c03，com.liyuan3210.juc.c04

```
demo1(2个进程一个进行add数据，另一个进程监控数量给出提示并退出)
demo2(生产消费demo，2个生产线程，10个消费线程)
AQS源码阅读
VarHandle(普通属性原子操作，比反射快，直接操作二进制码)

代码：com.liyuan3210.juc.c04
	java四种引用‘强软弱虚’与TheadLocal
	
JUC:	java.util.concurrent.*			//高并发工具类
AQS:	AbstractQueuedSynchronizer.*	//所有锁的核心
CAS：	AQS底层实现是CAS
```

## [四.Collection,Map并发容器](4-collection-map.md)

代码：com.liyuan3210.juc.c05

```
买票系统实现demo（List，Vector，ConcurrentLinkedQueue）
Map
queue
A1B2C3各种实现
```

## [五．线程池](5-thread-pool.md)

代码：com.liyuan3210.juc.c06

```
...
```

## 六．JMH,Disruptor

相对遍历　链表　一定　比　数组低

## 七.jMeter使用

https://www.cnblogs.com/spareyaya/p/12807638.html