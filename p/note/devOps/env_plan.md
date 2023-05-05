# 环境规划：

<br/>
[二.grub使用](#grubuse)<br/>
[三.分区MBR与GTP](#mbr_gtp)<br/>
		1).分区概念<br/>
		2).分区工具<br/>
		3).分区规则<br/>
		4).分区使用<br/>
[四.虚拟机与存储](#vir_store)<br/>
		1).虚拟机<br/>
		2).存储<br/>
[五.winToGo,linuxToGo](#wintogo)<br/>
		1).winToGo<br/>
		2).linuxToGo<br/>
		3).wintogo(mac本，普通机器)<br/>
​		4).问题<br/>

# 一.linux系统分类
<div id="unix_linux"/>
### 1.unix两大分支

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
| minix         | 基于微内核unix系统  |
|               |                     |
|               |                     |

1).BSD家族大观：FreeBSD、OpenBSD、NetBSD

https://blog.csdn.net/sahusoft/article/details/3195594

2).为什么要使用BSD系统

https://www.linuxprobe.com/why-use-bsd.html

3).bsd安装软件源方法

​		*.package(bsd通常用这种)

​		*.ports

https://blog.csdn.net/zhangdaisylove/article/details/46379947

http://blog.chinaunix.net/uid-29727170-id-4509832.html

### 2.GNU项目

建立一个自由开放的unix操作系统(操作系统应用层软件集)

unix与linux跑的应用大部分都可以相互移植，因为都遵循了POSIX标准

GNU(一个自由的操作系统)与FSF(自由软件基金)

**1983年发起的自由软件集协作计划**

### 3.常见linux发行版

linux为了兼容unix，参照了POSIX标准，linux通常指linux内核
Linux = gnu	+	linux kernel

**linux内核1994年1.0发布**

| 家族          | 发行版            | 描述                                                  |
| ------------- | ----------------- | ----------------------------------------------------- |
| redhat家族    |                   |                                                       |
|               | redhat enterprise | 企业版（收费）                                        |
|               | fedora            | 红包上游版本                                          |
|               | centos            | 红帽下游版本，稳定                                    |
|               | rockylinux        | centos替代品                                          |
|               |                   |                                                       |
| Slackware家族 |                   |                                                       |
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

1).archlinux gentoo选择

https://www.cnblogs.com/huapox/archive/2012/03/01/3299990.html

https://blog.csdn.net/neyes/article/details/6129027/

2).手动做LFS	???

### 4.当前主力系统
```
1.linux(推荐)
		服务器:centos
			https://www.centos.org/
		服务器:centos(替代品)
			https://rockylinux.org/
			https://almalinux.org/
		桌	面:ubuntu(推荐)
			https://ubuntu.com/

2.windwos(当前)
		服务器:windows_server_2012
		桌	面:windows7,windows10,windows11
				百度搜索“windows11”下载（提供相应的usb工具）
		usb制作工具（windows）:
		https://zhuanlan.zhihu.com/p/551730189
			电脑店：http://dnd.lzlcm.cn/
			大白菜：http://dbc.nxexvq.cn/
```
### 5.windows(开源dos系统)

	https://github.com/FDOS/kernel
	https://github.com/FDOS/freecom
	http://www.freedos.org/
# 三.分区MBR与GTP
<div id="mbr_gtp"/>
### 1.分区概念（ MBR分区概念）

###### 常见文件系统格式：

 fat32:早期windows系统格式，单个文件大小不能超过4G 
 ntfs：windows替代fat32的文件系统，单个文件可以超过4G
 exFAT：适合优盘，exFAT可以支持更多设备和操作系统,它比FAT32支持的单个文件大
 ext4: linux系统默认文件系统
**主分区：**
主分区也叫引导分区，是系统安装的分区,如windows的c盘，linux的`/`挂载目录，一个硬盘只能建4个主分区(包括扩展分区)

**扩展分区：**
扩展分区是一个概念，实际在硬盘中是看不到的，也无法直接使用扩展分区，扩展分区下可以划分`逻辑分区`

