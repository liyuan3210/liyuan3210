# 二.concurrent多线程类

代码:

```
https://github.com/liyuan3210/java/juc
com.liyuan3210.juc.c02
```

## 1.ReentrantLock

```
ReentrantLock实现：

理论实现：
cas vs synchronized

AQS就是CAS???
```

## 2.CountDownLatch门栓（也可以用join）

```
CountDownLatch指定数量X。每个线程业务完成后执行一次countDown()，阻塞那里。countDown()次数达到X，开始执行后面逻辑
```

## 3.CyclicBarrier（并发执行固定线程完成不同任务）

```
CyclicBarrier,当所有操作并发执行完成后才能执行后面操作
//与CyclicBarrier使用场景无关，实际限流会用Guava RateLimiter
```

## 4.Phaser（分阶段并发）

```
T09_TestPhaser2
指定线程数量（人数），阶段自定义。
```

## 5.ReadWriteLock

```
共享锁 read
排他锁 write

读数据不上锁，发现有写数据就加锁
```

## 6.Semaphore（信号灯，可以做线程限流）

```
创建一个数量为1的信号量，有两个线程同时执行。
当一个线程拿到资源后首先(acquire)，减1,为0（不允许其他线程占有cpu资源执行）,当任务执行完后(release)加1，释放资源给其他进程。
```

## 7.Exchanger（只能两个线程数据交换，线程通信，多个线程???）

```
exchanger有两个位置，当地一个exchanger.exchange(s)方法执行为阻塞，第二个执行时会进行数据交换，两个线程继续往下执行。
```

## 8.LockSupport（park停止线程，unpark退出线程）

unsafe方法实现

## 9.上面同步工具实现原理(底层实现AQS[AbstractQueuedSynchronizer])

底层AQS实现原理CAS



