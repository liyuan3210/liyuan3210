# java多线程高并发

```
官网api：
https://docs.oracle.com/javase/8/docs/api/index.html
示例源码：
https://github.com/liyuan3210/java/juc
```

### 概要

```
一。线程基础

二。并发编程的三大特征

三。锁

四。阻塞队列

五。线程池

六。并发集合

七。JUC并发工具

八。异步编程
```

## [一．线程，synchronized基础](#)

代码：com.liyuan3210.juc.c00	，	com.liyuan3210.juc.c01

```
synchronized升级，CAS,volatile,AtomicXXX原理
```
示例源码：

```
https://github.com/liyuan3210/java/juc
```

#### 1.线程基础

com.liyuan3210.juc.c00

#### 2.synchronized基础

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

#### 3.volatile

代码：com.liyuan3210.juc.c01.T12_volatile

```
* 保证线程可见性（但不保证原子性）
* 禁止指令重排序

   java类里声明的变量在堆内存（共享内存）里面。每个线程都有变量单独存储的副本，线程1里面的操作的变量什么时候写进共享内存里面，线程2什么时候去同步，这个不好控制。
   
volatile尽量修饰简单类型值，不要修饰Object（成员变量改变观察不到）引用值
```

#### 4.CAS（AtomicXXX底层实现CAS）:

代码:com.liyuan3210.juc.c01.T15.T01_AtomicInteger

```
AtomicXXX实现原理CAS(无锁优化，自旋锁)
.Compare and Set 比较和设置

.cas(V，Expected,NewValue) 要改的值，期望值，新值
    if V（要改的值）==E（期望值）
    V=New 相等设置新值
    otherwise try again or fail	//循环比较，否则再次尝试
    CPU原语支持			//cas底层unsafe
    
．ABA问题
	根据前面比较值还是期望值，但中间已经改变过，又改回来了，这就需要加version号来解决
    A 1.0
    B 2.0
    C 3.0
    int无所谓
    object对象指向引用发生改变（需要加version号来解决）
    
．其中的问题
	在cas比较过程中，比较相等时可能被另外一个线程改了（Unsafe）
	Unsafe调用的是CAS操作。
	java层面用的乐观锁，但底汇编层用到了Lock cmpxchg悲观锁
	
```

#### 5.Unsafe？？？

代码:com.liyuan3210.juc.c01.T15.HelloUnsafe

```
直接操作虚拟机内存
直接生成类实例
直接操作类或实例变量
CAS相关操作
```

#### AtomicLong,synchronized,LongAdder对比

代码：com.liyuan3210.juc.c01.T15.T02_AtomicVsSyncVsLongAdder

```
AtomicLong			//使用cas实现
synchronized		//sync
LongAdder			//使用分段锁
```


## [二．concurrent多线程类](#)

代码：com.liyuan3210.juc.c02

```
ReentrantLock，CountDownLatch，CyclicBarrier．．．线程同步容器，基于AQS实现
```
代码:

```
https://github.com/liyuan3210/java/juc
com.liyuan3210.juc.c02
```

#### 1.ReentrantLock

```
ReentrantLock实现：

理论实现：
cas vs synchronized

AQS就是CAS???
```

#### 2.CountDownLatch门栓（也可以用join）

```
CountDownLatch指定数量X。每个线程业务完成后执行一次countDown()，阻塞那里。countDown()次数达到X，开始执行后面逻辑
```

#### 3.CyclicBarrier（并发执行固定线程完成不同任务）

```
CyclicBarrier,当所有操作并发执行完成后才能执行后面操作
//与CyclicBarrier使用场景无关，实际限流会用Guava RateLimiter
```

#### 4.Phaser（分阶段并发）

```
T09_TestPhaser2
指定线程数量（人数），阶段自定义。
```

#### 5.ReadWriteLock

```
共享锁 read
排他锁 write

读数据不上锁，发现有写数据就加锁
```

#### 6.Semaphore（信号灯，可以做线程限流）

```
创建一个数量为1的信号量，有两个线程同时执行。
当一个线程拿到资源后首先(acquire)，减1,为0（不允许其他线程占有cpu资源执行）,当任务执行完后(release)加1，释放资源给其他进程。
```

#### 7.Exchanger（只能两个线程数据交换，线程通信，多个线程???）

```
exchanger有两个位置，当地一个exchanger.exchange(s)方法执行为阻塞，第二个执行时会进行数据交换，两个线程继续往下执行。
```

#### 8.LockSupport（park停止线程，unpark退出线程）

unsafe方法实现

#### 9.上面同步工具实现原理(底层实现AQS[AbstractQueuedSynchronizer])

底层AQS实现原理CAS

## [三.生产消费容器demo,读源码规则](#)

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

#### 1.demo1