**逻辑分区：**
扩展分区上可以建多个逻辑分区，是一块存储介质，和操作系统，主分区没什么关系，是`独立的`

**激活分区：**
一个硬盘最多可以有4个主分区，你可以在4个主分区上都安装系统，而系统从硬盘启动启动时，并不知道你打算从哪个分区启动系统。所以，需要将其中一个设为“活动”属性

### 2.分区概念2（ GPT分区概念）
相比于MBR模式,GPT模式拥有对2TB以上的原生支持。GPT不存在扩展分区和逻辑分区,所有分区全都是主分区,且理论上可存在的主分区个数是无限的。

```
MBR与GPT(uefi)分区的区别:
http://www.360doc.com/content/18/0901/23/11935121_783145790.shtml

一个硬盘安装多个linux(MBR分区)(Centos7):
https://www.cnblogs.com/youxia/p/linux018.html

linux分区工具：
https://www.lxlinux.net/6469.html
https://gparted.org/
硬盘GPT分区与MBR分区的区别
https://xinzhi.wenda.so.com/m/a/1542627025209463
新版本fdisk可以识别gpt格式分区
https://www.zhangweijie.net/post/2017/11/12/2906.html
```

### 3.usb工具

	浏览器操作系统：
	   https://github.com/DustinBrett/daedalOS.git
	
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
				
		3>linux下可以直接使用dd命令写入ios镜像
			fdisk -l	//查看u盘物理路径(/dev/sdb)
			dd if=/home/xxx/xxx.iso of=/dev/sdb status=progress

### 3.分区工具

```
windows:
https://www.diskgenius.cn/download.php		//常用的分区工具(推荐)
	https://www.disktool.cn/download.html		//这个要关注才行

linux:
https://gparted.org		//分区工具，分区前要设置MBR,GPT分区
```

###### fdisk格式化：

```
1）查看分区:
fdisk -l

一般u盘插上去
u盘设备：/dev/sdb或/dev/sdc
设备分区:/dev/sdb1或/dev/sdc1

2）fdisk分区:
fdisk /dev/sdb 
d[删除分区]，n[新增分区]，a[激活分区]，w[保存并退出]

d 删除所有分区(保证是一块干净的分区)

n 新增主分区(只能存放4个分区，包含主分区，扩展)
	p primary(主分区,mbr,引导信息存放区)
		Command (m for help): n	//新增主分区
		Partition type
		   p   primary (0 primary, 0 extended, 4 free)
		   e   extended (container for logical partitions)
		Select (default p):  //按回车默认

		Using default response p.
		Partition number (1-4, default 1): //按回车默认
		First sector (2048-15728639, default 2048): //按回车默认
		Last sector, +sectors or +size{K,M,G,T,P} (2048-15728639, default 15728639): 7864319 //自定义大小(偏移量计算) 或者输入 +30G(分配30G大小)
		
		//15728639(u盘总大小),分配主分区大小7864319(7864319-2048=7862271)

	e extended(扩展分区(逻辑分区可以有多个,多个逻辑分区之和就是扩展分区))
		Command (m for help): n  //新建扩展分区
		Partition type
		   p   primary (1 primary, 0 extended, 3 free)
		   e   extended (container for logical partitions)
		Select (default p): e //选扩展分区
		Partition number (2-4, default 2): //按回车默认
		First sector (7864320-15728639, default 7864320): //按回车默认
		Last sector, +sectors or +size{K,M,G,T,P} (7864320-15728639, default 15728639): //按回车默认,把剩下空间全分配为扩展分区

		Created a new partition 2 of type 'Extended' and of size 3.8 GiB.

		Command (m for help): n //新增逻辑分区
		All space for primary partitions is in use.
		Adding logical partition 5
		First sector (7866368-15728639, default 7866368): //按回车默认
		Last sector, +sectors or +size{K,M,G,T,P} (7866368-15728639, default 15728639): 9830399 //自定义大小(偏移量计算) 或者输入 +30G(分配30G大小)
		//9830399(分配逻辑分区大小9830399-7866368=1964031)
		Created a new partition 5 of type 'Linux' and of size 959 MiB.
		
		注意：
		First sector (2048-15728639, default 2048)表示:
		总大小:15728639
		默认开始量:2048
		First sector：表示设置分区的开始地址(通常按回车默认)
		Last sector：最后分配的大小是由指定的Last sector结束地址决定的
		分配总大小计算设置:结束地址-开始地址
	
a 激活主分区
	一般最后需要激活分区
	
w 保存并退出(推出后需要看是否有设备繁忙错误信息,否则编辑的分区信息会不生效)


3）格式化为FAT32:
要先卸载u盘
umount /dev/sdb

格式化
mkfs.vfat -F 32 /dev/sdb1
mkfs.vfat /dev/sdb1

注意:
是分区号/dev/sdb1,而不是/dev/sdb
```

