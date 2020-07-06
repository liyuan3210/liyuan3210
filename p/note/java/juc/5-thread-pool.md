# 五．线程池

## 1.ThreadPoolExecutor线程池主要类

```
Executor
继承
ExecutorServic
Executors　工具类

Future
继承
RunnableFuture
实现
FutureTask

多个任务同时执行任务并返回汇总结果
CompletableFuture（底层用的ForkJoinPool）
```

## 2.默认线程池



## 3.自定义线程池

```
ThreadPoolExecutor tpe = new ThreadPoolExecutor(2, 4,
                60, TimeUnit.SECONDS,
                new ArrayBlockingQueue<Runnable>(4),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.CallerRunsPolicy());
```

## 4.ForkJoinPool

```
递归分解总的任务，很扫的线程执行很多的任务，cpu密集型
```

