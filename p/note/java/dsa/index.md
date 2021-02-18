# 数据结构与算法

链接: 
https://pan.baidu.com/s/18y3CKb6a5N6f8DCpUH1Pbw 提取码: hnms

视屏课程:
https://www.bilibili.com/video/BV1B4411H76f?from=search&seid=8541134073371747563

当前（总：195，当前：29）

https://www.bilibili.com/video/BV1B4411H76f?p=16

## 一．数据结构与算法

### 内容介绍

应用场景（程序）　＝ 	数据结构	＋　算法

### 一．数据结构分类

#### 线性结构：

数组，队列，链表和栈

```
特点是数据元素之间存在一对一的线性关系

有两种不同存储结构，顺序存储（存储元素是连续的），链式存储（元素不一定是连续的，元素存储相邻的元素地址信息）
```

#### 非线性结构：

二维数组，多维数组，广义表，**树结构，图结构**

1.稀疏数组(五子棋例子)

实例：dsa.dataStructures.sparsearray

2.队列

实例：dsa.dataStructures.queue

简单队列：ArrayQueueDemo.java

```
数组模拟队列一个思路，但有问题（数组不能重复使用）
```

环形队列：CircleArrayQueueDemo.java

```
改进成环形队列（通过取模方式）。算法可以有多种，但这种数据有效个数是lenth-1(因为有个预留空间)
```

3.链表

链表方式：
分为，有头链表，无头链表两种

实例：dsa.dataStructures.linkedlist

单向链表：

SingleLinkedListDemo.java

链表排序，添加，删除

面试题：

```
1>求单链表中有效节点个数
2>查找单链表中的倒数K个结点
3>单链表反转(数据结构反转)
4>从头打印单链表(不改变链表数据顺序，使用到了栈Stack)
```

双向链表：

DoubleLinkedListDemo.java

双向链表显示，添加，删除，修改

单向环形链表（约瑟夫问题）

Josepfu.java

数组取模也可以完成（环形链表）



### 二．算法分类

