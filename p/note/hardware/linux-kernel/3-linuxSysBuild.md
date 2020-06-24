# 3.嵌入式Linux内核制作

```
一.嵌入式Linux内核制作
描述：
嵌入式linux系统由linux内核 与 根文件系统两部分构成，两者缺一不可.

x86与ok6410对比安装:
	·清除原有的配置与中间文件
		1)x86:make distclean
		2)arm:make distclean
	·配置内核
		1)x86:make menuconfig 
		2)arm:make menuconfig ARCH=arm //会把arm芯片的都加载上来
	·编译内核
		1)x86:make bzImage
		2)arm:make uImage ARCH=arm CROSS_COMPILE=arm-linux-  //编译

最后编译好的内核在 linux内核根目录/arch/<cpu>/boot/uImage下面

注意：s3c6410的编译可以参考s3c6410的官方文档,本文档只是把原理流程工作列出来
-----------------------------------------------------------------------------
			二.根文件系统制作
描述(文件系统的两层含义)：
	·按照固定格式组织的目录与文件(看的见)
	·在特定的存储介质上管理这些目录和文件需要的数据(看不见)

制作文件系统步骤：
	·创建目录和文件
	·为创建好的目录和文件建立管理数据

创建目录和文件:
	·创建根文件系统目录
		1)mkdir rootfs
		2)cd rootfs
		3)mkdir bin dev etc lib proc sbin sys usr mnt tmp var
		4)mkdir usr/bin usr/lib usr/sbin lib/modules
	·创建设备文件
		1)cd dev
		2)mknod -m 666 console c 5 1
		3)mknod -m 666 null c 1 3
	·安装etc
		etc里面的文件通常是配置文件,通常是从x86,其它嵌入式设备里面copy，通用的。
		解压备份好的目录命令tar xvzf etc.tar.gz -C /xxx/rootfs
	·内核模块安装
		1)模块编译
		make modules ARCH=arm CROSS_COMPILE=arm-linux-
		2)模块安装
		make modules_install ARCH=arm INSTALL_MOD_PATH=/xxx/rootfs
	·Busybox
		相当于linux中的瑞士军刀,在嵌入式中包含许多有用的命令
		配置:make menuconfig(配置需要的命令与工具)
		1)设置为静态连接:
		Busybox Settings--->
				 Build Options--->
					   Build Busybox as a static binary(no shared libs)(选中)
					   Cross Compiler prefix(指明编译Busybox的前缀:arm-linux-)或者在编译时候指明一下这个参数
		2)Busybox Settings--->
				Installation Options --->
				Don't use /usr(选中)
			Busybox installation prefix(填上/xxx/rootfs,也是文件系统的目录)
		3)编译：make 
		如果前面Cross Compiler prefix没指定，需要如下编译
		make CROSS_COMPILER=arm-linux-
		4)最后安装:make install
-----------------------------------------------------------------------------
			三.嵌入式文件系统
描述：
	linux文件系统(VFS)支持多种文件系统,ext2,ext3,vfat,jffs,romfs和nfs。VFS为众多的文件系统提供了一个统一的接口(VFS).

文件系统结构:fsjg.jpg

常用嵌入式文件系统:
	嵌入式文件系统是根据应用需求与场合决定的,主要的存储设备有RAM和FLASH.FLASH又可以分NOR()和nand()两种.
	·JFFS或JFFS2
	用于NOR型FLASH,基于MTD驱动层，特点是，可读写，支持数据压缩的日志型文件系统,并提供了崩溃/掉电安全保护等，确点是 当文件已满时，因为收集的关系而使JFFS2的运行速度大大放慢。
	·yaffs或yaffs2
	专为NAND型FLASH设计的一种日志型文件系统，与jffs2比减少了一些功能(如不支持数据压缩),所以熟读更快，挂载时间更短,另外它还是跨平台的文件系统,除了Linux和eCos，还支持WINCE,PSOS和Threadx等。
	·cramfs
	即可以用在NOR，也可以用在NANDFLASH上,只读的,基于MTD驱动层,在运行时解压缩，所有的应用程序要求拷贝到RAM里去运行。另外它的速度效率高，其只读特点有利于保护文件系统免受破坏。
	·ramdisk/Initramfs(Initramfs取代ramdisk)
	ramdisk把一部分固定大小的内存当做块设备使用，它并非一个实际的文件系统，Initramfs与ramdisk不同的是，它是动态扩展内存的设备，比ramdisk使用更灵活。
	·NFS
	网络文件系统，在实际嵌入式过程中很少使用，经常使用NFS进行嵌入式开发测试。NFS可以通过网络进行加载文件。


内核文件系统制作：
	·nfs
		1)配置:make menuconfig ARCH=arm
		1>File systems --->
			 Network File System --->
				   Root file system on NFS(选上)
		2>Boot options
			 Default kernel command string(里面填上如下:)
			 1.视频上的:[root=/dev/nfs nfsroot=192.168.1.103:/root/soft/rootfs ip=192.168.1.222 rw console=ttySACO mem=256M]
			 2.s3c6410:root=/dev/nfs nfsroot=192.168.1.103:/root/soft/rootfs ip=192.168.1.222:192.168.1.103:192.168.1.1:255.255.255.0:witech.com.cn:eth0:off console=ttySAC0,115200
		或者手动配置.config(修改CONFIG_CMDLINE="")
		2)编译:make uImage ARCH=arm CROSS_COMPILE=arm-linux-
		最后编译好的在：linux内核根目录/arch/arm/boot/uImage 下面

	·Initramfs(把文件系统编译到内核里面)
		配置:make menuconfig ARCH=arm
		1)General setup
				Initial RAM filesystem and RAM disk(选中)
				Initramfs source file(s) 编辑(文件系统路径rootfs)
		2)编译前需要创建busybox软连接:ln -s ./bin/busybox init
```

