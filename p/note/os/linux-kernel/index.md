# linux内核

#### [笔记](explore.md)

## 一．基础

[1.kernelBasis](1-kernelBasis.md)

 内核模块编译，内核模块开发 	

[2.uboot](2-uboot.md)

基础，uboot编译\，uboot命令 	

[3.linuxSysBuild](3-linuxSysBuild.md)

嵌入式内核制作，根文件系统制作，嵌入式文件系统 

4.memManage

 内存管理，进程地址空间，内核地址空间，linux内核链表，linux内核定时器 	

5.procManage linux

进程控制，Linux进程调度，Linux系统调用实现，proc文件系统，内核异常分析 	

6.charDriver LINUX

驱动程序介绍，字符设备驱动程序设计，虚拟文件系统VFS，竞争与互斥(驱动调试技术) 	

7.highChar Ioctl

设备控制，内核等待队列/阻塞型字符设备驱动，poll设备方法，自动创建设备文件 	

8.hdOperation mmap

设备方法，硬件访问，混杂设备驱动

## 二．方案

[1.ok6410开发板烧写](ok6410-arm.md)

2.grub+linux源码编译+u盘制作最小启动盘

文件系统＋网卡驱动＋用户登录模块

## 三。linux内核编译及在qemu调试运行

```
1.安装内核编译依赖库文
    sudo apt install -y ncurses-dev
    sudo apt install -y flex
    sudo apt install -y bison
    sudo apt install -y libssl-dev
    sudo apt install -y libelf-dev
    # 一条命令解决
    sudo apt install -y ncurses-dev flex bison libssl-dev libelf-dev

2.配置.config
	make defconfig	//会生成.config文件

打开调试选项
    # debug
    CONFIG_DEBUG_INFO=y
    CONFIG_READABLE_ASM=y
    CONFIG_DEBUG_SECTION_MISMATCH=y

编译
	make -j 4	//查看核心  cat /proc/cpuinfo

生成的内核在 
	linux-6.7.5/arch/x86_64/boot/bzImage

3.编译busybox
    https://busybox.net/

    make mrproper
    make menuconfig		//在settings里面找到Build static binary选项，按y选上
    make -j 4
    make install

    生成的程序在
    busybox-1.36.0/_install/
    把busybox生成物放到前面建立好的目录run/busybox下面
    cp busybox-1.36.0/_install/bin/busybox initramfs/bin/busybox

4.编写init脚本
    #!/bin/busybox sh
    /bin/busybox mkdir -p /proc && /bin/busybox mount -t proc none /proc
    /bin/busybox echo "Hello Linux"

    /bin/busybox sh

    给init权限
    chmod 777 init

5.成果物存放目录run
run
   bzImage		
   initramfs
      bin
         busybox
   	  init
   initramfs.img
   Makefile
   
6.制作文件系统
   文件系统类型:
      initramfs(内存文件系统)
      Disk image（磁盘文件系统，ext4,jffs2...）
      Network filesystem(NFS文件系统，属于网络文件系统) 
   制作脚本(initramfs)：
      cd ./initramfs && find . -print0 | cpio -ov --null --format=newc | gzip -9 > ../initramfs.img

7.使用qemu运行
    ubuntu安装：
    sudo apt install qemu-system-x86

运行命令:
    qemu-system-x86_64 \
    -kernel bzImage \
    -initrd initramfs.img \
    -m 1G \
    -nographic \
    -append "earlyprintk=serial,ttyS0 console=ttyS0"

8.放到Makefile里面
.PHONY: initramfs

initramfs:
	cd ./initramfs && find . -print0 | cpio -ov --null --format=newc | gzip -9 > ../initramfs.img

run:
	qemu-system-x86_64 \
		-kernel bzImage \
		-initrd initramfs.img \
		-m 1G \
		-nographic \
		-append "earlyprintk=serial,ttyS0 console=ttyS0"
		
运行命令
make run

9.进入后执行命令
$ busybox ls
$ busybox pwd
```

## 四。创建ext4文件系统

