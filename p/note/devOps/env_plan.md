# 环境规划：

1.linux查看登录信息
https://www.jianshu.com/p/f060ecca9c81

2.A10报错问题
window service2012 clock watchdog timeout

3.seo优化
https://www.bilibili.com/video/av31630462/?redirectFrom=h5

### 一）.操作系统
​	1.linux(推荐)
​		服务器:centos(推荐)
​			https://www.centos.org/
​		桌	面:ubuntu(推荐)
​			https://ubuntu.com/
​			

	2.windows(当前)
		服务器:windows_server_2012
		桌	面:windows7,windows10
		
	创建usb引导工具:
	可以参考ubuntu,centos官方文档,在不同平台创建usb引导
	https://docs.centos.org/en-US/8-docs/standard-install/assembly_preparing-for-your-installation/#create-bootable-usb-linux_preparing-for-your-installation
	https://ubuntu.com/tutorials/tutorial-create-a-usb-stick-on-windows#2-requirements
		1>on windows
			create windows usb：
				ultraiso(收费的)
					https://cn.ultraiso.net/
			create linux usb：
				rufus(免费,window,linux IOS都可以创建):
					https://rufus.ie/
					https://github.com/pbatard/rufus/releases/download/v3.11/rufus-3.11.exe
		2>on linux(ubuntu-desktop)
			create windows usb:
				https://github.com/slacka/WoeUSB
					sudo add-apt-repository ppa:nilarimogard/webupd8
					sudo apt update
					sudo apt install woeusb
				实例:
				https://www.linuxidc.com/Linux/2017-10/148157.htm
				https://blog.csdn.net/qq_45440355/article/details/103179459
			create linux(ubuntu) usb:
				在ubuntu上搜索`Startup Disk Creator`(只能创建ubuntu IOS)
				
			linux下可以直接使用dd命令写入ios镜像
			fdisk -lh	//查看u盘物理路径(/dev/sdb)
			dd if=/home/xxx/xxx.iso of=/dev/sdb

### 二）.虚拟机
​	1.linux(基于linux内核的KVM)：
​	

	2.windows(基于windows内核的Hyper-V)：
	
	3.OS系统之上的
		1>.VMware-workstation
		2>.virtualbox

### 三）.存储
​	RAID,mdadm,LVM(存储管理)
​	https://mck.baidu.com/v2309244-151568-556501.html
​	

	1.硬件Raid
		raid0：条带(将数据分布式存储在阵列数据盘上,读写性能高,没有冗余能力,最少2个硬盘(2N偶数个))
		raid1：镜像(将数据分布式镜像拷贝到其它盘上,硬盘利用率只有50%,最少2个硬盘)
			   能坏一个盘
		raid2：校验冗余(把数据分散为位或块，加入汉明码，间隔写入到磁盘整列中(对数据做异或运算))
			   最少3个盘,采用并行存储，花费大成本昂贵,能坏一个盘
		raid3：数据被分为更小的块并行传输到各个磁盘上，同时计算XOR校验数据存放到专用的校验磁盘上
			   最少3个盘(瓶颈在一个校验盘读写上),能坏一个盘
		raid4: 数据被分为更大的块并行传输到各个成员磁盘上，同时计算XOR校验数据存放到专用的校验磁盘上
				独立校验冗余(与raid3比,校验迅速，更高读取速度,控制器设计更加复杂),能坏一个盘
		raid5：采用独立阵列存储，校验信息被均匀的分散到各个磁盘上,能坏一个盘
		raid6: 是指带有两种分布存储的校验信息的磁盘阵列,它是raid5扩展，主要要求数据绝对不能出错的场合，
			   使用了两种奇偶校验方法，需要N+2个磁盘,可以坏两个盘
		raid7:  是一种比较新的raid标准，扎实的实时操作系统，不占用主机cpu，独立运行
		
		常用的组合:
		RAID	10(01也可以,但生产一般使用10,容错高)
		RAID	50
		
	2.软Raid
	   依赖现有操作系统,md模块(实现Raid机制命令管理)
	   1>命令mdadm:模式化的命令
				-C
				-A
				-F
	   2>dm模块(device mapper),比md复杂
		 也支持Raid功能,包括常用的LVM（logical volume manager）
	3.逻辑区扩容LVM
		逻辑区动态扩容,不停机情况动态扩容