### 4.分区规则

因为一个硬盘mbr分区最多只能分4个主分区，包括扩展分区(扩展分区可以有多个)，所以安装多个操作系统时分区方案如下：

| **系统os**                                          | **分区**       |
| --------------------------------------------------- | -------------- |
| windows                                             | C:/  (主分区1) |
| centos                                              | /（主分区2）   |
| ubuntu(**需要最后安装ubuntu,grub才能全部引导**)     | /（主分区3）   |
| 扩展分区                                            | 扩展分区4      |
| /boot(同一台机器centos,ubuntu需要划分不同/boot分区) |                |
| swap分区(共享)                                      |                |
| /home(共享)                                         |                |
| .....                                               |                |

980G（A10机器）

1.centos7(500G)

2.ubuntu20(300G)

3.windows10(180G)

#### 5.分区使用

```
1.debian安装问题
http://www.wellcms.cn/read-140.html
--------------------------------------
2.磁盘实际使用情况
A10
/		总共173.3GB		可用166.7GB		使用6.6GB
/boot 	总共4.2GB		可用3.6GB		使用311.9MB		
/home	总共804.9GB		可用529.2GB		使用275.7GB

A4
/		总共164.2GB	可用134.3GB		使用21.5GB
/boot	总共8GB		可用7.4GB		使用179.0Mb
/home	总共301.7GB	可用169.7GB		使用116.6GB
```
# 二.grub使用

<div id="grubuse"/>

```
https://www.gnu.org软件包子项目
grub官方网站:https://www.gnu.org/software/grub/

UEFI引导+GPT分区的方式
https://wiki.archlinux.org/index.php/GRUB_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)
https://wiki.archlinux.org/index.php/GRUB_(简体中文)
https://wiki.archlinuxcn.org/wiki/分区#分区表
混合 UEFI GPT + BIOS GPT/MBR 启动
https://wiki.archlinuxcn.org/wiki/多引导USB设备#简单安装
参考：
https://www.ngui.cc/zz/2401587.html?action=onClick
https://blog.csdn.net/WMX843230304WMX/article/details/90620168
https://blog.nanpuyue.com/2016/033.html
https://www.jianshu.com/p/00a216480183
windows系统下：
http://www.taodudu.cc/news/show-4612466.html
windows下分区工具
https://www.diskgenius.cn/download.php

一。grub制作USB启动盘

1、使用 GParted 分区U盘：
分区 500M , 并且设置标识 boot esp , fat32格式
第二个分区剩余全部，ntfs格式（可以读写大于4G的文件）

终端输入命令fdisk -l 查看结果：
终端输入命令blkid 查看结果：

2.扇区末尾1M空间使用命令 gdisk 打上标记 bios_grub ,格式化方式——未格式化
sudo gdisk /dev/sdb
GParted查看结果

boot esp 标记支持 EFI 启动
bios_grub 标记支持 BIOS 启动

3.安装grub到U盘
	3.1） 挂载U盘分区 /dev/sdb1 到 /mnt:
	sudo mount /dev/sdb1 /mnt
	3.2）grub安装到MBR
	sudo grub-install --target=i386-pc --recheck --boot-directory=/mnt/boot /dev/sdb
	3.3）grub安装到ESP,特别注意--removable参数，安装到移动设备上一定要用这个参数
	sudo grub-install --target x86_64-efi --efi-directory /mnt --boot-directory=/mnt/boot --removable

特别注意:
Ubuntu的grub默认的target是i386-pc，这个target包含在grub-pc这个包，如果你的系统使用BIOS+MBR安装，这个包默认是存在的。x86_64-efi这个target包含在grub-efi这个包，只有你的系统使用UEFI+GPT方式安装这个包才会存在。如果某个target报错，错误信息类似于grub-install: error: /usr/lib/grub/x86_64-efi/modinfo.sh doesn’t exist. Please specify --target or --directory. 这样的话，就需要安装grub-pc或grub-efi之后再试。
安装到ESP要加–removable这个参数 这个参数专门针对于可移动设备，一定要加
bios_grub标记的分区 如果按照上面的分区过程操作了，增加了这个标记的分区，安装grub的时候会自动识别这个标记的分区并成功安装grub，否则报错:
Installing for i386-pc platform.
grub-install: warning: this GPT partition label contains no BIOS Boot Partition; embedding won't be possible.
grub-install: warning: 无法嵌入。在此次安装中 GRUB 只能通过使用块列表安装。但是块列表是不可信赖的，不推荐使用。.
grub-install：错误： will not proceed with blocklists.

4.添加grub菜单
sudo grub-mkconfig -o /mnt/boot/grub/grub.cfg
然后基于你自己的电脑和U盘修改 /mnt/boot/grub/grub.cfg

5.提取ubuntu16.04lts.iso 镜像到 /dev/sdb2
启电脑，选择U盘启动，大功告成
```