```
实现一个容器，提供两个方法，add，size
写两个线程，线程1添加10个元素到容器中
线程2实现监控元素个数，当个数到5个时，线程2提示并结束
```

#### 2.demo2

```
写一个固定容量同步容器，有put，get，getCount方法，
能够支持2个生成线程，及10个消费者线程的阻塞调用
```

#### 3.读源码原则

```
跑不起来不读
解决问题就好-目的性
一条线索到底
无关细节略过
一般不读静态
一般动态读法

要点：
1.数据结构基础
2.设计模式

AQS：
Template Method
Callback Function
父类默认实现，子类具体实现
```

3.1流程图

3.2VarHandle

```
普通属性原子操作，比反射快，直接操作二进制码
```

#### 4.RefType And TheadLocal

代码：com.liyuan3210.juc.c04

TheadLocal为什么每个线程能独享数据：

​		因为每次set都设置到了当前线程的map里面了

java四种引用‘强软弱虚’

```
强引用：值为null后内存会进行回收（一般new出来的都是强引用）
软引用：产生gc内存不一定回收，产生新的对象不满足空间时才进行回收（验证程序需要配置堆内存大小20mb方便测试）
弱引用：只要gc就进行回收，作用（如果有个强引用指向，强引用消失，会跟随回收，一般用在容器，TheadLocal用到）
虚引用：堆内存外，DirectByteBuffer。虚引用get不到值.
通过队列获取垃圾回收通知（c,c++写的用对应语言回收），1.9后就没unsafe
```

## [四.Collection,Map并发容器](#)

代码：com.liyuan3210.juc.c05

```
买票系统实现demo（List，Vector，ConcurrentLinkedQueue）
Map
queue
A1B2C3各种实现
```
![](img/collection-map.jpeg)

物理上只存在两种结构，数组与链表

java主要分两大类容器，List与Map

#### 1.Hashtable,HashMap,synchronizedMap,ConcurrentHashMap比较

代码：com.liyuan3210.juc.c05.map

Hashtable，vector基本不用（自带锁）

```
Hashtable		线程安全
HashMap			线程不安全
synchronizedMap()	与Hashtable效率相当，降低锁力度
ConcurrentHashMap	 插入效率低，读取效率高
```

#### 2.以卖票系统为例，比较ArrayList，Vector，   LinkedList，ConcurrentLinkedQueue

代码：com.liyuan3210.juc.c05.collection

```

```

#### 3.容器

代码：com.liyuan3210.juc.c05.queue

```
总结：
1：对于map/set的选择使用(线程不安全)
HashMap	无序,遍历效率低
TreeMap	有序，使用红黑树，平衡
LinkedHashMap	无序，链表接口，查询效率会变高

2.线程安全
Hashtable
HashMap+Collections.sychronizedXXX	//等同Hashtable

3.线程安全,高并发(无锁cas实现)
ConcurrentHashMap
ConcurrentSkipListMap 排好序，在链表基础上加上分段索引（cas在tree上实现非常复杂，然后实现skip）

3：队列
    ArrayList
    LinkedList
    Collections.synchronizedXXX
    CopyOnWriteList		写时候加索，读的时候不加索。使用在写少读多情况
队列Queue：
	CocurrentLinkedQueue 	//concurrentArrayQueue,内部加锁队列,offer(添加结果返回值表示),add(不成功会报异常) | poll（取出值并移除），peek（只取出）
	BlockingQueue			//阻塞队列
		LinkedBQ	//无界队列 put(满了就阻塞)，take（为空就阻塞）
		ArrayBQ		//有界队列	put(满了就阻塞)，
		TransferQueue	//transfer('data')是阻塞（确认数据已取走），另一个线程take后才往下走，多个生产对应多个消费线程基于SynchronusQueue
		SynchronusQueue	 //同步数据交换，put阻塞，消费线程take后才往下走，1对1线程
	DelayQueue执行定时任务	  //自定义方法根据规则，根据时间规则调度
		PriorityQueue 					//内部二叉树实现排序
		
代码:
	T01_ConcurrentMap		//高并发集合
	T02_CopyOnWriteList		//写时复制。写时效率低,读时效率高(读没加锁),适合写少都多情况
	T03_SynchronizedList	//synchronizedList返回加锁集合
	T04_ConcurrentQueue		//内部加锁Queue常用操作,offer类似add,poll类似remove,peek拿出来但不从队列删除
	T05_LinkedBlockingQueue //阻塞队列,生产消费队列使用
	T06_ArrayBlockingQueue  //有界队列
	T07_DelayQueue			//定时任务执行
	T08_TransferQueue		//消费者先启动(transfer找不到消费者会阻塞),再启动生产者
	T09_SynchronusQueue		//容量为零,put阻塞,等待消费者消费(用add会报错)
	
	list queue区别？
	对线程提供友好的api offer,peek,poll
	blocking阻塞：put,take



ConcurrentHashMap	无序,迭代效率低
ConcurrentSkipListMap 排好序，在链表基础上加上分段索引
TreeMap	使用红黑树，
linkHashMap	使用链表，迭代查询效率高

CopyOnWriteArrayList	写时候加索，读的时候不加索。使用在写少读多情况

BlockingQueue 阻塞队列

```

