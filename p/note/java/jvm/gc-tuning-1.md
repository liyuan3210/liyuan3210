# 1.gc调优

试验用程序：

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