### 1.windows(usb安装)

```
直接下载二进制可执行文件exe包,进入到命令目录下：

windows 下安装grub
https://blog.csdn.net/qq_42748849/article/details/81273086


1.安装grub i386-pc命令（用于传统bios启动）
wmic diskdrive list brief	//查看U盘物理设备(\\.\PHYSICALDRIVE1)
grub-install.exe --target=i386-pc --boot-directory=f:\ --recheck \\.\PHYSICALDRIVE1

安装grub x86_64-efi命令（用于UEFI方式启动）貌似普通电脑引导启动不了,格式不对
grub-install.exe --target=x86_64-efi --efi-directory=f: --boot-directory=f: --removable
--recheck                        #如果存在磁盘映射，将删除磁盘映射

2.grub-install.exe --boot-directory=f:\ --efi-directory=f: --removable --target=x86_64-efi
3.grub-install.exe --boot-directory=f:\ --efi-directory=f: --removable --target=i386-efi
--removable   #安装装置是可移动设备。此选项仅适用于EFI。安装多系统可以不用，可移动设备必须用此参数

EFI、UEFI、MBR、GPT的区别???
https://www.cnblogs.com/ricksteves/p/9800002.html
https://www.aioboot.com/en/install-grub2-from-windows/

EFI在linux中的安装

linux安装grub efi
https://www.jianshu.com/p/0ebf640c29bb
https://www.cnblogs.com/callmelord/p/11420457.html

grub-install --target=i386-efi --efi-directory=/home/liyuan/usb --bootloader-id=grub
```

### 2.linux(ubuntu安装)

