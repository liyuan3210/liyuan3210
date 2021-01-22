# linux远程桌面连接

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
----------------------------------------------------------------------------
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



错误查看
systemctl status vncserver@:1.service	//启动日志
grep vnc /var/log/messages				//错误日志

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
----------------------------------------------------------------------------
KVM桥连接
kvm桥连配置GUI
https://yq.aliyun.com/articles/510788
http://www.biyunfei.com/2019/07/17/397.html
视频kvm桥连配置
https://www.iqiyi.com/v_19rrf06thk.html
https://www.jianshu.com/p/14ee5f13f3c7
https://www.jianshu.com/p/e720a1dd26c5
安装kvm管理界面
https://v.youku.com/v_show/id_XMzIyMzI5MjgwMA==.html

bridge-utils下载
https://pkgs.org/download/bridge-utils

----------------------------------------------------------------------------
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

