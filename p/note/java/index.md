# java介绍

## [环境安装](env/index.md)

## 一．基础知识

[1.jvm](jvm/index.md)

[2.多线程高并发](juc/index.md)

[3.设计模式](design23/index.md)

[4.算法与数据结构](dsa/index.md)

[5.netty](netty/index.md)

[6.spring](spring/index.md)

[7.spring_cloud微服务](spring/spring_cloud.md)

[笔记](java_mark.md)

## 二．系统方案

**1.尚硅谷2020最新版SpringCloud(H版&alibaba)**

https://www.bilibili.com/video/BV18E411x7eT?p=1

源码：

https://github.com/TNoOne/cloud2020，https://blog.csdn.net/qq_41211642/article/details/104772140

https://github.com/leelovejava/cloud2020

nacos,rabbitmq,sentinel,seata,consul,erlang等课程需要的软件及jar包都在这
链接：https://pan.baidu.com/s/18zr_KFYgme6ZI04VZZ4KtA 
提取码：dzvq

**2.mybatis代码生成器比较：**

https://blog.csdn.net/m0_37524586/article/details/88351833

**3.jmeter(并发测试工具)**

number。。。:一次发X次请求

Ramp。。。。：0（点击压测开始一刹那发X次请求）或数字为 n（表示n秒要发X次请求）

Loop。。。。：表示循环上面步骤多少次

**4.io（文档整理）**

流的分类（按照不同角度进行分类）：

1》.按照流的方向：输入流，输出流【站在程序角度】

2》.按照处理数据单位：字节流(InputStream,OutPutStream)，字符流（Reader,Writer）

3》.按照功能：节点流（一个管道直接怼在数据源上），处理流（套在其他管道上的流为处理流）

4.》*转换流(套接流,编码转换）

5》.*数据流ByteArrayOutputStream,DataOutputStream

6》.*print打印流（只有输出流,不会抛异常）printWriter,printStream

7》.*Object流（Object写入或读出，序列化）,transient修饰的成员变量序列化时不与考虑

serilizable:序列化，jdk控制序列化过程

Externalizable：自己控制序列化过程

**5.时间介绍（UT/UTC/GMT的区别和联系）**

https://blog.csdn.net/xujiezhige/article/details/8215186

**6.视屏格式转码（ffmpeg）**

[http://www.ffmpeg.org](http://www.ffmpeg.org/)

http://www.ruanyifeng.com/blog/2020/01/ffmpeg.html

音视频开发

https://www.bilibili.com/video/BV16N411d7ih/

视频格式转换

https://www.zhihu.com/question/351214031/answer/873651210

**7.分布式数据库（postgresql）**

[**http://postgresql.org**](http://postgresql.org/)

数据库排名网站

https://db-engines.com/en/ranking