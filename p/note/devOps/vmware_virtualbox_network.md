# vmware_virtualbox_network

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
----------------------------------------------------------------------------
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
----------------------------------------------------------------------------
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
----------------------------------------------------------------------------

```

