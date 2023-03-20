## 一．线程，synchronized基础

示例源码：

```
https://github.com/liyuan3210/java/juc
```

## 1.线程基础

com.liyuan3210.juc.c00

## 2.synchronized基础

com.liyuan3210.juc.c01

synchronized锁升级过程:

```
JDK早期的synchronized（重量级，需要每次向操作系统申请）
后来的改进
锁升级的概念：
    （文章）我就是厕所所长 （一 二）

1）锁四种状态升级
sync (Object)
1.只有一个线程markword记录这个线程ID （偏向锁）
2.如果有线程争用：升级为 （自旋锁）
3.自旋10次以后,向OS申请资源（重量锁）

2）atomic,lock...基本都是用的（自旋锁），CAS实现

3）适用场景：
执行时间短，线程数少，用（自旋锁）
执行时间长，线程数多，用（重量锁）
```

## 3.volatile

代码：com.liyuan3210.juc.c01.T12_volatile

```
* 保证线程可见性（但不保证原子性）
* 禁止指令重排序

   java类里声明的变量在堆内存（共享内存）里面。每个线程都有变量单独存储的副本，线程1里面的操作的变量什么时候写进共享内存里面，线程2什么时候去同步，这个不好控制。
   
volatile尽量修饰简单类型值，不要修饰Object（成员变量改变观察不到）引用值
```

## 4.AtomicXXX(底层实现CAS):

代码:com.liyuan3210.juc.c01.T15.T01_AtomicInteger

```
AtomicXXX实现原理CAS(无锁优化，自旋锁)
.Compare and Set 比较和设置
.cas(V，Expected,NewValue) 要改的值，期望值，新值
    if V（要改的值）==E（期望值）
    V=New 相等设置新值
    otherwise try again or fail	//否则再次尝试
    CPU原语支持			//cas底层unsafe
．ABA问题
    A 1.0
    B 2.0
    C 3.0
    int无所谓
    object对象指向引用发生改变（需要加version号来解决）

```

## 5.CAS(底层实现Unsafe)

代码:com.liyuan3210.juc.c01.T15.HelloUnsafe

```
直接操作虚拟机内存
直接生成类实例
直接操作类或实例变量
CAS相关操作
```

## AtomicLong,synchronized,LongAdder对比

代码：com.liyuan3210.juc.c01.T15.T02_AtomicVsSyncVsLongAdder

```
AtomicLong			//使用cas实现
synchronized		//sync
LongAdder			//使用分段锁
```
