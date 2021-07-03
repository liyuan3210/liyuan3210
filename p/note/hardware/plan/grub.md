# grub引导制作

## 一．grub下载

```
https://www.gnu.org软件包子项目
grub官方网站:https://www.gnu.org/software/grub/
```

## 二.usb安装

### 1.windows安装

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

grub介绍
https://wiki.archlinux.org/index.php/GRUB_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87)


linux安装grub efi
https://www.jianshu.com/p/0ebf640c29bb
https://www.cnblogs.com/callmelord/p/11420457.html

grub-install --target=i386-efi --efi-directory=/home/liyuan/usb --bootloader-id=grub
```

### 2.ubuntu安装

```
安装grub-2.0(需root权限):
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
```

### 老版本grub

```
安装grub到u盘(进入grub安装根目录/sbin,执行下面命令)：
1）安装grub到u盘（：
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

### grub.cfg配置

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

## 三．格式化与分区

### 分区概念：

```
分区概念（ MBR分区概念）
1.主分区：
主分区也叫引导分区，是系统安装的分区,如windows的c盘，linux的`/`挂载目录，一个硬盘只能建4个主分区(包括扩展分区)
2.扩展分区：
扩展分区是一个概念，实际在硬盘中是看不到的，也无法直接使用扩展分区，扩展分区下可以划分`逻辑分区`
3.逻辑分区：
扩展分区上可以建多个逻辑分区，是一块存储介质，和操作系统，主分区没什么关系，是`独立的`
4.激活分区：
一个硬盘最多可以有4个主分区，你可以在4个主分区上都安装系统，而系统从硬盘启动启动时，并不知道你打算从哪个分区启动系统。所以，需要将其中一个设为“活动”属性


MBR与GPT(uefi)分区的区别
http://www.360doc.com/content/18/0901/23/11935121_783145790.shtml

```

**终分区方案：**

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

### fdisk格式化：

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