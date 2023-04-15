一.服务端linux虚拟机(kvm)<br/>
    1).虚拟机介绍<br/>
		1.1.主要厂商虚拟技术<br/>
		1.2.虚拟磁盘文件<br/>
		1.3.虚拟文件格式的转换及操作<br/>
​    2).qemu-kvm安装<br/>
​    3).qemu-kvm常用命令<br/>
​	4).qemu网络<br/>
​	5).桥接bridge-utils安装配置<br/>
[二.桌面虚拟机](#vmware_virtualbox)<br/>
​	1).vmware<br/>
​	2).virtualbox<br/>
​	3).笔记本创建wifi热点与笔记本直连<br/>
[三.linux远程桌面连接](#remote_desktop)<br/>
​    1).远程桌面介绍及方案<br/>
​    2).centos远程桌面安装配置<br/>
​    3).问题<br/>
​    4).其它远程桌面方<br/>

# 一.服务端linux虚拟机(kvm)

### 1).虚拟机介绍

###### 1.1.主要厂商虚拟技术

```
hypervisor centos
	一种运行在物理服务器和操作系统之间的中间层软件，可以允许多个操作系统和应用共享一套基础物理硬件
	https://blog.51cto.com/yangshufan/2130043
	https://blog.csdn.net/u013946404/article/details/78739830
	https://blog.csdn.net/bbc955625132551/article/details/71597863

一.微软的hyper-v
	微软的虚拟机，windows内核级别支持，需要开启才可用。
	
二.linux开源的KVM,Xen,VirtualBSD等
	1.).KVM
		核心支持是KVM（内核级支持），常配合qemu来用
		qemu,qemu-kvm,kvm区别???
		qemu:操作系统之上的虚拟平台(支持多cpu架构)，但性能比较差
		kvm：linux内核级别虚拟化技术，性能高，通常要配合用户程序qemu来使用
		qemu-kvm：用户管理使用qemu,底层运行基于kvm
		https://blog.csdn.net/u011619480/article/details/127719913
		https://blog.51cto.com/u_15786574/5669932
	2.).Xen
	3.).VirtualBSD
	
三.夸平台桌面
	1).vmware桌面
	2).VirtualBox桌面
	3).qemu
		支持多种架构，操作系统之上的

hyperv vmware hypervisor 
	https://cloud.tencent.com/developer/article/1041633
```
###### 1.2.虚拟磁盘文件
```
一。主要虚拟磁盘文件

1）.raw
	老牌的格式了,性能上来说的话还是不错的,如果其它格式需要转换，有时候还是需要它做为中间格式，但会比较消耗网络带宽和I/O

2）.qcow2
	qcow2现在比较主流的一种虚拟化镜像格式，经过一代的优化，目前qcow2的性能上接近raw裸格式的性能，这个也算是redhat的官方渠道了
	下面两个是qcow2过度：
	2.1）.cow：曾经qemu的写时拷贝的镜像格式，目前由于历史遗留原因不支持窗口模式,从某种意义上来说是,个弃婴，还没得它成熟就死在腹中，后来被qcow格式所取代
	2.2）.qcow一代的qemu的cow格式，刚刚出现的时候有比较好的特性，但其性能和raw格式对比还是有很大的差距，目前已经被新版本的qcow2取代。
	
3）.vmdk
	VMware的格式,性能不错
	
4）.vdi
	VirtualBox的格式，也是不错的workstation级别的产品
	
5）.vhd,vhdx
	微软出的一套虚拟文件系统
		
6）.img格式
	类似iso
	
参考文献：
	1）。raw qcow2 qed格式区别
	http://blog.chinaunix.net/uid-14735472-id-4220089.html
```
###### 1.3.虚拟文件格式的转换及操作

```

```
### 2).qemu-kvm安装