```
网络参考：
https://www.ngui.cc/zz/2401587.html?action=onClick
一。安装grub-2.0(需root权限):
    1）解压grub-2.00.tar.gz
    2）安装所支持：
        sudo apt-get install flex bison（运行此命令即可）
        如下deb包：
        bison_2.4.1.dfsg-3_i386.deb
        flex_2.5.35-10ubuntu1_i386.deb
        m4_1.4.16-1_i386.deb(只需要运行)
    3）编译及安装(进入解压后的目录) 
      ./configure --prefix=/home/liyuan/grub
      make install
	4）还可以使用apt安装
		？？？
二。写入u盘并进行引导
安装grub到u盘(进入grub安装根目录/sbin,执行下面命令)：
1）安装grub到u盘：
    新建usb挂载目录
    cd /home/liyuan
    mkdir usb
    mount /dev/sdb1 usb		//挂载u盘

2)安装grub
    //注意用的是/dev/sdb而不是/dev/sdb1
    ./grub-install --root-directory=/home/liyuan/usb /dev/sdb
    或者
    ./grub-install --force --no-floppy --root-directory=/home/liyuan/usb /dev/sdb

3)可以从源码grub-2.02/docs拷贝grub.cfg模板文件到u盘的/boot/grub目录下面进行修改

4)还可以直接新建配置文件grub.cfg到u盘的/boot/grub/下面，u盘启动盘就制作完成了
    ubuntu-16.04-desktop-amd64.iso u盘目录:			G:\boot 
    initrd-4.12.9 bzImage u盘目录: 					G:\linux4.12.9	

#
# Sample GRUB configuration file
#
# Boot automatically after 30 secs.
set timeout=30
# By default, boot the GNU/Linux
set default=gnulinux
# Fallback to GNU/Hurd.
set fallback=gnuhurd
# For booting GNU/Linux
menuentry 'My Linux 4.12.9' --class ubuntu --class gnu-linux --class gnu --class os {
	set root='(hd0,msdos1)'
	linux	/linux4.12.9/bzImage 
	initrd	/linux4.12.9/initrd-4.12.9
}
menuentry 'My ubuntu-16.04 iso' --class ubuntu --class gnu-linux --class gnu --class os {
	loopback loop (hd0,msdos1)/boot/ubuntu-16.04-desktop-amd64.iso
	linux (loop)/casper/vmlinuz.efi boot=casper iso-scan/filename=/boot/ubuntu-16.04-desktop-amd64.iso
	initrd (loop)/casper/initrd.lz
	boot
}

ubuntu-16.04 iso具体文件可以查看iso压缩文件
```

### 3.grub.cfg配置

```
windows grub2
https://blog.csdn.net/guyixiangxy/article/details/81005539

menuentry "Windows 7 (loader) (on /dev/sda1)" --class windows --class os {
	insmod part_msdos
	insmod ntfs
	set root='(hd0,msdos1)'
	search --no-floppy --fs-uuid --set=root 6CB6992BB698F6B4
	chainloader +1
}

menuentry "Boot Windoze" {
  search -f "--set-root /Winblows.iso"
  insmod memdisk
  linux16 /boot/memdisk iso
  initrd16 /Winblows.iso
}
```

### 4.老版本grub

```
NOTE:
update-grub:
更新本机grub文件

查看磁盘uuid:
ls -l /dev/disk/by-uuid

进入grub命令行(可以进行手动引导,可以尝试一下)

可以了解一下这种方式的功能
[root@linux: ~] # dd if=/home/liyuan/up/boot/grub/stage1 of=/dev/sdb bs=512 count=1
[root@linux: ~] # dd if=/home/liyuan/up/boot/grub/stage2 of=/dev/sdb bs=512 seek=1

fdisk分区
http://blog.csdn.net/wangzengdi/article/details/28109907
----------------------------------------------------------------------------------------------------
编译安装出现的问题error: ‘gets’ undeclared here (not in a function):
解决办法：
1.stdio.in.h找到这个文件
2.修改_GL_WARN_ON_USE (gets, "gets is a securityhole - use fgets instead");这行为如下:
#if defined(__GLIBC__) &&!defined(__UCLIBC__) &&!__GLIBC_PREREQ(2, 16)
_GL_WARN_ON_USE (gets, "gets is a security hole -use fgets instead");
#endif
```

# 四.虚拟机与存储
<div id="vir_store"/>
###	1.虚拟机
​		1).linux(基于linux内核的KVM)

​		2).windows(基于windows内核的Hyper-V)

​		3).OS系统之上的
​			1>.VMware-workstation
​			2>.virtualbox	

### 2.存储

###### 1).RAID,mdadm,LVM(存储管理)
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

###### 2).数据盘格式化及挂载：

```
参考：
https://www.cnblogs.com/root0/p/9269140.html

1>fdisk /dev/sdb	分区
2>mkfs.xfs /dev/sdb1	格式化
3>mount /dev/vdb1 /data	//新建data目录，并挂载
4>echo "/dev/sdb1 /data xfs defaults 0 0" >> /etc/fstab	//启动自动挂载
```

