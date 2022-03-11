### 一。存储技术

1.硬件RAID卡

2.软Raid（依赖操作系统md模块，lvm）

3.分布式存储框架（FreeNAS，Lustre，Ceph，Gluster）

```
FreeNAS: 存储管理操作系统
Lustre:早期分布式存储技术
Ceph：支持存储有，块存储(云计算磁盘虚拟设备)，对象存储（key,value），文件系统
Gluster：相对Ceph比较简单，底层依赖lvm库
```

二。分布式存储原理

以hadoop的hdfs分布式文件系统为例进行讲解