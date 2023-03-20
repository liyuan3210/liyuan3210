# jvm

## 一．jvm基础
[1.虚拟机基础概念](jvm-concept.md)

[2.class文件结构](class-format.md)

[3.内存加载过程](memory-load.md)

[4.jmm内存模型](jvm-jmm.md)

[5.运行时内存结构 / jvm常用指令](jvm-runtime.md)

[6.jvm调优实战-理论](gc-tuning-start.md)

[7.1jvm调优实战-1](gc-tuning-1.md)

[7.2jvm调优实战-2](gc-tuning-2.md)

[7.3jvm调优参数配置](gc-tuning-args-config.md)

[8.G1介绍，三色标记算法](gc-tuning-3.md)

#### 语言的发展

* c/c++

  ---手动管理 malloc , free / new delete

  ---忘记释放 -memory leak(内存泄露) 会导致 -out of memory（内存溢出）

  ---释放多次 -释放一次的时候，已经有另外进程使用此内存地址了，会导致使用的程序出现异常问题

  ---开发效率低

  优点：运行效率高

* java python go

  ---java空指针问题（野指针问题依然没解决）

  ---存在gc线程，cpu利用率低

  优点：

  方便内存管理

  GC -Garbage Collector(应用线程只管分配，垃圾回收器负责回收)

  开发效率高，降低程序员门槛，

* rust

  --- 学习难度高

  优点：

  ---运行效率高（asm,c++）

  ---不用手动管理内存，没有gc

  ---ownership(栈消失，堆空间内存直接进行清理)，只要语法通过，就不会有bug

#### 问题
```
1.JIT编译模型
2.JVM栈空间分配
3.G1，ZGC,shenandoah模型
```
