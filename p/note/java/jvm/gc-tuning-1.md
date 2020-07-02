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

2. java -XX:+PrintCommandLineFlags HelloGC    //-XX:+PrintCommandLineFlags打印默认参数

3. java -Xmn10M -Xms40M -Xmx60M -XX:+PrintCommandLineFlags -XX:+PrintGC HelloGC 

   -Xmn10M	:新生代大小

   -Xms40M	:最小堆大小  -Xmx60M	:最大堆大小	//一般设置相等，减少系统计算资源

   -XX:+PrintGC	:打印GC回收信息

   常见打印GC信息：PrintGCDetails PrintGCTimeStamps PrintGCCauses

   

   查看cms垃圾回收：

4. java -XX:+UseConcMarkSweepGC -XX:+PrintCommandLineFlags HelloGC        

   

5. java -XX:+PrintFlagsInitial 默认参数值

6. java -XX:+PrintFlagsFinal 最终参数值

7. java -XX:+PrintFlagsFinal | grep xxx 找到对应的参数

8. java -XX:+PrintFlagsFinal -version |grep GC

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