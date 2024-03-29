

## linux体系了解

```
Linux内核 0.11：
Linux内核查看工具
https://zhuanlan.zhihu.com/p/523591912
https://github.com/KrisJiaqiXie/linux_kernel_study.git

参考：
非常好的Linux内核视频
https://www.bilibili.com/video/BV1tQ4y1d7mo
https://www.bilibili.com/video/BV1tQ4y1d7mo?p=10
庖丁解牛Linux内核
https://www.bilibili.com/video/BV1yD4y1m7Q9
linux内核编程(基础,进阶,高级)
https://www.bilibili.com/video/BV1iJ411u7ps
uboot移植
https://www.bilibili.com/video/BV1s4411t7eT
https://www.bilibili.com/video/BV1Nr4y1F7Me
国内讲解linux0.11的三本书
https://zhuanlan.zhihu.com/p/550507987
研究linux kernel 0.11有哪些意义？
https://www.zhihu.com/question/65695598/answer/2633858858
```
#### linux内核汇编编译工具
```
见[图0.1]
```

图0.1
<img src="img/0/0.1.png" style="zoom:50%;" />


#### 体系结构

```
用户程序
｜
标准库
-------------------------
系统调用(服务层)
文件系统VFS	进程管理（与进程通信）
内存管理		驱动管理
-------------------------
硬件
```

#### 中断体系

```
中断目的：
	1.硬件中断响应
	2.系统调用的函数响应
	3.自定义中断
	4.信号中断（kill -signalnum）
	5.系统的异常和错误
中断分类：硬件中断，软件中断
	硬件中断：硬件控制芯片发出的
	软中断：异常错误，系统调用异常

sys_call服务层接口：
Kernel与arch文件夹？？？
Kernel/asm.s		trap.c
Kernel/system.call.s	fork.c	signal.c exit.c sys.c
```

#### 内核初始化

```
CPU内部有一个RTC,开机计算1970年1月1日0时开始所过的秒数。
给MKTIME函数传过来的时间结构赋值是由初始化RTC或coms中读出的参数，转换为时间存在全局变量中，
为jiffies所调用。

jiffies时一个系统的时钟滴答 一个系统滴答是10ms,定时器
	每隔10ms产生一个定时器中断，调用do_timer

cpl表示当前被中断进程所存储的 0表示是内核进程，1是用户进程
current->counter: 进程调度的时间片,???
```

#### 进程创建,调度,切换

###### 进程创建

```
task_struct
	堆栈
	TSS进程描述符（进程运行的过程中，cpu需要知道的进程状态标识）
	LDT(局部描述符，代码段，数据段)

系统级别GDT描述符（进程调度初始化）
	。。。
	进程3
	进程2
	进程1
	内核数据段
	内核代码段

内核态	不可抢占
用户态	可以抢占

Main.c
Fork->init(),创建fork 0号进程,所有进程都是0号进程子进程
最后把内核态切换到用户态
```

###### 进程调度

```
void schedule(void) 进程调度（哪个进程先要执行）
	counter,时间片
	priority,优先级
    switch_to(next) 进程切换函数（切换进程）

进程状态：
	运行状态		可以被运行，就绪，进程切换
	可中断睡眠状态	可以被信号中断
	不可中断睡眠状态	只能被wakeup所唤醒
	暂停状态
	僵死状态
```

###### 进程销毁

```
系统调用一般都是syscall,do_xxx
1.内核销毁
	1.1exit是销毁函数
		首先会释放进程的代码段，数据段
	1.2.关闭进程打开的所有文件，对当前的目录i节点进行同步
	1.3.当前要销毁的进程有子进程，就让x进程作为新的父进程
	1.4.如果当前进程是一个会话头进程，则会终止会话中的所有进程
	1.5 改变当前进程的运行状态为僵死，并向其父进程发送SIGCHLD信号

	2.1 父进程运行子进程时候 ，一般都会运行wait waitpid这两个函数
	2.2 父进程会把子进程的运行时间累加到自己的进程变量中
	2.3 把对应子进程的进程描述结构体进行释放，置空任务数组中的空槽
```

#### 引导与启动程序

```
BOIS BOOTLOADER
1.linux操作系统的引导
	1.1 linux是如何从硬盘读取的(bootloader紧密关系)
	1.2 linux启动的时候是如何拿到硬件参数的（bootloader怎么给linux传递参数的）
	1.3 linux在初始化运行中做了什么
		trap.c	trap_init()
		mktime.c time_init()
		sched.c. sched_init()

2.引导阶段
BOIS/BOOTLOADER:
	pc机器的BIOS(oxFFFF0是bios存储的总线地址)，把bootsect从某个固定的地址拿到了内存中的
某个固定地址（0x90000）,并且进行了一系列的硬件初始化 和 参数设置

bootsect.s:
	磁盘引导块程序，在磁盘的第一个扇区中的程序（0磁道，0磁头，1扇区）
	作用：首先将后续的setup.s代码从磁盘中加载到紧接着bootsect.s的地方
	在显示屏幕显示loading system ,再将system(操作系统)模块加载到0x10000地方
	最后跳转到setup.s中运行

setup.s:
	解析BOIS/BOOTLOADER传递来的参数
	设置系统内核运行的LDT(局部描述符号) IDT(中断描述符寄存器) 全局描述符号（设置全局描述符寄存器）
	设置中断控制芯片，进入保护模式运行（svc32保护模式，设置寄存器值）
	跳转到system模块的最前面的代码运行head.s

head.s:
	加载内存运行时的各数据段寄存器，重新设置中断描述符表
	开启内核正常运行时的协处理等资源
	设置内存管理的分页机制
	跳转到main.c开始运行
```

