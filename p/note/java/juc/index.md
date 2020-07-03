# java多线程高并发

示例源码：

```
https://github.com/liyuan3210/java/juc

```

## 一．线程基础与synchronized

1.线程基础

com.liyuan3210.juc.c00

2.synchronizedf基础

com.liyuan3210.juc.c01

synchronized的底层实现:

```
JDK早期的 重量级 - OS
后来的改进
锁升级的概念：
    我就是厕所所长 （一 二）

sync (Object)
markword 记录这个线程ID （偏向锁）
如果线程争用：升级为 自旋锁
10次以后，
升级为重量级锁 - OS

执行时间短（加锁代码），线程数少，用自旋
执行时间长，线程数多，用系统锁
```

AtomicXXX实现CAS:

```
AtomicXXX实现原理CAS(无锁优化，自旋)
.Compare and Set
.cas(Ｖ，Expected,NewValue)
if V==E
V=New
otherwise try again or fail
CPU原语支持
．ABA问题
Ａ1.0
Ｂ2.0
Ｃ3.0
int无所谓
object对象指向引用发生改变（需要加version号来解决）


ｕnsafe(cas实现)
直接操作虚拟机内存
直接生成类实例
直接操作类或实例变量
CAS相关操作
```

​	