```
参考：
https://www.bilibili.com/video/BV1UV411j71C
https://www.bilibili.com/read/cv11271232

1） 编译好内核
文件在：linux-4.9.229/arch/x86_64/boot/bzImage

2）编译好busybox-1.30.0
文件目录默认在（文件系统）：busybox-1.30.0/_install
bin	linuxrc  sbin  usr

3）完善文件系统（直接在busybox-1.30.0/_install目录完善）
# mkdir etc dev mnt
# mkdir -p proc sys tmp
# mkdir -p etc/init.d/

4）挂载文件系统
# vi etc/fstab 
proc        /proc           proc         defaults        0        0
tmpfs       /tmp            tmpfs    　　 defaults        0        0
sysfs       /sys            sysfs        defaults        0        0 

5）系统启动时执行
 # vi etc/init.d/rcS
echo -e "Welcome to tinyLinux"
/bin/mount -a
echo -e "Remounting the root filesystem"
//重新挂载根文件系统变为可读可写
mount  -o  remount,rw  /
mkdir -p /dev/pts
//处理热插拔
mount -t devpts devpts /dev/pts
echo /sbin/mdev > /proc/sys/kernel/hotplug
mdev -s

6）给权限
chmod 755 etc/init.d/rcS
	   
7）编辑inittab
# vim etc/inittab
::sysinit:/etc/init.d/rcS
::respawn:-/bin/sh
::askfirst:-/bin/sh
::ctrlaltdel:/bin/umount -a -r

8）给权限
# chmod 755 etc/inittab

9）创建设备
# cd dev
# mknod console c 5 1
# mknod null c 1 3
# mknod tty1 c 4 1

10）打成gzip包(制作成内核能识别的文件系统)
    #!/bin/bash
    rm -rf rootfs.ext3
    rm -rf fs
    //1.先制作一个空的镜像文件
    dd if=/dev/zero of=./rootfs.ext3 bs=1M count=32
    //2.然后把此镜像文件格式化为ext3格式
    mkfs.ext3 rootfs.ext3
    mkdir fs
    //3.然后把此镜像文件挂载，并把根文件系统复制到挂载目录
    mount -o loop rootfs.ext3 ./fs
    cp -rf ./_install/* ./fs
    //4.卸载该镜像文件
    umount ./fs
    //5.打成gzip包
    gzip --best -c rootfs.ext3 > rootfs.img.gz

11）启动系统
# qemu-system-x86_64 \
  -kernel ./linux-4.9.229/arch/x86_64/boot/bzImage  \
  -initrd ./busybox-1.30.0/rootfs.img.gz   \
  -append "root=/dev/ram init=/linuxrc"  \
  -serial file:output.txt
  
描述：
/linuxrc是内核运行起来后，转交给文件系统的第一个执行文件，一般来讲是init文件，但在busybox中是linuxrc
```

## 五。创建nfs文件系统

https://www.bilibili.com/video/BV1Wa411h7Xh

```
1) 搭建nfs服务器
# apt-get install nfs-kernel-server
//修改配置文件
# vi /etc/exports
/mnt/rootfs *(rw,sync,no_root_squash)
# 把之前创建好的文件目录COPY到/mnt/rootfs
cp -rfa ../busybox-1.30.0/_install /mnt/rootfs
# 启动服务
/etc/init.d/nfs-kernel-server start
# 验证(挂载验证)
show mount -e
mount -t nfs 192.168.119.131:/mnt/rootfs /home/liyuan/fs
	
2) linux内核配置与编译(???)
    # make menuconfig
    进入到（配置网络支持）:
    Networking support -> Networking options -> TCP/IP networking
    下面三项都勾选上
    [*]: kernel level auto configuration
    [*]   IP: DHCP support 只勾选此项
    [*]   IP: BOOTP support 只勾选此项
    [*]   IP: RARP support
    进入到（配置NFS支持）:
    File systems -> Network File Systems
    <*>    NFS Client support
    <*>      NFS Client support for NFS version 2
    <*>      NFS Client support for NFS version 3
    [*]        NFS Client support for the NFSv3 ACL protocol extension
    <*>      NFS Client support for NFS version 4
    [*]	 Root file system on NFS

# make //编译	

3）qemu启动(???)
# qemu-system-x86_64 -kernel ./linux-4.9.229/arch/x86_64/boot/bzImage \
-append "init=/linuxrc root=/dev/nfs rw nfsroot=192.168.119.131:/mnt/rootfs,v3,tcp \
ip=192.168.119.132:192.168.119.131:192.168.119.1:255.255.255.0::eth0:off" -serial file:output.txt
描述：
ip=ip1:ip2:ip3:255.255.255.0
 ip1:内核启动给内核分配的ip
 ip2:网关地址
视频中ip2是不是多写了一段ip,本应该是ip=ip1:ip2:255.255.255.0？？？？？

GRUB_CMDLINE_LINUX="root=/dev/sda nfsroot=<NFS服务器IP地址>:<共享目录路径> ip=dhcp"
网络参考：
qemu-system-x86_64 -kernel  c:/Users/nwz/Desktop/temp/bzImage -append "root=/dev/nfs rw nfsroot=192.168.5.24:/home/nwz/nfs/two,vers=3,tcp ip=dhcp console=ttyS0" -nographic -net nic -net tap,ifname=tap0

https://blog.csdn.net/weixin_33649132/article/details/112931226
```

