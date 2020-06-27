# iptables(防火墙)

## 一．概要

```
视频教程
http://video.tudou.com/v/XMjc2ODI5NTY2OA==.html?f=49837998
https://v.youku.com/v_show/id_XMjc2ODI4NjQ2MA==.html?spm=a2hzp.8244740.0.0&f=49837998

防火墙iptables
https://blog.csdn.net/fuzhongfaya/article/details/82022181
https://www.cnblogs.com/frankb/p/7427944.html

netfilter属于linux内核一个模块
iptables与ubuntu的ufw都属于应用层操作内核配置的程序

五表
	filter		对数据包过滤
	nat			修改数据包的Ip地址,端口号等信息
	mangle		修改数据包头部信息,如TOS,TTL,MARK标记
	raw			决定数据包是否被状态跟踪机制处理(1.2.9+)
	security	强制网络访问的网络,如selinux
	
	表的处理优先级：raw>mangle>nat>filter

五链
	input		接受到防火墙本机地址的数据包(入站)时
	output		防火墙本机向外发送数据包(出站)时
	forward		接收到需要通过防火墙发送给其他地址的数据包(转发)
	prerouting	对数据包作路由选择之前
	postrouting	对数据包作路由选择之后
	
				
PREROUTING			[网卡]
raw表				
mangle表							POSTROUTING
nat表								mangle表
					FORWARD		→	nat表
↓					mangle表
					filter表
路由判断	→		security表	    ↑
							   	
↓
									OUTPUT
INPUT								raw表
mangle表							mangle表
nat表(centos7)						nat表(centos7)
filter表							filter表
security表		→	[应用程序]	→	security表
									
所有数据都有两种路径出去
[网卡]入站 -> PREROUTING -> FORWARD -> POSTROUTING -> [网卡]出站
[网卡]入站 -> PREROUTING -> INPUT -> [应用程序] -> OUTPUT -> POSTROUTING -> [网卡]出站


表与链关系
filter		INPUT,FORWARD,OUTPUT
nat			OUTPUT,PREROUTING,POSTROUTING
mangle		PREROUTING,POSTROUTING,INPUT,FORWARD,OUTPUT
raw			PREROUTING,output
security	???
```

## 二．常用命令及零基础搭建防火墙

```
查看网卡及ip
	ip a
	ip addr

或者traceroute ip查看是通过哪台主机访问的目标机器的
	tracepath 192.168.5.12

查看iptables语法
	man iptables

telnet验证
	telnet ip 22
	验证规则(下面端口协议通过,可能是操作系统返回的参数)
可以使用watch iptables -vnxL命令监控是否通过规则
	telnet ip 80 
	telnet: connet to address 192.168.5.11:Connetion refused

查看所有表配置(默认对filter表进行操作)
	iptables -t filter -S
	iptables -t raw -S
	iptables -t mangle -S
	iptables -t security -S
	iptables -t nat -S

查看内建表
	-j 参数值，保存规则
	cat /proc/net/ip_tables_names
	https://www.cnblogs.com/wangkangluo1/archive/2012/04/19/2457072.html

查看表下面的链
	iptables -L -t security	

自定义链
	https://blog.csdn.net/lbyyy/article/details/78898931
	https://www.jianshu.com/p/4ac00aa88ec3
	
	iptables -t security -N WEB_LIYUAN3210	//新建链
	iptables -t security -I INPUT -p tcp --dport 80 -j WEB_LIYUAN3210	//引用自定义链
	iptables -t security -E WEB_LIYUAN3210 WEB_SIT	//重命名
	iptables -t security -X WEB_LIYUAN3210	//删除链(满足两个条件1.没被引用,2.没有任何规则)
	
	
从零搭建一个基本防火墙
放行端口
	//iptables -A INPUT -p tcp --dport 80 -j ACCEPT
	//iptables -A INPUT -p tcp --dport 443 -j ACCEPT
	iptables -A INPUT -p tcp --dport 22 -j ACCEPT
	上面三条语句可以只用一句搞定
	iptables -A INPUT -p multiport --dports 22,443,80 -j ACCEPT
	-j     后面加动作： 
			（1）ACCEPT  允许包  
			（2）DROP 丢掉包 
			（3）REJECT 拒绝包

默认执行上面是没有走上面规则的(放行22,443,80规则),要执行如下命令
	1.注意执行下面语句是要先执行上面放行22端口(否则会断开连接)
	iptables -A INPUT -p tcp --dport 22 -j ACCEPT
	iptables -P INPUT DROP

	2.现在上面是ping不通上面机器的问题(ping走的是icmp协议)
	iptables -A INPUT -p icmp -j ACCEPT

	查看规则使用情况
	watch iptables -vnxL

	3.本机telnet 127.0.0.1 22不能通过问题
	默认都是针对ens33网卡,不针对回环地址lo,需要执行如下命令
	iptables -I INPUT -i lo -j ACCEPT (大-I插入到规则最前面,-A是插到最后面,小-i指定网卡)
	-I把这个规则放到规则最前面

	4.本机不能访问外面网站服务问题(curl www.baidu.com)
	watch iptables -vnxL
	//iptables -I INPUT -m state --state ESTABLISHED -j ACCEPT
	//iptables -D INPUT -m state --state ESTABLISHED -j ACCEPT
	iptables -A INPUT -m state --state ESTABLISHED -j ACCEPT

	NEW:该包想要开始一个新的连接(重新连接或连接重定向)
	RELATED:该包是属于某个已经建立的连接所建立的新连接,如FTP的数据传输连接和控制连接之间的是RELATED关系
	ESTABLISHED:该包属于某个已经建立的连接
	INVALID:该包不匹配于任何连接,通常这些包被DROP
	
规则添加顺序问题
	大-I插入到规则最前面,-A是插到最后面

清除规则(重启后会不会还原)
	iptables -F
	-F：清除所有制订的规则
	-X：清除所有用户“自定义”的chain
	-Z：将所有chain的计数与流量统计都归零

	https://blog.csdn.net/chengqiuming/article/details/70139629
	https://blog.csdn.net/ingiaohi/article/details/70559425

保存配置(保存不同规则)
	iptables添加规则后(重启后不会保存)
	
	ubuntu保存
	iptables-save > /etc/iptables.rules	//保存
	
	vi /etc/network/interfaces
	pre-up iptables-restore < /etc/iptables.rules	//最后面添加这行
	
	这样重启后还会生效
	
	保存问题
	https://blog.csdn.net/sqzhao/article/details/10174955
	http://www.zhimengzhe.com/linux/281990.html

删除规则问题
https://blog.csdn.net/chengxuyuanyonghu/article/details/51897666
```

