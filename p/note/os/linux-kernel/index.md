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