```
安装qemu-kvm管理界面
https://v.youku.com/v_show/id_XMzIyMzI5MjgwMA==.html

虚拟机组成部分：
    https://www.cnblogs.com/goldsunshine/p/12668632.html
    qemu-kvm：qemu模拟器
    qemu-img：qemu磁盘image管理器
    virt-install：用来创建虚拟机的命令行工具
    libvirt：提供libvirtd daemon来管理虚拟机和控制hypervisor
    virt-viewer：图形控制台

    $ service libvirtd start	//启动libvirt
    $ virt-manager				//调用图形管理界面

1.qemu-kvm安装
$ yum -y install kvm python-virtinst libvirt  bridge-utils virt-manager qemu-kvm-tools
windows安装（qemu路径添加到PATH环境变量）

2.创建一个镜像
qemu-img create test1.raw 2G
默认创建的为raw格式镜像，如果需要指定qcow2
$ qemu-img create test2.qcow2 -f qcow2 2G

3.镜像信息查看
$ qemu-img info test1.raw

4.镜像格式转换
raw:默认格式，不支持快照功能。镜像大小可以增大缩小
qcow2:支持快照，镜像只能增大，不能缩小

$ qemu-img convert -p -f raw -O qcow2 test1.raw test1.qcow2
-p 显示转换进度，-f 原有镜像格式，-O 输出镜像格式 输入文件 输出文件
主要用于将不同虚拟化产品的虚拟机镜像格式进行转换，例如VMware的vmdk转换成kvm用的qcow2格式
```

### 3).qemu-kvm常用命令

```
5.快照管理
查看镜像信息
$ qemu-img info centos7_sev.qcow2
1.查看快照
$ qemu-img snapshot centos7_sev.qcow2 -l
2.删除快照
$ qemu-img snapshot centos7_sev.qcow2 -d snapshot1-init
3.还原快照
$ qemu-img snapshot centos7_sev.qcow2 -a snapshot1-init
4.快照单独提取镜像
$ qemu-img convert -f qcow2 -O qcow2 -s snapshot1-init centos7_sev.qcow2 centos7_sev-init.qcow2

镜像一致性检查
$ qemu-img check test1.qcow2

6.镜像大小修改
    6.1）.raw格式
    qemu-img resize test1.raw  +2G
    qemu-img resize test1.raw  3G
    6.2）.qcow2格式
    qemu-img resize test2.qcow2 +2G

------------------------------------虚拟机管理
7.查看所有虚拟机
$ virsh list --all
virsh pool-list --all	//列出所有存储池

8.start,shutdown虚拟机
$ virsh start centos7_sev
$ virsh shutdown centos7_sev

9.创建虚拟机
	9.1）create磁盘
	$ qemu-img create kvm_test.qcow2 -f qcow2 40G
	9.2）create虚拟机
	virt-install --10ame kvm_test --virt-type kvm --ram 2048 --cdrom=/home/liyuan/tool/os/ubuntu-18.04.3-desktop-amd64.iso --disk path=/home/liyuan/soft/mach_vm/testvm/kvm_test.qcow2 --network network=default --graphics vnc,listen=0.0.0.0 --noautoconsole
	
创建虚拟机
https://www.cnblogs.com/yexiaochong/p/6029315.html
https://blog.csdn.net/weixin_34212762/article/details/92179634
安装虚拟机并启动服务
https://blog.csdn.net/sukysun125/article/details/89474962
参数详细
https://blog.51cto.com/u_11555417/2341874

10.启动停止
$ virsh start centos7_sev
$ virsh shutdown centos7_sev
```
### 4).qemu/网络

