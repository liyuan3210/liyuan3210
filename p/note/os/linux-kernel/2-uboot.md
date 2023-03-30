# 2.uboot编译

```
一.Bootloader介绍
嵌入式软件层次：
	·boot(bootloader)
	包括固化在固件中的boot(bios),和bootloader，bootloader相当于grub。
	注意：在嵌入式系统中没有boot(bios),所有职责都由bootloader完成
	↓
	·linux内核 >
	通过boot来加载linux内核
	↓
	·文件系统
	最后是文件系统

pc机流程:
	·首先是由固化在pc中bois(固件程序)和grub和LILO一起组成
	↓
	·bois完成硬件的检测和资源分配后，将硬硬盘中的引导程序读到系统内存中
	↓
	·然后将控制权交给引导程序，将内核从硬盘读取到内存中，然后跳转到内核的入口点(即启动内核)

bootloader安装：
系统加电或复位后，所有的cpu通常都从cpu制造商预先安排地址开始执行。
比如,s3c6410在复位后从地址ox00000000起开始执行。
而嵌入式系统则将固态存储设备(比如:flash)安排在这个地址上，
而bootloader程序又安排在固态存储器的最前端，这样就能保证在系统加电后，cpu首先执行bootloader程序

bootloader两种启动流程：
	·单阶段
	·多阶段
	大多数bootloader采用两阶段的，也就是多阶段。
	1>stage1:完成硬件初始化，为stage2准备内存空间，并将stage2复制到内存中，设置堆栈,跳到stage2中。(通常汇编语言编写)
	2>stage2:也就是c入口处，初始化本阶段要使用到的硬件设备，将内核文件与根文件系统从flash拷贝到ram中，最后调用内核.(通常c语言编写)

内存分布:
RAM地址空间
stage1为stage2可执行映像准备的RAM地址范围,大小1MB	←堆栈指针sp:stage2_end-4
............
............

Flash地址空间
............(blank)
ramdisk
............(blank)
内核映像
............(blank)
bootloader的stage2可执行映像,可能的最大大小:64kb	←0x0000,0400(64kb)
                       			      ←0x0000,0400(1kb)
bootloader的stage1可执行映像,可能的最大大小:1kb	←0x0000,0000

bootloader工作模式：
	·启动模式:用户所在的模式
	·下载模式:仅对开发人员有意义的模式
----------------------------------------------------------------------------------------------------
			二.交叉工具链
安装arm-gcc:
	·安装工具链tar xvzf arm-linux-gcc-4.5.1 -C /要解压的目录
	·修改/etc/profile添加为：pathmunge=arm-gcc安装目录/bin 或 path=arm-gcc安装目录/bin
	·让第二步修改的生效: source /etc/profile

arm-gcc使用:
	·arm-linux-gcc(常用的编译工具):arm-linux-gcc hello.c -o hello

	·arm-linux-objdump(反汇编工具):arm-linux-objdump -D -S hello

	·arm-linux-readelf(ELF文件查看工具)
	1)arm-linux-readelf -a hello
	2)arm-linux-readelf -d hello 查看hello使用的动态库

	注意：使用arm-linux-objdump -D -S hello >log.txt:可以输出到log文件里面
----------------------------------------------------------------------------------------------------
			三.uboot
描述：
uboot是嵌入式领域应用最广的，是德国DENX小组开发爱的用于多种嵌入式CPU（MIPS,X86，ARM,XScale等）的bootloader程序，uboot不仅支持linux的引导还支持VxWorks，QNX等多种嵌入式操作系统

官网:http://www.denx.de
下载地址：ftp://ftp.denx.de/pub/u-boot

uboot目录结构：
	·board:和开发板有关的文件，每一个开发板都以一个子目录，如board/samsung/smdk6410
	·common:实现uboot支持的命令
	·cpu:与特定cpu架构相关的代码，arm920t，arm1176jzf等
	·disk:对磁盘的支持
	·doc:文档
	·drivers:uboot支持的设备驱动程序
	·fs:文件系统的支持
	·include:uboot使用的头文件
	·net:与网络协议相关的代码
	·tools:uboot的工具，如mkimage,crc等

u-boot.bin编译：
	·选择要使用的开发板
	make smdk6410_config //其中ok6410 执行 make forlinux_nand_ram256_config
	·编译生成u-boot.bin
	make CROSS_COMPILE=arm-linux- //其中ok6410 执行 make

	编译好后有个u-boot.bin在源码跟目录下面

网上直接下载编译的uboot是不能运行在ok6410开发版的,需要针对指定开发版进行uboot移植

uboot命令很多，真正开发不可能全部编译进进去
（具体选择编译命令要在uboot移植里面讲）
----------------------------------------------------------------------------------------------------
			四.uboot命令使用
帮助(列出现在开发板支持的命令):help

环境相关命令：
	·printenv:查看当前系统环境变量
	·setenv:添加，修改，删除环境变量
	1)添加,修改:setenv names value
	2)删除:setenv names
	·saveenv:保存环境变量
	注意：如果只使用setenv进行当前添加与修改,它只是存在内存中的，需要用saveenv进行保存一下,下一次重启机器后才不会丢掉.

设置自动启动:setenv bootcmd tftp c0008000 uimage \;bootm c0800000

文件下载:
	·tftp:使用tftp下载(环境，服务端必须设置好):tftp c0800000 uimage

内存操作：
	·md :显示内存的内容
	1)md [.b,.w,.l] address
	[.b,.w,.l]是长度标示符，可选,address是地址
	·mm:修改内存,地址自动递增
	1)mm [.b,.w,.l] address
	[.b,.w,.l]是长度标示符，可选,address是地址

Flash on 6410:
	·nand info:显示nand flash信息
	·bdinfo:显示开发板信息如内存地址和大小，时钟频率，mac地址等
	·device:打印当前nand设备
	·nand erase [1000] [2000]:擦除1000至2000地址的数据，没有这两个参数表示擦除整块falsh
	·nand write[e,uboot] [内存地址] [flash地址] leng
	从[内存地址]写入数据至[flash地址] leng大小的数据
	·nand read [内存地址] [flash地址] leng
	从nand[flash地址]大小leng，读出数据到[内存地址]
	·nand scrub [off] [size]
	没有两个参数表示檫除整块nandflash,比erase相比，此命令檫除更彻底，并会重新检查坏块,将坏块表存储到nand的指定位置
	·go addr [arg...]
	执行内存中的二进制代码，一个简单的跳转到制定地址
	·bootm [addr [arg...]]执行内存中二进制代码
	要求二进制有固定的格式的文件头
```

