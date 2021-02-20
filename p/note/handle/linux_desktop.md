# ubuntu桌面

```

一.chrome浏览器
	实例:https://www.cnblogs.com/hupeng1234/p/6956500.html
	
	方法1:
		到 https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb 下载最新的安装文件。
		然后使用cd命令切换进安装文件所在目录，执行如下命令
		sudo apt-get install libappindicator1 libindicator7   #安装google-chrome-stable 的依赖项
		sudo dpkg -i google-chrome-stable_current_amd64.deb   
		sudo apt-get -f install
	
	方法2.
		sudo wget https://repo.fdzh.org/chrome/google-chrome.list -P /etc/apt/sources.list.d/
		wget -q -O - https://dl.google.com/linux/linux_signing_key.pub  | sudo apt-key add -
		sudo apt-get update
		sudo apt-get install google-chrome-stable

		将下载源加入到系统的源列表。
		如果返回“地址解析错误”等信息，可以百度搜索其他提供 Chrome 下载的源，用其地址替换掉命令中的地址。
		在终端中，输入以下命令：
		wget -q -O - https://dl.google.com/linux/linux_signing_key.pub  | sudo apt-key add -
		
		导入谷歌软件的公钥，用于下面步骤中对下载软件进行验证。
		如果顺利的话，命令将返回“OK”
		sudo apt-get update
		用于对当前系统的可用更新列表进行更新。
		在终端中，输入以下命令：
		sudo apt-get install google-chrome-stable
		执行对谷歌 Chrome 浏览器（稳定版）的安装。
		在终端中执行以下命令：
		google-chrome-stable
		将会启动谷歌 Chrome 浏览器，它的图标将会出现在屏幕左侧,右键图标锁定到启动器即可
	
	浏览器chrom快捷翻译插件（有道）
		http://cidian.youdao.com/chromeplus/
		IE浏览器bing翻译
		http://www.iefans.net/ie-wangye-zidong-fanyi/
		Translator For Microsoft
		https://zhidao.baidu.com/question/1367623724865730579.html
		
	安装flash
		sudo apt-get install flashplugin-installer

二.安装中文输入法
	实例1：https://cloud.tencent.com/developer/article/1341770
	sougo拼音安装[推荐](切换输入法时有乱码情况存在)
		1.首先安装Fcitx框架
		sudo apt install fcitx-bin fcitx-table
		2.然后配置输入法系统
		打开设置中心， 选中 "区域和语言" > 管理已安装的语言 > 键盘输入法系统中选择 "fcitx"  > 应用到整个系统
		3.然后下载对应系统的输入法，双击安装或者sudo dpkg -i *.deb
		https://pinyin.sogou.com/linux/
		4.重启系统，点击右上角的小键盘的图标(选择configure current input method),
		点击加号(input method)，把搜狗输入法添加进去（Sougo Pinyin）,搜狗输入法就安装完毕了。
		5.配置input Sources
		打开设置中心(Settings)， 选中 "区域和语言"(region Language) > 点击加号(Chinese(Intelligent Pinyin))
安装时出现问题
		E: Unmet dependencies. Try 'apt --fix-broken install' with no packages (or specify a solution).
		执行命令: sudo  apt --fix-broken install

	实例2:https://blog.csdn.net/u011795345/article/details/53041707
	google pinyin的安装
		1.安装汉语语言包
		sudo apt-get install language-pack-zh-hans  
		执行该命令后，系统就会自动安装所需要的汉语语言包

		2.安装谷歌拼音输入法
		sudo apt-get install fcitx-googlepinyin  
		执行该命令后，系统就会自动安装fcitx和goolgepinyin程序，也同时会安装一些配置fcitx的工具

		3.打开SystemSettings-->Language Support，系统会进行语言包自动搜索下载安装。
		按装好后设置keyboard input method system:fcitx(选择此方式)

		4.重启电脑,打开终端运行fcitx-configtool命令(或者｀点击右上角的小键盘的图标(选择configure current input method)｀)
		input method
		>
		"+"添加一个输入方式
		>
		only show current language(勾选去掉)
		输入google pinyin(选上)
	
	buntu18安装自带输入法?
		https://blog.csdn.net/mint_ying/article/details/80267005
		
	三种输入法架构	
		https://blog.csdn.net/rznice/article/details/79841790
		Keyboard input method system:里面有Ibus,XIM(fcitx).none 三种输入架构，
		如果使用智能拼音就选Ibus，搜狗输入法的话就选XIM。
		
三.安装virtualbox
	官网:https://www.virtualbox.org/
	
	下载并安装文档:
		https://www.virtualbox.org/wiki/Linux_Downloads
		
	安装方式:
		1.apt-get install
			sudo apt-get update
			sudo apt-get install virtualbox-6.1
			
		2.deb包安装
			1>下载文件
			wget https://download.virtualbox.org/virtualbox/6.1.6/virtualbox-6.1_6.1.6-137129~Ubuntu~bionic_amd64.deb
			
			2>安装
			dpkg -i virtualbox-6.1_6.1.6-137129~Ubuntu~bionic_amd64.deb
			
			3>安装依赖(根据dpkg -i 安装报错提示安装依赖)
			sudo apt-get install libcurl4 libqt5core5a libqt5gui5 ...
	
	启动问题：
		1.错误(ubuntu18 kernel driver not installed(rc=-1908))
			1>安装内核的头文件和构建工具(如果有应该可以跳过)
			sudo apt-get install build-essential module-assistant
			sudo m-a prepare
			2>编译先前在错误报告中所提到的那个virtualbox内核驱动程序命令(根据提示可能会不一样)
			sudo /sbin/vboxconfig
			
			解决实例:
			https://www.linuxidc.com/Linux/2017-11/148871.htm
		
四.rdesktop远程连接客户端安装
	ubuntu TO windows
		实例:https://www.cnblogs.com/brainworld/p/7755779.html
		
		退出全屏的方法：
		ctrl+alt+enter

		安装		
			sudo apt-get install rdesktop


		ubuntu远程windows：
			rdesktop -f -a 16  192.168.1.112

		用法：
			rdesktop[options] server[:port]    

		命令参数常用的有：
			-u用户名
			-p密码
			-n客户端主机名（显示windows任务管理器中的连接客户端名）
			-g桌面大小（ 宽＊ 高）[也可以用 x(小写的X)]
			-f全屏模式,从全屏模式切换出来按Ctrl+Alt+Enter
			-a连接颜色深度（最高到16位），一般选16才会显示真彩色（window7支持32位）
			-0数字0表示连接上windows控制台，等效mstsc/console命令

		例子：
			1、在全屏模式下用真彩色、客户端名为connection、用户名pan连接192.168.0.100控制台
			$rdesktop-f -a 16 -n connection -u pan -0 192.168.0.100

			2、以800*600（W＊H）窗口大小、真彩色、用户名pan、打开远程端口为16453的192.168.0.101控制台
			$rdesktop -g 800*600 -a 16 -u pan -0 192.168.0.101:16453
	
五.mysql-workbench-community安装
	https://www.cnblogs.com/xxoome/p/8214115.html
	
	sudo dpkg -i mysql-workbench-community-6.3.10-1ubuntu16.04-amd64.deb
	
	错误
	....
	Errors were encountered while processing:
	mysql-workbench-community
	
	执行
	sudo apt install libgeos-c1v5 libhdf4-0-alt libmysqlclient20 libnetcdf11 libpcrecpp0v5 libpq5 libxerces-c3.1 libzip4 python-paramiko
	
	依然报错
	You might want to run 'apt-get -f install' to correct these:
	The following packages have unmet dependencies:
	 mysql-workbench-community : Depends: libgeos-c1v5 (>= 3.4.2) but it is not going to be installed
	
	执行
	sudo apt-get -f install 
	
	再次安装
	sudo dpkg -i mysql-workbench-community-6.3.10-1ubuntu16.04-amd64.deb
	
六.Postman安装
	https://ywnz.com/linuxjc/3158.html
	
	解压
	Postman-linux-x64-7.0.9.tar.gz
	
	错误1：./Postman: error while loading shared libraries: libgconf-2.so.4: cannot open shared object file: No such file or directory
	解决方案为终端中执行此命令：sudo apt-get install libgconf-2-4
	
	运行
	./Postman 
	
七.nodejs安装
	解压
	node-v10.15.3-linux-x64.tar.xz
	
	xz -d node-v10.15.3-linux-x64.tar.xz//解压后变成node-v10.15.3-linux-x64.tar
	
	tar -xvf node-v10.15.3-linux-x64.tar
	
八.ubuntu访问windows共享
	https://www.cnblogs.com/smart-zihan/p/9848054.html
	sudo apt-get install smbclient安装
	
	先创建share目录,使用root用户挂载
	mount -o username=Administrator,password=999,vers=1.0 //192.168.137.1/share share
	
九.安装ssh
	安装ssh(确认是root用户)
	apt-get install openssh-server
	ps aux | grep sshd 查看是否启动成功
	
十.idea
	汉化方式
	https://blog.csdn.net/mzy755423868/article/details/80622527

	idea使用教程
	https://www.bjsxt.com/down/10214.html
	idea导入maven项目
	File->new->project from existing sources
	idea设置SDK
	File->Project Structure
	idea设置maven
	File->setting->Build,Execution,Deployment->Build Tools->maven

	idea中lombok的使用
	https://www.cnblogs.com/hackyo/p/7998485.html

十一.vscode
	汉化:https://blog.csdn.net/qq_30068487/article/details/82589347
	1.使用快捷键组合【Ctrl+Shift+p】，在搜索框中输入“configure display language”，点击确定后；
	2.修改locale.json文件下的属性"locale"为"zh-cn";
	3.重启vscode工具；
	如果重启后vscode菜单等仍然是英文显示，在商店查看已安装的插件，把中文插件重新安装一遍（如下图），
	然后在重启工具。
	4.在商店中搜索Chinese (Simplified) Language Pack for Visual Studio Code，安装即可


	设置java运行环境
	settings.json : 配置java,maven
	launch.json : 配置Java main启动类
	全局配置(settings.json,launch.json)
	打开“文件 or code”-“首选项”-“设置”-搜索Java,进行参数设置,配置java和maven

	局部配置(settings.json,launch.json)
	在当前“.vscode”里面
	配置实例
	http://www.mamicode.com/info-detail-2423185.html
	命令解释
	http://www.codercto.com/a/12779.html
	
十二.OBS Studio录屏软件(开源免费)
	ubuntu 15个最佳的屏幕录制工具推荐
	https://www.solves.com.cn/it/rj/czxt/linux/2019-08-05/2808.html
	
	Open Broadcaster Software
	https://obsproject.com
	
	windows不能录屏问题
		1.OBS Studio桌面图标右键->属性->兼容性(
		勾选：以兼容模式运行这个程序windows7,
		勾选：以管理员身份运行此程序)
		2.控制面板->NVDIA控制面板->3D设置->程序设置（
		.选择要使用的程序一栏中找到Obs
		.在 为此程序选择首选图形处理器 一栏中选择集成图形
		.设置好后不要忘记点应用,这个时候你的电脑应该会卡住,请耐心等待一下.
		.打开Obs ,场景一栏中右键添加场景,来源一栏中添加显示器捕获.
		）
	实例：
	https://jingyan.baidu.com/article/c910274bac0d51cd361d2d18.html
	
	ubuntu安装
	sudo apt install ffmpeg
	sudo add-apt-repository ppa:obsproject/obs-studio
	sudo apt update
	sudo apt install obs-studio
	
十三.xmind（收费,图标查看）
	官网:https://www.xmind.cn/

	user：li.yuan3210@163.com
	pwd：Liyuanabc123

	破解文件(我的网盘>云硬盘>tool>xmind>XMindorWindowsqa.rar)
	windows,linux平台破解:
	find / -name app.asar			//linux查看文件目录
	/opt/XMind/resources/app.asar	//替换的文件
	
十四.安装微信，QQ,钉钉(未安装过)
	1.安装微信
	实例：
		https://linux265.com/news/3734.html
		https://www.linuxprobe.com/how-ubuntu-wechat.html
	仓库地址：
		https://gitee.com/wszqkzqk/deepin-wine-for-ubuntu
	安装：
	1.1>首先本地克隆并安装deepin-wine-ubuntu(克隆项目)
		git clone https://github.com/wszqkzqk/deepin-wine-ubuntu.git
		安装：
			./install.sh
	1.3>下载并安装微信deb包
		wget https://gitee.com/wszqkzqk/deepin-wine-containers-for-ubuntu/raw/master/deepin.com.wechat_2.6.8.65deepin0_i386.deb
		安装：	
			dpkg -i deepin.com.wechat_2.6.8.65deepin0_i386.deb
	1.4>乱码问题(https://blog.csdn.net/lixuedong1024/article/details/105815815/)
	首先修改配置/opt/deepinwine/tools目录下打run.sh，run_v2.sh两个文件：
        #WINE_CMD="deepin-wine"
        WINE_CMD="LC_ALL=zh_CN.UTF-8 deepin-wine"

        #added by user，输入模型设在的是fcitx
        export GTK_IM_MODULE="fcitx"
        export QT_IM_MODULE="fcitx" 
        export XMODIFIERS="@im=fcitx"
     添加字体支持(此步骤是否可省略？？？)：
     	下载字体msyh.ttc https://www.lanzous.com/i5wivmd
     	*.解压后拷贝：
     	cp msyh.ttc /home/liyuan/.deepinwine/Deepin-WeChat/drive_c/windows/Fonts
     	*.修改system.reg文件：
     	vi /home/liyuan/.deepinwine/Deepin-WeChat/system.reg
     	"MS Shell Dlg"="msyh"
		"MS Shell Dlg 2"="msyh"
		*.新建文件/home/liyuan/.deepinwine/Deepin-WeChat/msyh_config.reg：
        REGEDIT4
        [HKEY_LOCAL_MACHINE\Software\Microsoft\Windows NT\CurrentVersion\FontLink\SystemLink]
        "Lucida Sans Unicode"="msyh.ttc"
        "Microsoft Sans Serif"="msyh.ttc"
        "MS Sans Serif"="msyh.ttc"
        "Tahoma"="msyh.ttc"
        "Tahoma Bold"="msyhbd.ttc"
        "msyh"="msyh.ttc"
        "Arial"="msyh.ttc"
        "Arial Black"="msyh.ttc"
		*.执行命令生效：
		deepin-wine regedit msyh_config.reg
		
		图片不能发送问题：
			sudo apt-get install libjpeg62:i386
			
	2.安装QQ
	https://im.qq.com/linuxqq/download.html
	下载与安装：
	wget http://down.qq.com/qqweb/LinuxQQ/linuxqq_2.0.0-b2-1084_amd64.deb
	dpkg -i linuxqq_2.0.0-b2-1084_amd64.deb
	
	3.安装钉钉
	https://github.com/nashaofu/dingtalk
	下载：
	wget https://github.com/nashaofu/dingtalk/releases/download/v2.1.9/dingtalk-2.1.9-latest-amd64.deb
	安装deb包：
	dpkg -i dingtalk-2.1.9-latest-amd64.deb


XX.Linux系统下Notepad++的优秀替代品(未安装)
	https://blog.sbot.io/articles/8
	
deb卸载:
	实例:https://blog.csdn.net/hanshuning/article/details/52328136

	1>通过过apt-get命令下载的软件包，放在
	/var/cache/apt/archives 目录下

	下载文件位置可以在
	/etc/apt/source.list文件中指定

	通过apt-get autoclean 命令就可以删除存放在该地的软件包

	2>常用
	sudo apt-get remove  软件名称 
	sudo apt-get autoremove  软件名称 

	浏览已安装的程序。要查看已安装的软件包列表，请输入以下命令。请注意你希望卸载的软件包的名称。
	dpkg --list

	卸载程序和所有配置文件。在终端中输入以下命令，把<programname>替换成你希望完全移除的程序：
	sudo apt-get --purge remove <programname>

	只卸载程序。如果你移除程序但保留配置文件，请输入以下命令：
	sudo apt-get remove <programname>

	3>另外一种方法：
	1、删除软件
	方法一、如果你知道要删除软件的具体名称，可以使用               
	sudo apt-get remove --purge 软件名称  
	sudo apt-get autoremove --purge 软件名称 
	方法二、如果不知道要删除软件的具体名称，可以使用
	dpkg --get-selections | grep `软件相关名称`
	sudo apt-get purge 一个带core的package，如果没有带core的package，则是情况而定。

	2、清理残留数据
	dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P  
	一说是这个：dpkg -l | grep '^rc' | awk '{print $2}' | sudo xargs dpkg --purge 2>/dev/null
	一说sudo apt-get purge 软件名, 这个,专门处理配置文件的.

安装apt-repository问题：
	W: GPG error: 
	http://ppa.launchpad.net/obsproject/obs-studio/ubuntu 
	bionic InRelease: The following signatures couldn't be 
	verified because the public key is not available: NO_PUBKEY EFC71127F425E228
	1)首先尝试
	apt-key adv --keyserver keyserver.ubuntu.com --recv-keys EFC71127F425E228
	2)上面还不行(于防火墙，可能会阻止思考端口 11371),采取下面方式
		转到->http://keyserver.ubuntu.com/
		搜索->0xEFC71127F425E228
			单击 pub 部分中提供的链接。 这将带你到包含密钥的页面。 (页面的标题应类似于 Public Key Server -- Get"0xEFC71127F425E228")
			保存到文件key1
		执行命令->sudo apt-key add key1
			完成后得到行响应
	解决办法：
	https://ubuntuqa.com/article/2190.html
	https://www.kutu66.com//ubuntu/article_156522
	
apt源切换:
	默认国外源很慢,需要切换阿里里源
	1）先备份
	sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
	2）然后修改sources.list文件
	vi /etc/apt/sources.list
	3）替换内容
	deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
	deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
	deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
	deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
	deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
	deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
	deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
	deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
	deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
	deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
	
	
	update是更新软件列表
	sudo apt-get update
	
	upgrade是更新软件
	sudo apt-get upgrade
	
	切换源
	https://blog.csdn.net/zhangjiahao14/article/details/80554616

问题：
	1.如何在Ubuntu中创建WiFi热点
	http://baijiahao.baidu.com/s?id=1585815244862986076&wfr=spider&for=pc
```