## 三．ftp主动模式和被动模式下iptables的配置(vsftp)

```
1.主动模式
	ftp客户端登录成功后,客户端会分配一个随机的接收数据端口
	pasv_enable=NO
	#pasv_min_port=24500
	#pasv_max_port=24600
	主动模式服务端只要开通21端口

2.被动模式
ftp客户端登录成功后,服务端会分配一个随机的发送数据端口
	pasv_enable=YES
	pasv_min_port=24500
	pasv_max_port=24600
	开放一段的端口
	iptables -A INPUT -p tcp --dport 24500:24600 -j ACCEPT

使用内核模块来限制(把上面的24500,24600去掉)
	lsmod | grep ftp	//查看ftp内核模块是否启动
	modprobe nf_conntrack_ftp	//启动内核ftp模块
	vi /etc/sysconfig/iptables-config
	IPTABLES_MODULES="nf_conntrack_ftp"//去掉注释
	IPTABLES_MODULES=""//默认是空注释掉
	systemctl restart iptables	//重启

	最后重启
	systemctl restart vsftpd

	执行命令
	iptables -A INPUT -m state --state RELATED -j ACCEPT
```

## 四．iptables之SNAT和DNAT设置

```
SNAT:作为网关,使内网机器上网
	DNAT:进行端口映射
	
	把一台机器作为一个网关,让66网段能够访问55网段
	机器A					网关机器
	10.0.55.220/24			10.0.55.221/24
							10.0.66.221/24
							机器B
							10.0.66.222/24
							
	SNAT(机器B能访问机器A)
		(网关机器)
		iptables -t nat -A POSTROUTING -s 10.0.66.222(机器B) -j SNAT --to-source 10.0.55.221(网关55)
		sysctl -a | grep forward	//查看
		sysctl -w net.ipv4.conf.all.forwarding=1
		
		(机器B)
		ip r 	//查看路由
		修改默认路由规则
		ip route change default via 10.0.66.221 dev enp0s3(不同机器网卡名称会不同)
		ping 10.0.55.220	//可以ping通
		traceroute 10.0.55.220	//后面几跳过不去
		
		(机器A)traceroute不到
		iptables -A INPUT -m state --state NEW -j ACCEPT
		
	DNAT在上面基础上(A是访问不了B的)
		在A开通一个ssh端口访问B
		
		(网关机器)
		iptables -t nat -A PREROUTING -p tcp --dport 6222 -j DNAT --to 10.0.66.222:22
		
		登录测试
		ssh -p 6222 user@10.0.55.221
```

## 五．怎么利用iptables来禁止访问淘宝和QQ

？？？

## 六．iptables进行频率控制和并发控制

```
防止网络攻击,进行并发控制
	iptables -A INPUT -p icmp -m limit --limit 5/m --limit-burst 5 -j ACCEPT
	--limit 5/m 一分钟之内只运行走5次
	--limit-burst 5	//有五个的缓冲时间
	注意规则顺序
	
	测试验证
	ping ip 	//每分钟只能访问5次
	
	
	并发控制(并发量控制在100)
	iptables -I INPUT -p tcp --syn --dport 80 -m connlimit --connlimit-above 100 -j REJECT
	没有测试
```

## 七．软路由

```
【软路由从入门到放弃系列】第一期 软路由DIY攒机 硬件篇
https://www.bilibili.com/video/av54995428?spm_id_from=333.5.b_746563686e6f6c6f67795f77696c64.8

一个网口也能做软路由？闲置电脑再利用！
https://www.bilibili.com/video/av60334860/?spm_id_from=333.788.videocard.8

软路由做内网穿透
https://www.bilibili.com/video/av57997802
```

