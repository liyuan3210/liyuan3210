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

# qemu-kvm

```
1.qemu-kvm安装
$ yum -y install kvm python-virtinst libvirt  bridge-utils virt-manager qemu-kvm-tools

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

5.快照
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
1.raw格式
qemu-img resize test1.raw  +2G
qemu-img resize test1.raw  3G
2.qcow2格式
qemu-img resize test2.qcow2 +2G

------------------------------------
7.查看所有虚拟机
$ virsh list --all
virsh pool-list --all	//列出所有存储池

8.start,shutdown虚拟机
$ virsh start centos7_sev
$ virsh shutdown centos7_sev

创建虚拟机(centos qemu-kvm命令创建虚拟机)
https://blog.csdn.net/weixin_34212762/article/details/92179634

虚拟机组成部分
https://www.cnblogs.com/goldsunshine/p/12668632.html
qemu-kvm：qemu模拟器
qemu-img：qemu磁盘image管理器
virt-install：用来创建虚拟机的命令行工具
libvirt：提供libvirtd daemon来管理虚拟机和控制hypervisor
virt-viewer：图形控制台

$ virt-manager		//调用图形管理界面

kvm虚拟机安装：
？？？？

创建虚拟机
//qemu-img create -f qcow2 fedora.img 10G
创建虚拟机
https://www.cnblogs.com/yexiaochong/p/6029315.html
安装虚拟机并启动服务
https://blog.csdn.net/sukysun125/article/details/89474962
参数详细
https://blog.51cto.com/u_11555417/2341874
1.create磁盘
$ qemu-img create kvm_test.qcow2 -f qcow2 40G
2.create虚拟机
virt-install --name kvm_test --virt-type kvm --ram 2048 --cdrom=/home/liyuan/tool/os/ubuntu-18.04.3-desktop-amd64.iso --disk path=/home/liyuan/soft/mach_vm/testvm/kvm_test.qcow2 --network network=default --graphics vnc,listen=0.0.0.0 --noautoconsole
3.启动停止
$ virsh start centos7_sev
$ virsh shutdown centos7_sev
```

