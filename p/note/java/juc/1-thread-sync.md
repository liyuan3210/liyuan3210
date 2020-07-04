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
JDK早期的 重量级 - OS
后来的改进
锁升级的概念：
    （文章）我就是厕所所长 （一 二）

sync (Object)
锁四种状态升级
只有一个线程markword记录这个线程ID （偏向锁）
如果线程争用：升级为 （自旋锁）
10次以后：升级为（重量锁） - 向OS申请资源

执行时间短（加锁代码），线程数少，用自旋锁
执行时间长，线程数多，用系统锁(重量级锁)
```

## 3.volatile

代码：com.liyuan3210.juc.c01.T12_volatile

```
线程可见性
指令重排序

volatile尽量修饰简单类型值，不要修饰Object（成员变量改变观察不到）引用值
```

## 4.AtomicXXX(底层实现CAS):

代码:com.liyuan3210.juc.c01.T15.T01_AtomicInteger

```
AtomicXXX实现原理CAS(无锁优化，自旋锁)
.Compare and Set
.cas(V，Expected,NewValue)
if V==E
V=New
otherwise try again or fail
CPU原语支持
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