#### 初始化程序init()

```
1.初始化代码
	起点：磁盘引导程序，将内存放到内存进行运行，并初始化模块与硬件
	终点：运行到一个应用程序 系统的根文件系统

2.main.c里面的init()函数

3.linux移植
	* 进行操作系统初始化适配，能够让main在你的板卡上跑起来
	* 进行驱动的移植



4.Linux是怎么引导的
	linux3.4.2/arch/arm/kernel/head.s
	
5.bootloader启动内核如何进行的，在uboot(调用linux内核)
	do_bootm_linux函数里
	创建
	void (*theKernel)(int zero, int arch,uint params);
	把指针移到ep,linux的启动入口
	theKernel = (void(*)(int , int ,uint))ntohl(hdr->ih_ep);
	执行linux并传入参数
	uboot启动内核函数：theKernel(0,bd->bi_arch_number,bd->bi_boot_params)
	bd->bi_arch_number：称为process_id CPU架构号
	bd->bi_boot_params：参数地址
		
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
1.内核如何进行多平台的适配，在内核中是如何认识这些板子的？
2.内核启动的整体流程
3.认识一种高效的编程结构（代码段）	

head.s引导（uboot会传过来3个参数）
0，processor_id,taglist存放地址
｜
｜
｜
head-common.s跳到运行
__lookup_processor_type实现（__lookup_processor_type_data）
	__lookup_processor_type_data：	实现
	  .long .
	  .long __proc_info_begin
	  .long __proc_info_end
｜
｜
｜
__proc_info_begin与__proc_info_end体现在vmlinux.lds.s
VMLINUX_SYMBOL(__proc_info_begin) = .;
*(.proc.info.init)	//实现proc-arm1022.s,proc-arm1026.s等(arm内核指令集)
VMLINUX_SYMBOL(__proc_info_end) = .;


__mmap_switched: 将旧的地址转换为虚拟地址

最后跳转到start_kernel:
	b	start_kernel	//内核启动函数

关键地方：
../arch/arm/kernel/vmlinux.lds.s 链接脚本
.init.arch.info:{
	__arch_info_begin = .;
	*(.arch.info.init)		//代码段
	__arch_info_end = .;
}
｜
｜
｜
../arch/arm/include/asm/mach/Arch.h
#define MACHINE_START(_type,_name)
static const struct machine_desc __mach_desc_##_type
// ##_type上面的拼接machine_desc __mach_desc_“_type传过来的字符串”
｜
｜
｜
MACHINE_START用到的地方如下：
Mach-rx3715.c,Mach-smdk2410.c,Mach-smdk2413.c等等

machine_desc结构体：
用于linux做设备板子的识别结构体，这些结构体被限定了在内存的某一片区域
通过uboot传过来的参数进行该结构体的配置
在移植linux的时候 也要对结构体的变量进行赋值
在之后的启动或其它函数中 对该结构体变量进行调用
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>内核信号量
信号量：
	SIGHUP,SIGINT,SIGQUIT,....

kill -num 

信号，信号量是两个东西

do_signal函数是系统调用中断处理程序中的信号处理程序


系统调用的处理程序
对信号识别
调用do_signal函数将对应信号处理程序句柄插入到用户堆栈中
IRET(返回，用户程序继续执行)

```

#### 文件系统(vfs)

```
1.linux中使用文件系统几个部分
	1.1 关于linux中高速缓冲区的管理程序。分页机制，buffer.c
	1.2 文件系统的底层通用函数（对硬盘的读写，分配，释放，对于目录的节点管理inode,内存与磁盘的映射）
	1.3 对文件数据进行读写操作模块（VFS：虚拟文件系统，硬件驱动 和 文件系统的关系，pipe块设备等）
	1.4 文件系统与其他程序的接口实现（fopen 关闭 创建等使用文件的调用方式）

2.文件系统的基本概念
	磁盘中要有目录映射，我们把磁盘分成盘片
	每一个盘片都有一个文件系统的子系统

	数据结构：
	引导块：用来引导设备的，引导块可以为空
	超级块：是该文件子系统的描述符
	逻辑块位图：其每一位对应一个逻辑块的使用情况，对应逻辑块如果使用了则逻辑块对应位置为1
	i节点位图：其每一位对应一个i节点的使用情况，对应i节点如果使用了则对应i节点位图上位置为1
	逻辑块：用来存储数据的数据存储单元
	i节点：目录 与 磁盘的桥接 文件的属性描述

一个最小文件系统需要什么？
	1. /dev/console
	2.init_main函数---->busybox
	3./etc/init.d/rcS----->脚本
	4.因为需要运行shell命令，所以需要shell命令支持函数---->busybox
	5.busybox响应函数运行必须要标准库函数的支持----->glibc
```

#### 内存管理

```

```