### 四）.网络
​	3.ip子网划分?
​	chain_block?
​	

### 五）.资源托管

​	1.代码
​		github
​		gitee
​	2.js库 
​		npm
​	3.jar库
​		maven
​	4.docker镜像
​		阿里云
​		腾讯云
​	5.有道云 / 百度云

# unix与linux介绍：

### 一.unix两大分支

| system V家族                                                 | bsd家族                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| A/UX  ▪ AIX （IBM） ▪ HP-UX  ▪ IRIX  ▪ LynxOS  ▪ SCO OpenServer  ▪ Tru64  ▪ Xenix  ▪Solaris(SUN) ▪ OS/2 | BSD/OS  ▪ FreeBSD  ▪ NetBSD ▪ NEXTSTEP  ▪Mac OS X  ▪ iOS ▪ OpenBSD  ▪ SUN OS （sun） ▪ OpenSolaris |

**unix诞生于1969年**

| bsd发行版     | 描述                |
| ------------- | ------------------- |
| FreeBSD       | 侧重稳定            |
| OpenBSD       | 侧重安全            |
| NetBSD        | 侧重多平台          |
| DragonFly BSD | 2001年发起的bsd项目 |
| Mac OS        | 苹果系统            |
| BSD / OS      | ???                 |
|               |                     |

1.BSD家族大观：FreeBSD、OpenBSD、NetBSD

https://blog.csdn.net/sahusoft/article/details/3195594

2.为什么要使用BSD系统

https://www.linuxprobe.com/why-use-bsd.html

**bsd安装软件源方法：**

​		1.package(bsd通常用这种)

​		2.ports

https://blog.csdn.net/zhangdaisylove/article/details/46379947

http://blog.chinaunix.net/uid-29727170-id-4509832.html

## GNU项目

建立一个自由开放的unix操作系统(操作系统应用层软件集)

unix与linux跑的应用大部分都可以相互移植，因为都遵循了POSIX标准

GNU(一个自由的操作系统)与FSF(自由软件基金)

**1983年发起的自由软件集协作计划**

### 二.linux

linux为了兼容unix，参照了POSIX标准，linux通常指linux内核
Linux = gnu	+	linux kernel

**linux内核1994年1.0发布**

## 常见linux发行版：

| 家族          | 发行版            | 描述                                                  |
| ------------- | ----------------- | ----------------------------------------------------- |
| redhat家族    |                   |                                                       |
|               | redhat enterprise | 企业版（收费）                                        |
|               | fedora            | 红包上游版本                                          |
|               | centos            | 红帽下游版本，稳定                                    |
|               | rockylinux        | centos替代品                                          |
|               |                   |                                                       |
| suse家族      |                   |                                                       |
|               | suse              | 企业版(收费)                                          |
|               | openSUSE          | 德国人系统，社区开源版                                |
|               |                   |                                                       |
| debian家族    |                   |                                                       |
|               | debian            | 开源社区                                              |
|               | ubuntu            | debian的分支                                          |
|               |                   |                                                       |
| archlinux家族 |                   |                                                       |
|               | archlinux         | linux高度定制化(主要基于二进制定制),2002年3月11日发行 |
|               |                   |                                                       |
| gentoo        |                   | linux高度定制化(主要基于源码定制)，2002年3月31日发行  |
| Slackware     |                   | 古老的linux发行版，简洁，安全，稳定                   |
| PCLinuxOS     |                   | ？？？                                                |
|               |                   |                                                       |
|               |                   |                                                       |

1.archlinux gentoo选择

https://www.cnblogs.com/huapox/archive/2012/03/01/3299990.html

https://blog.csdn.net/neyes/article/details/6129027/

2.手动做LFS	???