```
qemu官网:
https://www.qemu.org/

windows下安装：
官方下载安装包，解压安装，配置path环境变量即可

linux下安装：
安装完qemu-kvm既有qemu用户态命令，本身qemu-kvm需要qemu的配合

1.启动磁盘
qemu-system-x86_64w -m 2048 -smp 1 -drive file=centos7_sev.qcow2      //nat方式
qemu-system-x86_64w -m 2048 -smp 1 -drive file=centos7_sev.qcow2 -net nic -net user    //等同如上

2.下面是桥接方式(还有问题)
https://blog.csdn.net/m0_43406494/article/details/124827927
qemu-system-x86_64w -m 2048 -smp 1 -drive file=centos7_sev.qcow2 -boot c -net nic -net tap,ifname=qemu-tap

3.cpu核心配置(还有问题)
参数-smp 1解释
https://blog.csdn.net/jersonborn521/article/details/102787109
https://blog.csdn.net/whatday/article/details/78603725

smp:    是指在一个计算机上汇集了一组处理器(多CPU),各CPU之间共享内存子系统以及总线结构。在这种技术的支持下，一个服务器系统可以同时运行多个处理器，并共享内存和其他的主机资源。像双至强，也就是我们所说的二路，这是在对称处理器系统中最常见的一种（至强MP可以支持到四路，AMD Opteron可以支持1-8路）。也有少数是16路的。但是一般来讲，SMP结构的机器可扩展性较差，很难做到100个以上多处理器，常规的一般是8个到16个，不过这对于多数的用户来说已经够用了。在高性能服务器和工作站级主板架构中最为常见，像UNIX服务器可支持最多256个CPU的系统，其实qemu从代码设计上也是最大支持256个virtual cpu。
sockets:    就是主板上插cpu的槽的数目，也就是可以插入的物理CPU的个数。
cores:    就是我们平时说的“核“，每个物理CPU可以双核，四核等等。
threads:    就是每个core的硬件线程数，即超线程
A4核心：物理核心：1个，总核心数：2个
//A4机器，正常
qemu-system-x86_64w -m 2048 -smp 1,cores=1,threads=1,sockets=1 -drive file=centos7_sev.qcow2 -net nic -net user
//A4机器，跑不起来
qemu-system-x86_64w -m 2048 -smp 1,cores=1,threads=2,sockets=1 -drive file=centos7_sev.qcow2 -net nic -net user
//A4机器，跑不起来
qemu-system-x86_64w -m 2048 -smp 1,cores=2,threads=1,sockets=1 -drive file=centos7_sev.qcow2 -net nic -net user
验证在实际多cpu机器调整cpu参数？？？

4.网络处理
https://blog.csdn.net/jiangwei0512/article/details/122791901
https://blog.csdn.net/qq_37887537/article/details/129384415
    4.1).QEMU支持的网络模式
    https://blog.csdn.net/whatday/article/details/78446196/
    https://article.itxueyuan.com/W1XE4M
    4.2).Windows 计算机如何添加虚拟网卡
    https://product.pconline.com.cn/itbk/software/dnyw/1492/14922107.html
    https://consumer.huawei.com/cn/support/content/zh-cn00693656/
    4.3).虚拟设备之TUN和TAP？？？？
    https://zhuanlan.zhihu.com/p/260405786
    4.4）.vpn下载
    github里搜索openvpn
```
### 5).桥接bridge-utils安装配置

```
bridge-utils下载
https://pkgs.org/download/bridge-utils
视频kvm桥连配置
https://yq.aliyun.com/articles/510788
https://www.jianshu.com/p/14ee5f13f3c7
https://www.jianshu.com/p/e720a1dd26c5
https://www.cnblogs.com/heyongboke/p/10337447.html

安装插件bridge-utils
$ brctl --version	#如果没此命令，就需要安装bridge-utils
https://pkgs.org/download/bridge-utils	//centos8如果没有就下载centos7版本的.rpm
rpm -ivh *.rpm

参考过(不必)
https://www.jianshu.com/p/e720a1dd26c5
brctl addif br0 eth0 //将eth0配置为桥接网卡接口

cd /etc/sysconfig/network-scripts/ifcfg-enp3s0
1.修改ifcfg-enp3s0
#BOOTPROTO="dhcp"	//ADD 注释
BRIDGE="br0"	//ADD

2.创建ifcfg-br0
DEVICE="br0"
TYPE="Bridge"
ONBOOT="yes"
BOOTPROTO="dhcp"

3.kvm配置(kvm新建虚拟机)
NIC(虚拟机windows)
Network source : Specify share device name
Birdge name: br0(ifconfig查看虚拟网桥)
Device model : 选Hypervisor default(会自动选择rtl8139)
点击保存后会在`Network source`多一项(Birdge br0：Host device enp3s0)
创建下一个直接选着即可
注意
1.`虚拟机windows`防火墙需要关闭，否则`宿主机`访问不到vm
2.`宿主机`不能关闭防火墙(systemctl stop firewalld.service),
否则vm不能访问外网

4.桥接查看
执行命令: brctl show
bridge name	bridge id		STP enabled	interfaces
br0		8000.3497f692fbde	no		enp3s0
virbr0		8000.525400dff118	yes		virbr0-nic

注意
`虚拟机windows`防火墙可以不用关闭,
开通远程桌面连接3389，既可以连接(可能ping不通)

-----------------------------
问题
	1.使用frpc公网ip穿透，不需要关闭防火墙
	  使用局域网ip进行vnc5901访问需要关闭防火墙
	2.使用kvm进行clone虚拟机,花生壳(hsk)内网穿透智能开启一个账号(必须重装win2012系统才可以开启多个账号)
	3.vncserver run 问题
    不能窗口直接登录,只能用ssh登录启动vncserver连接，否则服务启动不起来
    (如果后面需要多个用户远程，可以研究配置)
    要关闭防火墙
    systemctl stop firewalld.service
```
# 二.桌面虚拟机
<div id="vmware_virtualbox"/>
### 1).vmware

