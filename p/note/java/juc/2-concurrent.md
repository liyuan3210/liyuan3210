# 二.concurrent多线程类

代码:

```
https://github.com/liyuan3210/java/juc
com.liyuan3210.juc.c02
```

## 1.ReentrantLock

```
ReentrantLo实现：

理论实现：
cas vs synchronized

AQS就是CAS???
```

## 2.CountDownLatch门栓（也可以用join）



## 3.CyclicBarrier（并发执行固定线程完成不同任务）

```
实际限流会用Guava RateLimiter
```



## 4.Phaser（分阶段并发）



## 5.ReadWriteLock

```
共享锁 read
排他锁 write

读数据不上锁，发现有写数据就加锁
```

## 6.Semaphore（信号灯，可以限流）

```
创建一个数量为1的信号量，有两个线程同时执行。
当一个线程拿到资源后首先(acquire)，减1,为0（不允许线程占有cpu资源执行）,当任务执行完后(release)加1，释放资源给其他进程。
```

## 7.Exchanger（只能两个线程数据交换，线程通信，多个线程???）