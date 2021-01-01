# linux虚拟机kvm

```
hypervisor centos
	https://blog.51cto.com/yangshufan/2130043
	https://blog.csdn.net/u013946404/article/details/78739830
	https://blog.csdn.net/bbc955625132551/article/details/71597863
主要厂商虚拟技术
	1.微软的hyper-v
	2.vmware
	3.linux开源的KVM,Xen,VirtualBSD等
虚拟存储
	raw qcow2 qed格式区别
	http://blog.chinaunix.net/uid-14735472-id-4220089.html

hyperv vmware hypervisor 
	https://cloud.tencent.com/developer/article/1041633
	
命令行启动虚拟机
	https://www.centos.bz/2017/08/kvm-virtualization-platform-deploy/
----------------------------------------------------------------------------
kvm桥连配置GUI
https://yq.aliyun.com/articles/510788
http://www.biyunfei.com/2019/07/17/397.html

视频kvm桥连配置
https://www.iqiyi.com/v_19rrf06thk.html
https://www.jianshu.com/p/14ee5f13f3c7
https://www.jianshu.com/p/e720a1dd26c5
https://www.cnblogs.com/heyongboke/p/10337447.html

安装kvm管理界面
https://v.youku.com/v_show/id_XMzIyMzI5MjgwMA==.html
-----------------------------
vncserver run 问题
不能窗口直接登录,只能用ssh登录启动vncserver连接，否则服务启动不起来
(如果后面需要多个用户远程，可以研究配置)
要关闭防火墙
systemctl stop firewalld.service
-----------------------------
KVM桥接配置(宿主机与vm可以互访)
NIC(虚拟机windows)
Network source : Specify share device name
Birdge name: virbr0(ifconfig查看虚拟网桥)
Device model : 选Hypervisor default(会自动选择rtl8139)
注意
1.`虚拟机windows`防火墙需要关闭，否则`宿主机`访问不到vm
2.`宿主机`不能关闭防火墙(systemctl stop firewalld.service),
否则vm不能访问外网
-----------------------------------------------------
安装插件bridge-utils
https://pkgs.org/download/bridge-utils	//centos8如果没有就下载centos7版本的.rpm
rpm -ivh *.rpm

参考过(不必)
https://www.jianshu.com/p/e720a1dd26c5
brctl addif br0 eth0 //将eth0配置为桥接网卡接口

1.修改ifcfg-enp3s0
#BOOTPROTO="dhcp"	//ADD 注释
BRIDGE="br0"	//ADD

2.创建ifcfg-br0
DEVICE="br0"
TYPE="Bridge"
ONBOOT="yes"
BOOTPROTO="dhcp"

3.kvm配置
NIC(虚拟机windows)
Network source : Specify share device name
Birdge name: br0(ifconfig查看虚拟网桥)
Device model : 选Hypervisor default(会自动选择rtl8139)
点击保存后会在`Network source`多一项(Birdge br0：Host device enp3s0)
创建下一个直接选着即可

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
```

