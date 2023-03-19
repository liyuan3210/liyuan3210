# gc调优1

## 一．jvm调优hello world

```java
import java.util.List;
import java.util.LinkedList;

public class HelloGC {
  public static void main(String[] args) {
    System.out.println("HelloGC!");
    List list = new LinkedList();
    for(;;) {
      byte[] b = new byte[1024*1024];
      list.add(b);
    }
  }
}
```

1. 区分概念：内存泄漏memory leak（不一定产生内存溢出），内存溢出out of memory

2. 参数查看命令java -XX:+PrintCommandLineFlags

   **堆内存（堆内存不超过物理内存的3/4）**

   -XX:MaxHeapSize=15032385536 与-Xms14336M,

   -XX:MinHeapSize=15032385536与-Xmx14336M 参数含义相等（一般设置相等，减少系统计算资源）

   **年轻带（年轻代的大小不超过堆内存的3/8）**

   -XX:NewSize=5637144576 / 1024 /1024 =	5376M	   //年轻带大小
   -XX:MaxNewSize=5637144576 / 1024 /1024 = 5376M   //最大年轻带大小

3. java -XX:+PrintCommandLineFlags HelloGC    //-XX:+PrintCommandLineFlags打印默认参数

   -XX:InitialHeapSize=244714176 / 1024 / 1024 等于兆（M）	//起始堆大小

   -XX:MaxHeapSize=3915426816 / 1024 / 1024 等于兆（M）	//最大堆大小

4. java -Xmn10M -Xms40M -Xmx60M -XX:+PrintCommandLineFlags -XX:+PrintGC HelloGC 

   -Xms40M	:最小堆大小  -Xmx60M	:最大堆大小	//一般设置相等，减少系统计算资源

   -Xmn10M	:新生代大小

   -XX:+PrintGC	:打印GC回收信息

   常见打印GC信息：PrintGCDetails PrintGCTimeStamps PrintGCCauses

   查看cms垃圾回收：

5. java -XX:+UseConcMarkSweepGC -XX:+PrintCommandLineFlags HelloGC        

6. java -XX:+PrintFlagsInitial 默认参数值

7. java -XX:+PrintFlagsFinal 最终参数值

8. java -XX:+PrintFlagsFinal | grep xxx 找到对应的参数

9. java -XX:+PrintFlagsFinal -version |grep GC

垃圾日志查看分析：

heap dump部分:

## 二．jvm调优概念及目标

### 调优前的基础概念：

1. 吞吐量：用户代码时间 /（用户代码执行时间 + 垃圾回收时间）
2. 响应时间：STW越短，响应时间越好

所谓调优，首先确定，追求啥？吞吐量优先，还是响应时间优先？还是在满足一定的响应时间的情况下，要求达到多大的吞吐量...

问题：

科学计算，吞吐量。数据挖掘，thrput。吞吐量优先的一般：（PS + PO）

响应时间：网站 GUI API （1.8 G1）

### 设定日志参数:

java -Xloggc:/opt/xxx/logs/xxx-xxx-gc-%t.log -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=5 -XX:GCLogFileSize=20M -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintGCCause HelloGC

产生五个文件，满了后把最后一个干掉继续产生，最终不会超过20M*5=100M