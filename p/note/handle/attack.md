# 攻击流程
### 一。环境准备

```
1.kali (基于debian发行)
	https://www.kali.org
	设计用于安全检测操作系统，内部集成600+安全工具

2.metasploitable2(基于ubuntu操作系统)
	本身设计作为安全工具测试和演示常见漏洞攻击（本镜像一定不要暴露在容易受攻击的网络中）

3.win2k3(windows server系统)
	https://msdn.itellyou.cn
	
4.nessus漏洞扫描器
	https://www.tenable.com
	是目前全世界最多人使用的漏洞扫描器，总共有75000个机构使用。
	需要到官方获取激活码的

5.增加点攻击面（w2k3）
	关闭防火墙，开启远程桌面，启动iis服务器，添加新组件
```

### 二。信息获取

```
	OSI分层介绍，网络七层模型

1.二层发现
	主要指链路层，一般使用arp协议，利用mac地址作为识别地址。
	效率高，但无法扫描经过的路由主机。
	常用工具:arping，使用方法：arping 192.168.1.1。
	常用工具：netdiscover(可针对特定子网扫描)，netdiscover -r 192.168.1.1/24。
	弥补由于无法多个主机扫描，只能单个扫描问题
	
2.三层发现
	主要指网络层，一般使用ICMP协议。
	可以发现远程主机，经过路由的主机。
	但，经常被防火墙过滤，相对二层发现比较慢。
	常用工具：ping 域名/ip
	常用工具：fping(可针对特定子网扫描)，fping -g 192.168.1.1/24
	常用工具：hping3(发送自定义ICMP包，绕过防火墙),hping3 -c 2 --icmp 192.168.1.1

3.四层发现
	主要指传输层，一般使用tcp,udp探测
	可以探测远程主机，比三层更可靠，但花费时间长
	常用工具：nmap IP地址
	常用工具：hping3 (使用wirshark抓包，发现发送消息没返回可能防火墙拦截了，需要结合二三层主机发现)
		hping3 --udp -c 3 IP地址
	脚本探测：python,shell

4.端口扫描，端口banner信息（服务版本，操作系统信息）
	TCP端口235端口 与 UDP端口235并不冲突？？？
	nmap：
		nmap IP地址
		nmap IP地址 --script banner -p 端口号	//banner信息获取
		nmap -p 端口号 -sV IP地址							//端口服务版本(有些软件无法直接返回的，所以要加-sv获取)
		nmap -O IP地址 												//操作系统版本，直接获取所有nmap -A -v IP地址，要授权）
	Dmitry(端口扫描只是其中一种)：
		dmitry -p IP地址
		dmitry -pb IP地址			//banner信息获取
	netcat： 
		nc -nvz IP地址 20-30
		nc -vn IP地址 端口号			//banner信息获取
```

### 三。漏洞扫描

```
1.漏洞扫描
	nmap（服务漏洞）：
		nmap --script vuln ip地址
	nessus(系统漏洞)：
		$ nc ip地址 1524				//漏洞利用
		$ id
		

2.漏洞利用
	metasploit
	$ msfconsole

3.web漏洞扫描
	常见web漏洞扫描器（每个扫描器有各自不同的payload进行探测）：
	wasp-zap,AWVS,appscan,Nikto,burpsuite
	实例：sql Injection
```

### 四。服务漏洞

```

```

### 五。攻击方式

```

```

