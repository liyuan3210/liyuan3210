# linux服务器常用命令

```
一.ubuntu
	官网:
	https://ubuntu.com/
	开启root:
		·sudo passwd root  //输入命令后根据提示设置密码，就开启了root
		·sudo passwd -l root //输入命令后禁用了账户
		·sudo apt-get update //同步 /etc/apt/sources.list 和 /etc/apt/sources.list.d 中列出的源的索引，这样才能获取到最新的软件包。
		·sudo apt-get upgrade //升级已安装的所有软件包，升级之后的版本就是本地索引里的
		·sudo apt-get install libc6-dev//ubuntu安装命令

	环境变量设置:
		·/etc/profile:所有用户,当用户使用UI第一次登录时才执行。
		·/etc/bash.bashrc:所有用户shell，所属于所有用户，当用户打开一个shell时执行。
		·~/.bashrc: 专属用户自己的bash信息，用户打开一个shell时执行
		命令生效:source /etc/profile

	常用命令:
		·ls:当前目录所有文件
		·ls -la:包括系统隐藏的文件
		·pwd:查看当前目录
		·mv /dir/* /dir2 linux文件夹移动命令·
		·su userName:切换用户
		·locate crt1.o//安装时常用到的查找命令
		·ln -s /usr/lib/i386-linux-gnu/crt1.o /usr/lib/crt1.o//创建软链接命令
		·netstat -lntp,netstat -ntl//查看端口,pid
		·ps au,ps aux//查看进程
		·top //查看资源使用情况
		·df -h //查看磁盘使用情况
		·sudo ufw disable(关闭),enable(开启)//关闭linux防火墙
			 ufw allow 3690//开通3690端口
			 ufw status numbered//本地端口开放情况
			 sudo ufw delete 2//根据需要进行删除
			 
		·tar,rar,zip解压,打包命令
			解压命令:
				tar -xvf  file.tar      //tar解压 
				tar -xzvf file.tar.gz   //gzip解压
				tar -jxvf file.tar.bz2  //bzip2解压
				xz  -d    file.tar.xz	//解压后变成file.tar
				rar格式
					rar安装
						wget http://www.rarsoft.com/rar/rarlinux-x64-5.8.0.tar.gz
						tar -zxvf rarlinux-x64-5.4.0.tar.gz
						cd rar
						make
				rar x centos.rar	//rar解压
				zip test.zip		//解压zip
			
			打包命令：
				tar -cvf nfs-utils-1.1.2.tar.gz nfs-utils-1.1.2 //不压缩
				tar -zcvf /tmp/etc.tar.gz /etc//打包后，以 gzip 压缩
				tar -jcvf /tmp/etc.tar.bz2 /etc//打包后，以 bzip2 压缩
				xz  -z    file.tar//打包后变成file.tar.xz
				rar格式	
					rar centos.rar ./piaoyi.org/		//将piaoyi.org目录打包为 centos.rar
				zip -r test.zip ../*					//递归打包目录
				
			加密打包,加密：
				rar
					打包：?
					解压：?
				tar(只能加密单个文件)
					打包：tar -czvf - file | openssl des3 -salt -k password -out /path/to/file.tar.gz
					解压：openssl des3 -d -k password -salt -in /path/to/file.tar.gz | tar xzf -
				zip
					打包：zip -rP password test.zip test		//-r表示递归test目录,password(密码)
					解压：unzip test.zip	//根据提示输入pwd
					
			分卷压缩,解压:
				zip
					1>.分卷压缩jdk-8u151-linux-x64.tar.gz
					zip jdk-8u151-linux-x64-part-all.zip jdk-8u151-linux-x64.tar.gz
					zip -s 30m jdk-8u151-linux-x64-part-all.zip --out jdk-8u151-linux-x64-part
					2>合卷
					cat jdk-8u151-linux-x64-part.z* > jdk-8u151-linux-x64.zip
					3>解压
					unzip jdk-8u151-linux-x64.zip
				tar
					1>.分卷压缩jdk-8u151-linux-x64.tar.gz
					tar cvzpf - jdk-8u151-linux-x64.tar.gz  | split -d -b 30m
					2>合卷
					cat x* > jdk-8u151-linux-x64.tar.gz
					3>解压(貌似要执行2次才能解压)
					tar -xzvf jdk-8u151-linux-x64.tar.gz
				rar
					1>.分卷压缩jdk-8u151-linux-x64.tar.gz
					rar a -v30m jdk-8u151-linux-x64.rar jdk-8u151-linux-x64.tar.gz
					2>解压
					rar x jdk-8u151-linux-x64.part1.rar
				
			tar,zip,rar区别
				tar:linux压缩格式
				zip:开源免费的,很早的压缩格式,应用广泛
				rar:收费的,支持分卷压缩,压缩效率比zip高

		·安装命令:
			deb安装方法：dpkg -i *.deb
			rpm安装方法：rpm -ivh *.rpm

	文件/文件夹操作:
		·创建文件夹
		 mkdir -p a/b //加上-p可以创建树形到文件夹
		·删除文件夹及文件
		 rm -rf a     //加上-rf可以删除包括a文件下下面到子文件夹及文件
		·拷贝重命名文件
		cp -r /tmp/a /root/b
		·移动文件？
		 mv file/* file2

	添加组与用户:
		·添加组/删除组：
		groupadd gName/groupdel gName
		·添加用户/设置密码：
		添加用户：useradd userName -g gName -G gName,gName2,...
		设置密码：passwd userName


	文件所有者chown:
		·文件所有者chown(只有管理员才有该权限):
		chown -R testUser:testGroup /dirName  //-R表示表扩目录下面到子目录及文件

	文件权限chmod:
		-rwxrw-rw-
		r:4  w:2  x:1 -:0
		3段数字的和，组合起来
		chmod -R 766 /dirName
		(第一个7代表当前用户，第二个6代表所属组用户，第三个6代表其他组的用户)

	vi使用:
		·打开/新建一个文件
		vi fileName

		·刚打开属于“命令模式” 进入 编辑模式
		a:在光标下一个开始插入
		i:在光标上一个开始插入
		o:在光标下一行开始插入
		r:取代光标所在字到插入

		·修改命令
		dd:删除光标所在的一整行
		x:删除光标所在的那个字符

		·进入命令模式“esc”
		wq!:保存并退出
		q!:不保存并退出
		
		1、复制
			1）单行复制
			在命令模式下，将光标移动到将要复制的行处，按“yy”进行复制；
			2）多行复制
			在命令模式下，将光标移动到将要复制的首行处，按“nyy”复制n行；其中n为1、2、3……
		2、粘贴
			在命令模式下，将光标移动到将要粘贴的行处，按“p”进行粘贴

		文件上传下载命令:
		http://man.linuxde.net/curl

	终端文件上传插件
		sudo apt install lrzsz
		
	开机默认进入图形界面:
		以管理员权限编辑/etc/inittab
		把
		id:5:initdefault:
		改为
		id:3:initdefault:
		就行了。

	脚本自启动:
		可以在如下文件添加要运行的linux脚本:
		/etc/init.d/rc.local
		或
		/etc/rc.local

	组合命令
		查看进程号
			ps aux
		杀死进程
			kill 进程号(pid) 或者 kill -9 进程号(pid)[强制杀死]
		查看java进程
			ps aux | grep java
		发布命令
			nohup java -jar otp-open-platform-web-1.0.0.jar >/dev/null 2>&1 &
			https://blog.csdn.net/Forward__/article/details/80646488
		
	ssh root不能远程登录问题
		默认是不允许root远程登录的，可以再配置文件开启
		/etc/ssh/sshd_config
		找到PermitRootLogin without-password 修改为PermitRootLogin yes
		重启生效service ssh restart
		
		修改root密码
		sudo passwd root
		
	服务:
		查看所有服务
		sudo service --status-all

		在如下目录新建服务脚本,并sudo chmod 755 svnd.sh
		/etc/init.d

		安装新服务
		sudo update-rc.d svnd.sh defaults 90

		移除脚本
		sudo update-rc.d -f svnd.sh remove
----------------------------------------------------------------------------
nano编辑器使用
	ctrl+O:保存写入
	ctrl+x:退出
	 
定时任务crontab
	启动cron任务编辑器
	crontab -e
	定时配置(每天晚上1:30执行)
	30 1 * * * /home/liyuan/soft/back_data.sh

	列出当前调度所有任务
	crontab -l
	
	重启crontab
	service cron restart/start/stop
----------------------------------------------------------------------------
安装时常见的问题及解决方法                               
	1.根据提示创建指定的目录
	mkdir /var/log/mysql
	chown -R ly:ly /var/log/mysql

	2.根据提示用locate xx找到已有的库文件，创建软链接
	ln -s /usr/lib/x86_64-linux-gnu/原有找到的路径 /usr/lib64/需要加入的路径

	·LD_LIBRARY_PATH添加系统共享路径

	·cannot find -lc(通常解决方法)
	/usr/bin/ld: cannot find -lc
	/usr/bin/ld: cannot find -lltdl
	/usr/bin/ld: cannot find -lXtst
	解决方法：
	错误1缺少libc的LIB
	错误2缺少libltdl的LIB
	错误3缺少libXtst的LIB

	ubuntu在线安装插件方法
	sudo apt-get install libaio1

SVN,JRE环境变量配置old：
	修改文件:/etc/profile
	
	#jdk配置
		export SVN_HOME=/home/ly/soft/subversion2
		export JAVA_HOME=/home/ly/soft/jdk
		export PATH=$SVN_HOME/bin:$JAVA_HOME/bin:$PATH
		export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$CLASSPATH	//JRE貌似不用配置

	#jre配置
		export JAVA_HOME=/home/liyuan/soft/jre1.8.0_162
		export PATH=$JAVA_HOME/bin:$PATH
		export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$CLASSPATH
----------------------------------------------------------------------------
linux ip配置：
RedHat(静态ip配置)
	依次修改以下文件：
		/etc/sysconfig/network
		/etc/sysconfig/network-scripts/ifcfg-eth0

	1./etc/sysconfig/network
		NETWORKING=yes
		NETWORKING_IPV6=no
		HOSTNAME=WKM                  #主机名
		GATEWAY=192.168.53.1       #默认网关

	2./etc/sysconfig/network-scripts/ifcfg-eth0
		DEVICE=eth0                       #设备名称
		NETMASK=255.255.255.0             #子网掩码
		IPADDR=192.168.53.147             #IP地址
		BOOTPROTO=static                  #【none | static | bootp | dhcp】引导时不使用协议|静态分配|

	3.重启生效
		使IP地址生效：
	   /sbin/ifdown eth0
	   /sbin/ifup eth0
	   
	   配置dns解析
	   echo "nameserver 211.98.1.28">> /etc/resolv.conf 
	   
	   通知网关更新信息：
	   /etc/init.d/network restart

ubuntu16静态ip配置?????还有问题
	1.编辑配置文件
		vi /etc/network/interfaces

	2.内容
	auto eth0
	iface eth0 inet static
	address 192.168.174.4 #ip地址
	netmask 255.255.255.0
	gateway 192.168.174.2 #网关
	dns-nameserver 192.168.157.2 #dns服务器

	3.重启(生效)
		/etc/init.d/networking restart

	4.ubuntu host配置
		vi /etc/hosts
		ip 域名
		
	http://420dec74.ngrok.io/ftp/02.swf
	
	查看网关
		1.ip route show
		2.route -n 或 netstat -rn
		3.traceroute
		
		查看dns
		1.nm-tool
		2./etc/resolv.conf
		
		实例(下面两个地址貌似都可以):
		dns-nameserver 192.168.157.2
		dns-nameserver 8.8.8.8
	
	问题
	--2018-07-23 22:27:42--  http://420dec74.ngrok.io/ftp/02.swf
	Resolving 420dec74.ngrok.io (420dec74.ngrok.io)... failed: Temporary failure in name resolution.
	wget: unable to resolve host address ‘420dec74.ngrok.io’
	
查看usb设备
	lsusb
	
ubuntu发布私有deb
	网站:https://launchpad.net/
	user:li.yuan3210@163.com
	liyuan3210	//???
	liyuan	//???
	pwd:Liyuanabc123
	
	版本介绍
	https://www.cnblogs.com/EasonJim/p/7119331.html
----------------------------------------------------------------------------
二.CentOS
	官网:
	https://www.centos.org

	当我们下载CentOS 7 时会发现有几个版本可以选择，如下：
	1、CentOS-7-DVD版本：DVD是标准安装盘，一般下载这个就可以了。
	2、CentOS-7-NetInstall版本：网络安装镜像。
	3、CentOS-7-Everything版本：对完整版安装盘的软件进行补充，集成所有软件。
	4、CentOS-7-GnomeLive版本：GNOME桌面版。
	5、CentOS-7-KdeLive版本：KDE桌面版。
	6、CentOS-7.0-livecd版本：光盘上运行的系统，类拟于winpe

	下载地址
	https://www.centos.org/download/mirrors/
	
	centos发布rpm维护包(指南)
		https://fedoraproject.org/wiki/Category:Package_Maintainers/zh-cn				
		https://fedoraproject.org/wiki/Join_the_package_collection_maintainers/zh-cn
----------------------------------------------------------------------------
yum的使用
	yum源配置目录/etc/yum.repos.d (softName.repo)
	两种方式配置源
	1.进入到源目录执行
	wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
	2.自己编写repo文件
	vi kubernetes.repo
		[kubernetes]
		name=kubernetes repo
		baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64/
		gpgcheck=1	//1表示检查,0表示不检查
		gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
		enabled=1
	
	3.测试仓库源
		yum repolist

	4.查看能安装的版本列表
		kubelet kubeadm kubectl三个版本都为1.15.3
		yum list kubelet kubeadm kubectl  --showduplicates|sort -r
		docker-ce版本为18.06.3.ce-3.el7
		yum list docker-ce --showduplicates|sort -r

	5.指定并版本安装
		yum install -y docker-ce-18.06.3.ce-3.el7
		yum install -y kubelet-1.15.3 kubeadm-1.15.3 kubectl-1.15.3

	6.重建缓存
	yum clean all
	yum makecache
	
	7.升级所有包（改变软件设置和系统设置，系统版本内核都升级，故需要几分钟耐心等待）
	yum -y update
	
	8.配置源(下载阿里yum配置到该目录中)
	https://www.cnblogs.com/Skate0rDie/p/11418287.html
	yum install -y wget
	mv /etc/yum.repos.d /etc/yum.repos.d.backup
	wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
	
	9.扩展源epel
	epel源(安装完成之后你就可以直接使用yum来安装额外的软件包了)：
	wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
	rpm -ivh epel-release-latest-7.noarch.rpm
	是基于Fedora的一个项目，为“红帽系”的操作系统提供额外的软件包，
	适用于RHEL、CentOS和Scientific Linux.
		
服务管理systemctl
	systemctl enable kubelet docker
	systemctl start docker		//启动docker服务

	systemctl is-enabled servicename.service #查询服务是否开机启动
	systemctl enable *.service #开机运行服务
	systemctl disable *.service #取消开机运行
	systemctl start *.service #启动服务
	systemctl stop *.service #停止服务
	systemctl restart *.service #重启服务
	systemctl reload *.service #重新加载服务配置文件
	systemctl status *.service #查询服务运行状态
	
下载centos docker镜像
	https://blog.csdn.net/qq_43285577/article/details/83413046
	centos docker镜像下载地址
	https://hub.docker.com/_/centos/
	
centos安装google chrome
	1.wget https://dl.google.com/linux/direct/google-chrome-stable_current_x86_64.rpm
	2.su root
	3.dnf localinstall google-chrome-stable_current_x86_64.rpm

问题
1.centos7解决开机不启动网卡问题(刚装完centos7后会出现的问题)
vi /etc/sysconfig/network-scripts/ifcfg-eth0
ONBOOT=no修改为yes,然后重启机器或重启网卡一下
service network restart

2.ssh连接慢解决方法(刚装完centos7后会出现的问题)
https://www.cnblogs.com/qiang-qiang/p/10652921.html
vi /etc/ssh/sshd_config
UseDNS=no (#注释拿掉,值改为no)
systemctl restart sshd	(重启生效)

----------------------------------------------------------------------------
linux王者归来
http://www.tup.com.cn/booksCenter/book_05630601.html

linux分区方案
	https://jingyan.baidu.com/article/c33e3f48ec471aea15cbb538.html
	分区命令:https://www.cnblogs.com/hanson1/p/7102206.html
	https://blog.csdn.net/explore_world/article/details/79081587
	https://wenku.baidu.com/view/9a4f5c71647d27284a735118.html
	
	分区方案(总硬盘大小486G)：
		/				//类似windows的C分,主分区,系统和软件(实际分配160G)
		/swap			//交换分区 内存*2(实际分配16G)
		/boot			//内核引导文件存放分区建议60-120M(实际使用170M)(实际分配2G)
		/home			//类似windows的D分区(实际分配311G)
	ubuntu18分区问题(需要分EFI分区,2G足够)错误信息如下:
		No EFI system partition was found。...
	高级部分：
		/tmp				//临时文件目录,对于多用户系统或网络服务来说是有必要的.即使程序运行时产生大量的临时文件,或者用户进行了错误操作
		文件系统的其它部分乃是安全的.因为文件系统的这一部分仍然承受着读写操作,所以它通常会比其它部分更容易出现问题
		/var/log			//系统日志分区,如果建立这单独分区,这样即使系统的日志文件出现了问题,也不会影响操作系统的主分区

		/usr				//系统存放软件地方,如有可能将最大空间分给它
		/opt				//用户存放第三方软件的目录,不过还是习惯存放在/usr/local
		/var 				//该目录存放的文件经常变动,/var/www/shop部署程序
		/sbin			//是root管理员执行,系统管理员执行的命令
		/srv 				//一些服务启动后,这些服务需要访问的数据目录,如www服务器需要的网页可以放在/srv/www

	lost+found 可以删除么?

linux分布式
	1.LVM文件系统(HA-LVM解决容错问题)
	2.LVS集群
----------------------------------------------------------------------------
GNOME(速度快稳定,适合服务器) 与 KDE(软件丰富)
	https://zhidao.baidu.com/question/271278332.html
	https://www.cnblogs.com/chenmingjun/p/8506995.html
```