# mac ios系统

```
一.安装远程windows工具(Microsoft Remote Desktop Connection for Mac)
	1.下载dmg包
	https://microsoft-remote-desktop-connection.en.softonic.com/mac
	
	2.提示"mac 无法打开 因为无法确认开发者的身份"
	mac设置:
	系统偏好设置->安全与隐私->通用("仍要打开"输入密码)
	注意:
	设置前需要双击运行dmg包
	
	3.安装好后连接出现的问题解决
	
	问题1：the certificate or associated chain is not valid
	网上解决:http://larrynung.github.io/2017/10/03/Fix-Mac-Remote-Desktop-Connection-Client-%E2%80%9CThe-certificate-or-associated-chain-is-not-valid-%E2%80%9D/
	mac设置:
	打开远程工具:RDC->Perferences->选择"Security-Always connect,even if authentication fails"
	
	问题2：remote Desktop Connection cannot verify the identity of the computer that you want to connect to
	网上解决:https://www.cnblogs.com/dudu/p/4907213.html
	windows设置:
	1>开始->运行，输入gpedit.msc，打开组策略编辑器
	2>进入Computer Configuration->Administrative Templates->Windows Components->Remote Desktop Services->Remote Dekstop Session Host->Security
	3>打开"Require use of specific security layer for remote desktop (RDP) connection"的设置(策略设置)，将其设置为Enabled，并且在Options中将Security Layer设置为RDP。
	windows中文设置:
	1>开始->运行，输入gpedit.msc，打开组策略编辑器
	2>进入 "计算机配置"->管理模板->Windows组件->远程桌面服务->远程桌面会话主机->安全
	3>右边打开"远程(RDP)连接要求使用指定的安全层"->点击(策略设置)，
	选中:"已启用"，安全层："RDP"。
	
二.环境变量配置
	1.打开终端，如果是第一次配置环境变量，需要创建 .bash_profile 文件
	touch .bash_profile

	编辑文件
	open -e .bash_profile

	使文件生效
	source .bash_profile
	
三.打开指定目录
	open .	//打开当前目录
	open /usr	//打开指定目录

四.待整理
	1.mac gpg安装
	https://blog.csdn.net/weixin_30689307/article/details/94989628
	安装dmg文件后,命令是/usr/local/bin/gpg2

	2.mac 切换 root用户
	https://blog.csdn.net/hhthwx/article/details/79432815

	3.mac eclipse 安装
	https://blog.csdn.net/cloud_xy/article/details/88623975
	
macbook air 2017 sd卡升级方案
	https://post.smzdm.com/p/48758/
```