###### 3).数据盘挂载及云盘扩容：

```
参考：
https://v.youku.com/v_show/id_XMjM1Njc2NjI2NA==.html?

1>查看磁盘挂载情况：df -TH
2>新建一个数据：echo "abc" >test.txt
3>打快照（防止扩容失败，可以回滚）
4>扩容操作,支付(阿里需要重启，点击实例按钮重启)
5>fdisk -l	(查看磁盘容量，扩容后的)
6>df -TH	(查看分区信息，旧的)
7>umount /dev/xvdb1 停服务，卸载磁盘（umount -lf强制卸载）
8>fdisk /dev/xvdb	//重新分区（d（删除），n（新建分区），p（主分区），分区号，分区开始，分区结束（默认），w(写入)）
9>e2fsck  -f  /dev/xvdb1	检查分区
10>resize2fs  /dev/xvdb1	  扩容命令
11>mount  /dev/xvdb1  /data	挂载	(df -TH查看)
12>/etc/fstab	//开机自动挂载
```

# 五.winToGo,linuxToGo

注意：
	1.如果虚拟磁盘文件是vdi文件，需要把文件添加后缀名.vtoy，否则启动时文件看不到
	2.在Blos中先在Security->Secure Boot->Secure Boot->选择了Disabled

引导（最终方案）：
	制作wintogo为了在同一移动存储设备上既能引导windows，又能引导linux，外设需要格式化为ntfs格式（exfat不能引导windows）
		windows直接写入外设(Mac本，普通机器) > 用wintogo制作的vhd文件（如果成功的话，可以挂载到VirtualBox分析gtp分区？？？）
		ubuntu >  VirtualBox制作的vhd格式（固定大小vhd，动态大小貌似有问题），最后需要加.vtoy后缀
		windows > VirtualBox制作的vhd格式（固定大小vhd，可以动态大小），vhdx貌似有问题
		可以试试vhdx格式是否可以引导？？？（vbox推荐vhdx动态大小）

工具：百度云盘：云硬盘>env>public>wintogo

###### VHD快照???

```
Vhd快照操作：
	vhd-util是个linux下的开源软件，通过封装libvhd库函数，提供vhd镜像相关操作。例如可用于创建vhd镜像、快照等功能。
	分支仓库
	https://github.com/microsoft/azure-vhd-utils
	VirtualBox：
	VBoxManage modifyhd --resize <size in MB>
	https://www.oomake.com/question/4372630

	qemu-img：
	https://www.cnblogs.com/yanfen/p/14777232.html
	qemu-img resize <path-to-raw-file> size
```

### winToGo（手动vhd）

```
普通U盘快速安装Wintogo教程
https://www.bilibili.com/read/cv17650897
《详细》使用ventoy制作linux to go和win to go放在u盘中《即走即用的便携式系统》，自带系统引导
https://blog.csdn.net/qq_45366830/article/details/129612844

【Ventoy】开源
https://www.ventoy.net/cn/index.html
https://github.com/ventoy/vhdiso/releases/download/v3.0/ventoy_vhdboot.zip

【Wintogo辅助工具】写入install.wim
https://github.com/nkc3g4/wtg-assistant			//先进的install.esd镜像写不了，需要install.wim(单独写这个文件到优盘驱动装不上去)

set1:
	鼠标右键“我的电脑”，选择"管理"->“计算机管理”选择存储“磁盘管理”->选中一个磁盘，右上角“磁盘管理->更多操作”->下拉选“创建VHD”(选择是否固定大小，并选择虚拟磁盘文件保存路径)
	
set2:
	使用wtg-assistant工具，选择windows ISO文件写入set1步骤创建的VHD文件
	
set3:
	插入优盘，使用ventoy制作u盘启动工具，windows貌似要把优盘格式化为ntfs(fat32)
	
set4:
	4.1.优盘根目录下创建ventoy目录，下载ventoy_vhdboot.zip把ventoy_vhdboot.img文件copy到ventoy目录下。
	4.2.再把写入的VHD文件拷贝到优盘根目录下，至此wintogo优盘启动制作完成
```