```
vMware环境设置：
    安装好vMware后有vmnet1,vmnet8网卡，
   1. 常用模式有如下
    1>Bridged（桥接）方式
    vMware就模拟成了一个网桥，虚拟机就相当于插在同一个路由器上面，这个时候就要用vmnet1了，vmnet8无效

    2>NAT(网络地址转换）方式
    vmware就模拟成了一个具有DHCP功能的路由器，这个时候就要用vmnet8了,vmnet1无效
    需要：Edit>Virtual network editor
    可以查看vmnet8网关，要确保NAT service为开启（及DHCP）.

    2.设置vMware从u盘启动
    1>首先使用vMware新建一台裸机
    2>选中新建好的裸机选择"edit virtual machine settings"
    3>进入编辑硬件UI，选择“Hard Disk”添加一块硬盘
    4>选择添加类型为"use a physical disk"
    5>然后选择插上去的U盘（共有physicalDrive0,physicalDrive1两个），
    选择physicalDrive1，首先条件是必须先插入U盘后再启动vMware.
    6>最后重启vMware的那台机器，不断的按esc键，进入bois,选择boot项，按-，+来切换硬盘启动顺序

	vMware(Intel VT-x 处于禁用状态)：
			当你的电脑cpu是intel,并且在vMware上安装64位虚拟系统，启动vm时会报提示错误
	“Intel VT-x 处于禁用状态”，这种情况需要重启电脑进入bois设置：
	Virtualization Technology状态设置为Enabled(启用)

	共享虚拟机位置:
	C:\Users\Public\Documents\Shared Virtual Machines\
```

### 2).virtualbox

```
virtualbox环境设置：
	deb软件卸载
	https://blog.csdn.net/hanshuning/article/details/52328136

	报错问题解决
	https://blog.csdn.net/wofolangren/article/details/78467027

	虚拟机安装
	https://jingyan.baidu.com/article/656db918c55836e381249cbe.html

	端口转发
	https://blog.csdn.net/festone000/article/details/70852377

	可行的
	https://my.oschina.net/wffger/blog/206144
	
	虚拟机iso文件安装：
		新建虚拟机>选择虚拟机右键setting>storage>empty(右边点击选择系统iso文件,重启机器即可)
	
	常用四种网络模式:
		1.网络地址转换NAT(虚拟机通过主机连网,虚拟机能找到主机,但主机发现不到虚拟机的存在)
		2.Host-only(仅主机模式,可以通过网卡配置,实现复杂的网络模型)
		3.Bridged(占有局域网中一个ip,相当于是局域网中一台机器)
		4.Internal(与外网完全断开,只实现虚拟机之间的内部网络模式)
		
	常用网络启动:
		1.Bridged模型(网卡设置为Bridged模型即可)
		2.nat模型(要解决主机,虚拟机之间相互ping通)
			1>Host network manager > 新增一个adapter
			2>需要两个网卡,net1(nat模型),net2(Host-only,选择创建的adapter)
			
			
	通用可行的:
	如果需要实现主机,虚拟机之间相互ping通,通常做法是
		1>Host network manager > 新增一个adapter(默认有一个)
		2>每个安装的虚拟机启用,连接两个网卡(nat,Host-only)
		
	1.安装虚拟机(windows7为例)
		网络地址转换(nat)
		Host-only(启用,选定新增的adapter)

	2.启动虚拟机
		配置静态虚拟机ip(在adapter一个网段),网关(新增的adapter)
		windows7:需要配置ip地址到同一网段(关闭防火墙)
		ubuntu16:貌似不需要配置就可以ping通
		
	默认光标切换
		Right(→) +  Ctrl 
		
	full clone与linked clone区别
		https://www.myxzy.com/post-186.html
		
问题：		
	1.首次安装VirtualBox，当添加Host-only网络时，没有网络可选择，解决办法
	管理->主机网络管理器:添加一个网络适配器，即可解决

	2.虚拟机VirtualBox启动虚拟机报Only Ethernet Adapter' (VERR_INTNET_FLT_IF_NOT_FOUND).
	添加网络管理器可以选择Host-only，报上面问题，解决办法：
	控制面板打开"网络连接"选中刚才建的网络适配器右键禁用，然后再启用就ok

	3.退出屏幕
	Right+Ctrl 按右边的（Alt+Ctrl）
```

### 3).“笔记本创建wifi热点”与“笔记本直连”

