# vpn-pptpd

## 一．服务端安装

```
服务端安装:
	1.安装pptpd
	sudo apt-get update //新安装的系统需要更新一下
	sudo apt-get install pptpd

	2 设置pptpd服务器本地ip与拨号进入的用户的IP地址范围
	sudo vi /etc/pptpd.conf
	添加如下行：
	localip 94.191.73.142	//指定本机内网ip 或 公网ip(不能指定localhost与127.0.0.1) [如果是本机映射到路由器，该地址要改为路由器的入口地址。]
	remoteip 192.188.2.1-15	//指定vpn分配的内网ip段,可以与内网ip,公网ip不在一个网段 

	3 修改pptp的dns配置（可选）
	修改：
	/etc/ppp/pptpd-options
	增加ms-dns 的值为192.168.1.1，这一步是可选的，不配置也可以

	4 配置用户信息
	vi /etc/ppp/chap-secrets

	# client        server  secret                  IP addresses
	liyuan pptpd ly123456 *
	用户名（tab） 主机名（tab） 密码（tab） 分配到的ip地址

	5 重启pptpds
	sudo /etc/init.d/pptpd restart

	6 修改/etc/sysctl.conf
	sudo vi /etc/sysctl.conf

	去掉"net.ipv4.ip_forward=1"
	前的注释号或者添加一行,
	执行生效:
	sudo sysctl -p

	7 转发规则命令
	iptables -t nat -A POSTROUTING -s 192.188.2.1/15 -o eth0 -j MASQUERADE  ////注意eth0表示局域网使用的网卡名称,虚拟网段ip
	iptables-save > /etc/iptables.pptp//生成文件

	8 重启pptpd
	sudo /etc/init.d/pptpd restart

	9 配置pptp客户端来连接服务器
	最后就可以在使用windows或是另一台Linux服务器上，作为客户端来通过pptp连接这台pptp服务器，
	必须使用IP地址，用户名和密码。连接成功之后能看到客户端上能看到分配的ip地址，
	pptp服务器上也能看到新的服务器和客户端ip地址。

	10.解决浏览器无法访问页面问题
	iptables -A FORWARD -s 192.188.2.1/15 -p tcp -m tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1200
	//下面这个貌似无效
	iptables -A forwarding_rule -s 172.30.2.0/16 -j ACCEPT

	服务器重启后要重新执行如下语句
	iptables -t nat -A POSTROUTING -s 192.188.2.1/15 -o eth0 -j MASQUERADE	//注意eth0表示局域网使用的网卡名称
	iptables -A FORWARD -s 192.188.2.1/15 -p tcp -m tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1200
		
	首次安装配置需要做如下两件事情
	1.放行端口: ufw allow 1723
	2.云服务器配置安全规则: 
```

## 二．客户端安装

```
1.windows连接
	控制面板>网络和共享中心>
			设置新的连接或网络>连接到工作区>使用我的Internet连接(VPN)

2.linux连接
	1.vpn客户端安装
	sudo apt-get install -y pptp-linux

	2.vpn服务端连接
	pptpsetup --create kk --server pptp服务器IP --username 用户名  --password 密码 --encrypt --start
	这里面“kk”是创建的pptp客户端的名称，随便写一个就行，命令运行完会生成文件：
	/etc/ppp/peers/kk
	可以使用vi命令查看其内容。

	3.查看路由
	客户端连接成功后，在pptp服务器上可以通过：
	route -nr
	命令查看到客户端的连接。

	删除原来路由
	route del default

	将vpn连接ppp0设置成默认路由
	route add default dev ppp0 貌似可以route add default ppp0
	注意：有的时候遇到：
	LCP terminated by peer (MPPE required but peer refused)
	的错误，这说明服务端要求MPPE加密，但是客户端不支持，也就是在运行pptpsetup命令时漏掉了：.
	--encrypt
	选项，加上就可以了。
	
	开机自启动编辑文件添加如下内容(vi /etc/rc.local)
	pptpsetup --create kk --server pptp服务器IP --username 用户名  --password 密码 --encrypt --start
	route add default ppp0

	linux客户端连接vpn
	https://www.jianshu.com/p/c78560fd1c56
```

## 三．概要

```
1、按VPN的协议分类：
	VPN的隧道协议主要有三种，PPTP、L2TP和IPSec，其中PPTP和L2TP协议工作在OSI模型的第二层，又称为二层隧道协议；IPSec是第三层隧道协议。

2、按VPN的应用分类：
	（1）Access VPN（远程接入VPN）：客户端到网关，使用公网作为骨干网在设备之间传输VPN数据流量；
	（2）Intranet VPN（内联网VPN）：网关到网关，通过公司的网络架构连接来自同公司的资源；
	（3）Extranet VPN（外联网VPN）：与合作伙伴企业网构成Extranet，将一个公司与另一个公司的资源进行连接。
协议场景
IPSec:主要用于公司与公司之间的局域网连接(不同城市),主要是为了其他不支持IPSec上的
L2TP或PPTP VPN隧道技术的路由器,网关或终端之间的相互操作
PPTP:不需要安装客户端软件,兼容许多移动设备，
如iphone，ipad和Windows移动，安装简易(但存在一些网络环境不能连接)
SSL VPN(典型openVPN)：客户端是cs结构,openVPN开源,稳定

vnp三种应用场景
http://www.metsky.com/archives/278.html

视频
http://edu.51cto.com/center/course/lesson/index?id=333083
ipsec
http://v.youku.com/v_show/id_XMjc5MzAxNDE0MA==.html

vpn搭建

	pptd
	https://blog.csdn.net/dongdong9223/article/details/80790203
	https://blog.csdn.net/qq_26938637/article/details/77817134
	
	PPTP和OpenVPN有什么区别
	https://zhidao.baidu.com/question/584463058.html
	
google助手
https://github.com/haotian-wang/google-access-helper
```