## 六。linux内核源码阅读方式

```
linux内核阅读方式
	1.使用vscode+clangd远程精准浏览linux kernel源码 与 clangd+vscode+codelldb
		https://blog.csdn.net/oushaojun2/article/details/129877412
		https://zhuanlan.zhihu.com/p/675453558
	2.vim+ctags：使用vim编辑器并搭配ctags插件，可以很方便地浏览和跳转代码，支持语法高亮和代码折叠
	3.Eclipse：Eclipse是一个非常强大的IDE，可以使用它来阅读内核源码。Eclipse可以通过插件添加代码导航、高亮、语法检测等功能。
	4.KDevelop：KDevelop是一个专门为C++开发设计的IDE，支持代码补全、高亮、跳转等功能，非常适合阅读内核源码。
	5.GNU Global：GNU Global是一种代码导航工具，可以在整个源代码中搜索函数、变量等定义和引用，并提供跳转功能。

vscode+clangd阅读linux源码
https://blog.csdn.net/weixin_40209493/article/details/128175003
下载最新版clangd
https://zhuanlan.zhihu.com/p/659343853?utm_id=0
Vscode安装clangd插件
 
下载安装clangd并配置path环境变量
https://github.com/clangd/clangd/
https://github.com/rizsotto/Bear

clangd-mac-snapshot_20240128.zip
clangd_indexing_tools-mac-snapshot_20240128.zip

vscode+clangd远程阅读linux源码
https://www.bilibili.com/video/BV1184y117Uj

vscode插件Remote Development

问题解决
https://codeleading.com/article/51175752633/
sudo apt-get install bc
------------------------------------------vscode+clangd+远程阅读linux源码
1.首先安装vscode
https://code.visualstudio.com/

2.vscode安装clangd插件
vs里面搜clangd安装,还可以安装如下插件
Arm Assembly
Chinese
Hex Editor
Remote Development(vscode远程ide插件，见如下6)

3.本机安装clangd(解压安装需要配置path环境变量)
https://github.com/clangd/clangd/
sudo apt-get install clangd
clangd --version

4.本机安装bear
https://github.com/rizsotto/Bear
sudo apt-get install bear
bear --version

5.下载linux源码并解压
https://www.kernel.org/

生成索引
sudo apt install -y ncurses-dev flex bison libssl-dev libelf-dev
//清理所有产生的文件与config配置文件，并且编辑过的与补丁文件
make distclean

bear -- make defconfig	//.cofnig
bear -- make -j 4 		//编译内核并生成linux内核索引文件

6.安装vscode远程ide插件Remote Development
	确认本机有ssh命令:
       6.1 windows平台可以安装Git-2.40.0-64-bit.exe（里面包含ssh命令，配置path）
       6.2.配置ssh命令环境变量
        C:\Users\liyuan\Desktop\soft\dev\git\usr\bin

------------------------------------------vim+ctags方式阅读linux源码

```



