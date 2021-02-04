# gc调优2

代码：

```java
import java.math.BigDecimal;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;
import java.util.concurrent.ScheduledThreadPoolExecutor;
import java.util.concurrent.ThreadPoolExecutor;
import java.util.concurrent.TimeUnit;

/**
 * 从数据库中读取信用数据，套用模型，并把结果进行记录和传输
 */

public class T15_FullGC_Problem01 {

    private static class CardInfo {
        BigDecimal price = new BigDecimal(0.0);
        String name = "张三";
        int age = 5;
        Date birthdate = new Date();

        public void m() {}
    }

    private static ScheduledThreadPoolExecutor executor = new ScheduledThreadPoolExecutor(50,
            new ThreadPoolExecutor.DiscardOldestPolicy());

    public static void main(String[] args) throws Exception {
        executor.setMaximumPoolSize(50);

        for (;;){
            modelFit();
            Thread.sleep(100);
        }
    }

    private static void modelFit(){
        List<CardInfo> taskList = getAllCardInfo();
        taskList.forEach(info -> {
            // do something
            executor.scheduleWithFixedDelay(() -> {
                //do sth with info
                info.m();

            }, 2, 3, TimeUnit.SECONDS);
        });
    }

    private static List<CardInfo> getAllCardInfo(){
        List<CardInfo> taskList = new ArrayList<>();

        for (int i = 0; i < 100; i++) {
            CardInfo ci = new CardInfo();
            taskList.add(ci);
        }

        return taskList;
    }
}
```



## 1.cpu经常100％，如何调优

top或jps命令查看java进程id

top -Hp 1333		//根据进程id查看具体哪个线程占用cpu高,线程id是7951

​		*  jstack -l 7951		//列出指定java进程里面的线程信息（7951十进制转成16进制ox1f0f）

​		*  jstack 1333		//列出这个java进程下面所有线程信息

jps定位进程

​		* jinfo pid 		//查看java线程基本信息

jstat -gc 动态观察gc情况 / 阅读GC日志发现频繁GC / arthas观察 / jconsole/jvisualVM/ Jprofiler（最好用）

```
 jstat -gc 4655 500 : 每个500个毫秒打印GC的情况 如果面试官问你是怎么定位OOM问题的？如果你回答用图形界面（错误） 1：已经上线的系统不用图形界面用什么？（cmdline arthas） 2：图形界面到底用在什么地方？测试！测试的时候进行监控！（压测观察）
```

## 2.系统内存飙高，如何查看

查看堆内存（生产系统使用jmap会影响系统）

1. jmap - histo 4655 | head -20　//查找有多少对象产生，在线定位

2. jmap -dump:format=b,file=xxx pid ：

   线上系统，内存特别大，jmap执行期间会对进程产生很大影响，甚至卡顿（电商不适合） 

   1>设定了参数HeapDump，OOM的时候会自动产生堆转储文件

   2>很多服务器备份（高可用），停掉这台服务器对其他服务器不影响 3：3>在线定位arthas(一般小点儿公司用不到)

3. java -Xms20M -Xmx20M -XX:+UseParallelGC -XX:+HeapDumpOnOutOfMemoryError com.mashibing.jvm.gc.T15_FullGC_Problem01

4. 使用MAT / jhat /jvisualvm 进行dump文件分析 https://www.cnblogs.com/baihuitestsoftware/articles/6406271.html

5. jhat -J-mx512M xxx.dump [http://192.168.17.11:7000](http://192.168.17.11:7000/) 拉到最后：找到对应链接 可以使用OQL查找特定问题对象

6. 找到代码的问题

## 3.如何监控jvm（测试上线之前使用）

#### jconsole远程连接（jdk老的）

1. 程序启动加入参数：

   > ```shell
   > java -Djava.rmi.server.hostname=192.168.17.11 -Dcom.sun.management.jmxremote -Dcom.sun.management.jmxremote.port=11111 -Dcom.sun.management.jmxremote.authenticate=false -Dcom.sun.management.jmxremote.ssl=false XXX
   > ```

2. 如果遭遇 Local host name unknown：XXX的错误，修改/etc/hosts文件，把XXX加入进去

   > ```java
   > 192.168.17.11 basic localhost localhost.localdomain localhost4 localhost4.localdomain4
   > ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
   > ```

3. 关闭linux防火墙（实战中应该打开对应端口）

   > ```shell
   > service iptables stop
   > chkconfig iptables off #永久关闭
   > ```

4. windows上打开 jconsole远程连接 192.168.17.11:11111

#### jvisualvm远程连接(推荐)

https://www.cnblogs.com/liugh/p/7620336.html （简单做法）

#### jprofiler (收费)

#### arthas在线排查工具

- 为什么需要在线排查？ 在生产上我们经常会碰到一些不好排查的问题，例如线程安全问题，用最简单的threaddump或者heapdump不好查到问题原因。为了排查这些问题，有时我们会临时加一些日志，比如在一些关键的函数里打印出入参，然后重新打包发布，如果打了日志还是没找到问题，继续加日志，重新打包发布。对于上线流程复杂而且审核比较严的公司，从改代码到上线需要层层的流转，会大大影响问题排查的进度。 
- jvm观察jvm信息
- thread定位线程问题
- dashboard 观察系统情况
- heapdump + jhat分析
- jad反编译 动态代理生成类的问题定位 第三方的类（观察代码） 版本问题（确定自己最新提交的版本是不是被使用）
- redefine 热替换 目前有些限制条件：只能改方法实现（方法已经运行完成），不能改方法名， 不能改属性 m() -> mm()
- sc - search class
- watch - watch method
- 没有包含的功能：jmap