```
network(网络问题解决)
	笔记本创建wifi热点：
		1.以管理员运行cmd
		netsh wlan set hostednetwork mode=allow ssid=liyuannet key=0123456789

		执行后:
		网络>网络和共享中心>更改适配器设置>无线网络连接器2(执行命令后多出来的)

		2.选中本地连接>右键属性>共享(选中"允许其他网络用户通过此计算机internet连接..")
		开启热点:netsh wlan start hostednetwork
		关闭热点:netsh wlan stop hostednetwork

	笔记本直连:
		https://jingyan.baidu.com/article/b24f6c822135b286bfe5da0f.html
```

# 三.linux远程桌面连接
<div id="remote_desktop"/>
### 1).远程桌面介绍及方案
```
一.linux桌面环境
	1.桌面环境介绍
	GNOME(速度快稳定,适合服务器) 与 KDE(软件丰富)
		https://zhidao.baidu.com/question/271278332.html
		https://www.cnblogs.com/chenmingjun/p/8506995.html
		
	http://c.biancheng.net/view/2912.html
	https://www.cnblogs.com/chenmingjun/p/8506995.html
	https://www.jianshu.com/p/69d9d2e7c7ec
	
	2.查看linux桌面环境
		http://dl.17house.com/321144/
		https://www.cnblogs.com/APeng2019/p/10719417.html
		查看版本
		echo $DESKTOP_SESSION

二.linux远程桌面方案
	介绍：
	这里重点介绍linux图形化的远程桌面方案,ssh不作讲解.
	每个linux发行版不同,最适合的linux远程方案也不同,因为每个发行版桌面环境不一样.
	这里只介绍如下两个发行版的：
	1).ubuntu18(使用x11vnc)
		安装
		sudo apt-get install x11vnc -y
		https://blog.csdn.net/weixin_41803874/article/details/81233789
		https://blog.csdn.net/han609768249/article/details/78759590/
	2).centos8
		安装
		dnf install tigervnc-server tigervnc-server-module -y
		centos具体的安装配置见后面
	
	1.平台介绍
		一般远程常见有如下两种情况:
		1)linux远程windows
			这种场景现有比较好解决,利用系统自带的remmina或grdesktop客户端就可以连接
			条件是windows设置为允许远程连接,客户端连接协议是RDP协议(windows远程协议)
		2)windows远程linux
			这种场景首先要配置,安装好linux远程服务(这也是本文档重点介绍的地方).
			然后安装好对应客户端软件vnc,就可以远程了(不想安装vnc除非linux服务端配置安装好RDP服务协议).
			
		客户端:
		https://www.realvnc.com/en/connect/download/viewer/
			服务端:
			https://www.realvnc.com/en/connect/download/vnc/linux/
		
		服务端:
		https://tigervnc.org
			常见的服务端还有：
				https://www.tightvnc.com
				http://www.karlrunge.com/x11vnc
				
		1.ubuntu18
			sudo apt-get install x11vnc -y
			https://blog.csdn.net/weixin_41803874/article/details/81233789
			https://blog.csdn.net/han609768249/article/details/78759590/
		
		2.centos8
			下面基本上全部介绍的都是centos安装配置tigervnc-server
			问题(redhat 8.3 的VNC 配置与其他的redhat 版本不太一样)：
			https://www.cnblogs.com/gaizhongfeng/p/14162596.html


linux远程桌面连接
https://www.cnblogs.com/wxwgk/p/11738367.html
https://blog.csdn.net/Robinsone/article/details/46686531
https://blog.csdn.net/u011054333/article/details/79905102

远程桌面介绍
https://www.cnblogs.com/pipci/p/7833581.html

xrdp krdc
https://www.jianshu.com/p/f60cec98eccd

协议
https://blog.csdn.net/zbgjhy88/article/details/81009222
```
### 2).centos远程桌面安装配置

```
远程连接方案
xrdp安装
https://blog.csdn.net/oqqDong123456789/article/details/93298362

tigervnc-server安装
https://vps.zzidc.com/centos/2476_2.html
https://linux.cn/article-11458-1.html

1.安装
dnf install tigervnc-server tigervnc-server-module -y

vnc密码
888888
systemctl daemon-reload
systemctl start vncserver@1.service
systemctl start vncserver@.service
vncserver@.service

2.配置(新建vncserver@.service)
vi /etc/systemd/system/vncserver@.service,内容如下

[Unit]
Description=Remote Desktop VNC Service
After=syslog.target network.target

[Service]
Type=forking
WorkingDirectory=/home/liyuan
User=liyuan
Group=liyuan

ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/usr/bin/vncserver -autokill %i
ExecStop=/usr/bin/vncserver -kill %i

[Install]
WantedBy=multi-user.target

3.切换到liyuan设置vnc密码
vncpasswd
[root@linuxtechi ~]$ vncpasswd
Password:
Verify:
Would you like to enter a view-only password (y/n)? n
A view-only password is not used

4.重新加载,启动,使能
[root@linuxtechi ~]# systemctl daemon-reload
[root@linuxtechi ~]# systemctl start vncserver@:1.service
[root@linuxtechi ~]# systemctl enable vncserver@:1.service


[root@linuxtechi ~]# systemctl start vncserver@:2.service
[root@linuxtechi ~]# systemctl enable vncserver@:2.service


5.条件
systemctl stop firewalld.service	//关闭防火墙
vi /etc/selinux/config				//关闭selinux
修改如下
SELINUX=disabled

```
### 3).问题