###### esd转wim
1.install.esd转install.wim
https://blog.csdn.net/qq_35229961/article/details/103024144
	1.1）esd decrypter工具（esd转wim工具）
	https://github.com/Chuyu-Team/Dism-Multi-language
	1.2）wintogo工具选install.wim就可以直接安装

### linuxToGo（vbox创建）

```
【VirtualBox】
需要用到VirtualBox

【Ventoy】开源
https://www.ventoy.net/cn/index.html

【Wintogo辅助工具 】
https://github.com/ventoy/vtoyboot

set1:
	首先使用VirtualBox创建虚拟机与VHD磁盘，并安装好linux系统至VHD。
	
set2:
	进入VirtualBox安装好的linux系统，下载vtoyboot并解压，执行如下：
	$ sudo bash vtoyboot.sh

set3:
	3.1）插入优盘，使用ventoy制作u盘启动工具，优盘使用ntfs格式(为了在同一个优盘既能引导windows,linux),因为优盘exfat格式引导windows貌似不行
	3.2）把VirtualBox创建的虚拟系统VHD文件添加.vtoy后缀，否则文件识别不了还有引导报错
	3.3）然后把VirtualBox创建的虚拟系统VHD文件COPY到优盘根目录，至此wintogo优盘启动制作完成
```

### wintogo直接写入外设(mac本，普通机器)

工具下载(直接写入)：https://www.disktool.cn/wintogo.html

mac机器开机从优盘引导：开机按住option

mac机器开机安全设置(从外设引导需要密码)：开机按住command + R

```
1.下载wintogo工具
https://www.disktool.cn/wintogo.html

2.安装好后可以选择给“mac设备”,"windows设备（一般机器）"制作wintogo
选择mac设备，需要准备好windows安装镜像，优盘
注意：
现在貌似只有cn_windows_10_enterprise_x64_dvd_6846957.iso可以，在“windows之家”下载的都是ghost的，貌似不行

3.选择"mac设备"可以在mac(intel)引导，也可以在普通机器上引导
```

###### mac制作wintogo需要支持的windows驱动包获取

```
一。使用mac做wintogo移动启动，需要下载apple提供的windows驱动
mac安装windows
https://www.iqiyi.com/v_1rshytlyxvc.html

1.进入mac系统，进入“启动转换助理”
如果mac硬盘空间不够，可以准备并格式化一个移动优盘，格式为MS-DOS(FAT)
然后启动“启动转换助理”，进入点击菜单“操作”，再点击“下载windows支持软件”并选择存储的位置（选移动优盘）

2.等待完成后，mac下载的符合当前机器的驱动程序就下载到了选择的 移动优盘里面
Wintogo制作好，通过Mac引导进入windows会有一些驱动没装上包含wifi,安装下载的驱动即可
驱动已下载：百度云盘/liyuandf:云硬盘>env>driver>WindowsSupport.zip

二。apple官网参考
首先进入苹果官网>菜单栏选“技术支持”>搜索内容中点进“在 Mac 上下载并安装 Windows 支持软件”
下载到usb
https://support.apple.com/zh-cn/HT204923
https://support.apple.com/zh-cn/guide/bootcamp-assistant/welcome/mac
```

### 待解决

```
1.使用网上下载的最新镜像windows_10_professional_x64_2023.iso安装到优盘

2.grub引导vhd

3.vhd快照操作
```

### 问题

###### 1.windows使用问题

