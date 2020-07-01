# gc垃圾回收理论

除epsilon（调试） ，zgc，shenandoah之外的gc都是分代模型

⬇

1.8前（包含1.8）逻辑分代，物理分代

G1逻辑分代，物理不分

##  一．分带模型

跟可达算法：

![](img/jvm-root-searching.png)

分带逻辑分区

![](img/jvm-new-old.png)

#### 1.如何定位垃圾

1. 引用计数（ReferenceCount）
2. 根可达算法(RootSearching)(jvm使用的)

#### 2.常见的垃圾回收算法

1. 标记清除(mark sweep) - 位置不连续 产生碎片 效率偏低（两遍扫描）
2. 拷贝算法 (copying) - 没有碎片，浪费空间
3. 标记压缩(mark compact) - 没有碎片，效率偏低（两遍扫描，指针需要调整）



## 二．常见垃圾组合

![](img/jvm-garbage-list.png)

1.serial + serial old（jdk诞生，单线程回收)

2.parallel scavenge + parallel old (1.8默认，多线程回收)

3.parNew + cms	(可以搭配调整，不打段应用并行回收，但问题较多)

浮动垃圾过会出发stw,切换成serial old(等待时间会非常慢)浮动垃圾

![](img/jvm-cms-thread.png)



常见命令使用：

查看-XX所有参数

java -XX:+PrintFlagsFinal -version

查看新生代 与 老年代 比例（查找命令）

java -XX:+PrintFlagsFinal -version | grep NewRatio



三色标记（标记算法）：

cms		三色标记	+	incremental update

g1			三色标记  + satb算法（配合rset进行）

zgc				coloredPointer（颜色指针算法）+写屏障？

shenandoah	coloredPointer（颜色指针算法）+读屏障？