```
问题解决(不必修改项)
https://blog.51cto.com/bingwzi/1391821
vi /etc/sysconfig/vncservers
VNCSERVERS="1:liyuan 2:root"
VNCSERVERARGS[1]="-geometry 1024x768"
VNCSERVERARGS[2]="-geometry 1024x768"

Created symlink /etc/systemd/system/multi-user.target.wants/vncserver@:2.service → /etc/systemd/system/vncserver@.service

Unencrypted connection1	

192.168.202.130:5901

tiger/888888

错误查看：
systemctl status vncserver@:1.service	//启动日志
grep vnc /var/log/messages				//错误日志

Vncserver默认端口改变：
解决办法：
删除 / tmp/.X11-unix/的内容
重启vnvserver
$ systemctl restart vncserver@:2.service
----------------------------------------------------------------------------
问题(未解决)
Job for vncserver@:1.service failed because the control process exited with error code. 
See "systemctl status vncserver@:1.service" and "journalctl -xe" for details.
See "systemctl status vncserver@:1.service" and "journalctl -xe" for details.
1.未解决
https://www.cnblogs.com/Jesse-Li/p/9742698.html
修改vncserver@:1.service 中的 Type 改为 simple

2.未解决
https://www.cnblogs.com/ningcoding-life/p/10943417.html

3.dbus-daemon存在冲突
https://www.cnblogs.com/kevingrace/p/5821450.html
Could not make bus activated clients aware of XDG_CURRENT_DESKTOP=GNOME environment variable:
	1>查看
	find / -name "dbus-daemon"
	2>修改配置文件vncserver@.service
	ExecStart=/usr/bin/vncserver -autokill %i
	改为
	ExecStart=/usr/sbin/runuser -l liyuan -c "/usr/bin/vncserver -autokill %i"
	
	PIDFile=/home/liyuan/.vnc/%H%i.pid
	3>使用非root用户启动
----------------------------------------------------------------------------
4.(手动执行vncserver)权限不够
dbus[3973]: Unable to set up transient service directory: XDG_RUNTIME_DIR "/run/user/1000" is owned by uid 1000, not our uid 1001
Failed to import environment: Process org.freedesktop.systemd1 exited with status 1

(process:3988): dconf-CRITICAL **: 03:20:23.763: unable to create directory '/run/user/1000/dconf': 权限不够.  dconf will not work properly.

(process:3988): dconf-CRITICAL **: 03:20:23.763: unable to create directory '/run/user/1000/dconf': 权限不够.  dconf will not work properly.

Wed Jan 29 03:20:25 2020

5.服务启动
systemctl start vncserver@:1.service
```
### 4).其它远程桌面方案

```
其它远程linux方法：
1.web ssh服务端安装(web浏览器终端访问)
	1>.gateone(依赖的插件比较多)
		...
	2>.shellinabox(安装简单)
		官网
		https://code.google.com/p/shellinabox
		技术资料
		https://www.tecmint.com/shell-in-a-box-a-web-based-ssh-terminal-to-access-remote-linux-servers/
		
		//安装
		apt-get install shellinabox
		//端口配置
		vi /etc/default/shellinabox
		//重启服务
		service shellinabox restart

2.teamviewer install图形界面(限时收费的)
	1>.从teamviewer官网下载ubuntu的deb安装包

	2>.执行命令 sudo dpkg -i teamviewer_11.0.xxxxx_i386.deb

	3>.添加32位架构，命令如下：
	sudo dpkg --add-architecture i386
	sudo apt-get update
	在缺少依赖包的安装 需要执行：sudo apt-get -f install

	4>.安装
	sudo dpkg -i teamviewer_11.0.xxxxx_i386.deb

	5>.启动
	命令行输入:
	teamviewer
```