```
1.系统默认问题
windows10开启administrator登陆
https://zhidao.baidu.com/question/818645681578524892.html
net user administrator /active:yes

2.Win10系统打开应用时提示“无法使用内置管理员账户打开”
https://blog.csdn.net/qq_63852611/article/details/126354682
https://zhuanlan.zhihu.com/p/575842781
https://soft.zol.com.cn/jingyan/5_all.html
	1）.cmd下运行“gpedit.msc”并点击“确定”
	在组策略编辑界面中，依次展开“Windows设置”-“安全设置”-“本地策略”-“安全选项”

	2）.在右侧找到“用户帐户控制：用于内置管理员账户的管理员批准模式”项并右击，从其右键菜单中选择“属性”项以打开“属性”窗口
	将“本地安全策略”栏目中的“启用”项进行勾选，并点击“确定”以保存退出

3.百度网盘错误提示 “驱动器中没有磁盘 请在驱动器 D:中插入磁盘”
https://pc.geren-jianli.org/k8Dkv62zb/
https://product.pconline.com.cn/itbk/software/dnwt/1703/8914464.html
	3.1)打开磁盘管理器：右键点“计算机”-“管理”-“磁盘管理器”，进入磁盘管理器，找到出问题的盘符
	3.2)禁用此盘：右键点击“属性”，找到“驱动程序”标签项，点击“禁用”。即可解决此问题。
	
4.驱动精灵安装不上去（问题）
	4.1.应用程序无法启动,因为应用程序的并行配置不正确。有关详细信息，请参阅应用程序事件日志，或使用命令行sxstrace.exe工具.
	https://baijiahao.baidu.com/s?id=1697276343632123335&wfr=spider&for=pc
	4.2.安装Visual studio 2008 里面的VC_x86Runtime.exe
	https://www.bbsmax.com/A/gGdXK3nE54/
```

###### 2.引导盘后windows非常慢(磁盘100%)

```
一。解决windows10启动磁盘满问题
https://zhidao.baidu.com/question/1998384507575390547.html
https://www.xitongtang.com/class/win10/14664.html
https://www.xtzjcz.com/pc/246594.html
https://baijiahao.baidu.com/s?id=1740747857499279147&wfr=spider&for=pc
https://jingyan.baidu.com/article/f71d603793621f5bb741d100.html
https://www.160.com/article/4081.html
http://www.65ly.com/a/19/1671432446437592.html

services.msc（禁用）

“HomeGoup Listener”，“HomeGroup Provider”
家庭组是占用硬盘的原因之一。有用户反映在关闭家庭组后，硬盘占用率从90%降到10%左右，但对没加入家庭组的用户来说，这个方法也许无法解决问题。
退出windows10家庭组？？？
https://jingyan.baidu.com/article/9225544654b02cc41748f45f.html

Superfetch
是为传统的机械式磁盘设计，它允许内存以极快的速度载入一部分常用程序运行所需的数据片段，从而提升系统和程序运行速度。

Windows Update
windows更新

Windows Defender？？？
Windows Defender会定期扫描硬盘，在过去版本中，这项功能可以被关闭，但是升级Win10预览版9860后却无法关闭定期扫描功能，导致硬盘占用率居高不下，系统整体卡顿。用户可以尝试使用组策略限制Defender的定时扫描或关闭Defender来解决问题。
在搜索栏输入gpedit.msc打开组策略编辑器，定位到“计算机配置-管理模板-Windows组建-Windows Defender-扫描”中的“指定每周的不同天运行计划扫描”配置为“已启用”根据帮助中的内容选择设置。或直接在Windows Defender设置内在管理选项中将其关闭。

打开以太网，取消ipv6(勾取消掉)？？？
https://www.xitongtang.com/class/win10/14664.html
```

###### 3.U盘变成只读,无法格式化,怎么办?

```
U盘变成只读,无法格式化,怎么办?
https://www.disktool.cn/content-center/remove-write-protection-from-usb-1016.html

1.修改注册表
https://zhidao.baidu.com/question/1828014833204420148.html
添加注册表信息：
reg add HKLM\SYSTEM\CurrentControlSet\Control\StorageDevicePolicies /v WriteProtect /t REG_DWORD /d 00000000
如果想再次保护U盘不被写入，可以执行下面的命令：
reg add HKLM\SYSTEM\CurrentControlSet\Control\StorageDevicePolicies /v WriteProtect /t REG_DWORD /d 00000001

2.DiskPart命令
▪list disk - 将显示所有连接到计算机的磁盘，包括内部硬盘驱动器。
▪select disk n - 其中n是U盘的驱动器号。你可以使用大小来确定哪一个是它。
▪attributes disk clear readonly - 更改USB的属性，删除只读属性。

3.分区助手(重新分区)
https://www.disktool.cn/download.html
```

