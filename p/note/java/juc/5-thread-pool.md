# 五．线程池

## 1.ThreadPoolExecutor，ForkJoinPool线程池主要类

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

## 2.默认线程池

```
1.newSingleThreadExecutor //保证任务顺序执行，维护线程队列生命周期
2.newCachedThreadPool //当有线程空闲时，默认60秒后自动消失，如果有新任务来，使用空闲线程执行任务，不用再次创建新的线程
3.newFixedThreadPool //固定的线程数,用户线程可以并行处理
4.newScheduledThreadPool //线程任务队列,设定多长时间执行
5.newWorkStealingPool	//forkjoin实现，每个线程任务维护自己的队列，当一个线程空闲时会从其它线程任务队列`偷`任务执行(任务的拆分，结果合并)

6.parallelStream //forkjoin实现，流式API，并行处理

```

## 3.自定义线程池

```
public ThreadPoolExecutor(int corePoolSize,		//核心线程数
                        int maximumPoolSize,	//最大线程数
                        long keepAliveTime,		//非工作线程等待时间
                        TimeUnit unit,			//非工作线程等待时间单位
                        BlockingQueue<Runnable> workQueue,	//线程没有工作时放入的队列
                        ThreadFactory threadFactory,	//构建线程的线程工作
                        RejectedExecutionHandler handler) {	//线程无法执行时，执行当前策略
```