## [五．线程池](#)

代码：com.liyuan3210.juc.c06

#### 1.ThreadPoolExecutor，ForkJoinPool线程池主要类

```
Executor
继承
ExecutorServic
Executors　工具类

Callable(有返回值) -> Runnable

Future+（存储执行将来的结果）
继承
RunnableFuture
实现
FutureTask->Future+Runnable

ForkJoinPool
递归分解总的任务，很扫的线程执行很多的任务，cpu密集型

多个任务同时执行任务并返回汇总结果
CompletableFuture（底层用的ForkJoinPool）
```

#### 2.默认线程池

```
1.newSingleThreadExecutor //保证任务顺序执行，维护线程队列生命周期
2.newCachedThreadPool //当有线程空闲时，默认60秒后自动消失，如果有新任务来，使用空闲线程执行任务，不用再次创建新的线程
3.newFixedThreadPool //固定的线程数,用户线程可以并行处理
4.newScheduledThreadPool //线程任务队列,设定多长时间执行
5.newWorkStealingPool	//forkjoin实现，每个线程任务维护自己的队列，当一个线程空闲时会从其它线程任务队列`偷`任务执行(任务的拆分，结果合并)

6.parallelStream //forkjoin实现，流式API，并行处理

```

#### 3.自定义线程池

```
public ThreadPoolExecutor(int corePoolSize,		//核心线程数
                        int maximumPoolSize,	//最大线程数
                        long keepAliveTime,		//非工作线程等待时间
                        TimeUnit unit,			//非工作线程等待时间单位
                        BlockingQueue<Runnable> workQueue,	//线程没有工作时放入的队列
                        ThreadFactory threadFactory,	//构建线程的线程工作
                        RejectedExecutionHandler handler) {	//线程无法执行时，执行当前策略
```


## 六．JMH,Disruptor

相对遍历　链表　一定　比　数组低

## 七.jMeter使用

网络参考

https://www.cnblogs.com/spareyaya/p/12807638.html

**代码**：https://github.com/liyuan3210/java/tree/master/frame/demo/jwt

```
一。jmeter简单使用
1.安装
https://jmeter.apache.org/
并准备启动好测试java项目jwt

2.切换成简体中文
菜单栏的Options->Choose Language->Chinese(Simplified)

3.新建一个简单的测试计划
默认打开jmeter会默认新建一个测试计划“Test Plan”（我们也可以新建一个测试计划File->new）
3.1）创建线程组（Threads group）
右键点击TestPlan->添加->线程（用户）->线程组
3.2）创建http请求
完成后选择线程组(Thread Group)，在这个线程组上点击右键，添加->取样器（sampler）->HTTP请求
接口数据(貌似二选一)：1）Parameters	2）Body Data	
3.3）报错（Content type 'text/plain;charset=UTF-8' not supported"）
选中“http请求头右键”添加->配置原件(config element)->HTTP信息头管理器(Http header manager)，配置如下：
Content-Type	|		application/json;charset=UTF-8

4.添加“查看结果树”（View Results Tree）与“聚合报告”（Summary Report）
查看结果树，聚合报告 可以放在“测试计划”，“线程组”，“Http请求”。
放在“测试计划”里面表示监控真个测试计划，如果放在“线程组”表示监控这一个线程组，放在单独http请求里面，表示只监控这一个接口

5.根据简单例子获取token
http://127.0.0.1:8080/jwt/login

6.根据获取token 配置授权，调用授权接口
选中“http请求头右键”添加->配置原件(config element)->HTTP信息头管理器(Http header manager)Authorization
http://127.0.0.1:8080/jwt/hello



7.线程组配置介绍
number。。。:一次发X次请求
Ramp。。。。：0（点击压测开始一刹那发X次请求）或数字为 n（表示n秒要发X次请求）
Loop。。。。：表示循环上面步骤多少次


二。jmeter高级使用
1.共享配置
2.线程并行，顺序
3.参数
```

### jmeter授权配置与postman授权配置，及各种授权的区别？？？

jmeter授权配置
选中“http请求头右键”添加->配置原件(config element)->HTTP Authorization Manager/
<img src="img/jmeter1.png" style="zoom:50%;" />

 postman授权配置
<img src="img/jmeter2.png" style="zoom:50%;" />

参考
postman的Authorization <br/>
https://zhuanlan.zhihu.com/p/470189410<br/>
jmeter 新增带token的http请求<br/>
https://blog.csdn.net/fwk19840301/article/details/123046759<br/>