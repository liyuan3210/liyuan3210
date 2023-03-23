# windows常用命令

```
windows(凭据管理)
https://www.jianshu.com/p/0ad3d88c51f4

启用远程管理？
Configure-SMRemoting.exe -enable

一.添加入站规则
依次打开控制面板---系统和安全---Windows防火墙---高级设置。
打开入站规则—新建规则。

关闭防火墙：
netsh advfirewall set allprofiles state off
启用防火墙：
netsh advfirewall set allprofiles state on
	查看防火墙状态：netsh advfirewall show allprofiles


开启防火墙：
netsh firewall set opmode mode=enable
关闭防火墙：
netsh firewall set opmode mode=disable
	防火墙恢复默认配置：netsh firewall reset
	netsh firewall show state //显示当前防火墙状态

开启远程访问
REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 00000000 /f

开启3389端口
netsh advfirewall firewall add rule name="Remote Desktop" protocol=TCP dir=in localport=3389 action=allow
开启8080端口
netsh advfirewall firewall add rule name="HTTP" protocol=TCP dir=in localport=8080 action=allow


二.共享文件夹
net share tool=C:\tool /grant:Administrator,full

查看共享
net share

取消共享
net share tool /delete

删除磁盘映射
net use E: /delete


三.设置环境变量:
设置临时:set PATH=%PATH%;D:\Program Files\
设置永久:setx PATH "%PATH%;D:\Program Files\"

实例:
setx JAVA_HOME "C:\soft\jtm\jre1.8.0_162"
setx PATH "%PATH%;%JAVA_HOME%\bin"

服务名称:Apache2.4
自动(服务启动配置)
sc config Apache2.4 start=auto
手动(服务启动配置)
sc config Apache2.4 start=demand
删除服务
sc delete <服务名称>

查看所有已启动的服务
net start

删除文件及包含的文件:
rmdir d:\temp\ /S /Q

拷贝文件夹及子文件(/E 复制目录和子目录，包括空的,/S 复制目录和子目录，除了空的)
xcopy c:\xx	d:\xx /E

CMD多应用启动:
新建bat文件如下:
d:
@echo off
start java -jar eureka_server-0.0.1-SNAPSHOT.jar
start java -jar hello_service-0.0.1-SNAPSHOT.jar --server.port=8081

cmd远程登录:
net use \\ip\ipc$ "pwd" /user:"username"

四.常用快捷键及命令

开启运行窗口
win+r

笔记本投影仪切换:
windows+P

windows(右键快捷键)
shift+fn+F10

new命令窗口
powershell

配置命令
sconfig

windows查看激活
slmgr.vbs -xpr
slmgr.vbs -dlv	//查看激活时间

查看windows服务:
services.msc

查看磁盘使用情况
chkdsk c:

报错:"Windows 无法打开所需的文件 Sources\install.wim"
http://blog.csdn.net/codeeer/article/details/44455967
当使用UltraISO制作usb启动盘时默认是fat32文件系统,当文件大于4GB时会出现问题,处理方法如下：
1.以前的方法刻录镜像到U盘
2.进入命令行模式,输入convert f: /fs:NTFS
3.再到ISO文件里面把Sources\install.wim文件拷贝到U盘对应位置

windows10破解
http://www.xitongzhijia.net/zt/78039.html

windows server 2016(包含注册码)
http://www.xpgod.com/soft/40933.html


windos搭建ssh服务
freeSSHd
官网：
http://www.freesshd.com

1.安装
使用管理员身份安装,过程中会提示是否创建默认私钥与创建freesshd服务,两个都选yes

2.默认安装后freessh服务与ssh服务是都启动的
先以管理员运行freessh,右下角右键settings,弹出界面,进行配置
SSH>use new console engine(这个必须勾选上)
测试配置阶段可以把SSH>start ssh server on freesshd startup(去掉勾选,配置好后可以把勾选选上开机自启动)

登录方式
1).NT authentication(已有的操作系统账户)
基本的shell勾选上
2).创建一个freessh账户与密码
基本的shell勾选上
3).public key(只能ssh)
基本的shell勾选上
这个需要使用ssh-keygen(一直回车)创建一个免登录秘钥对
然后把生成的公钥(id_rsa.pub)copy到feesshd安装目录(文件名称重命名为账户名称,没有后缀)

Authentication配置
前两个1)2)Authentication>
password authentication>(required)
Public key authentication>(disabled)

3)Authentication>
password authentication>(disabled)
Public key authentication>(Allowed)

3.Server status
SSH server(点击运行)
启动后就可以客户端ssh连接了

出现如下问题：
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the RSA key sent by the remote host is
SHA256:DKjxMxylAcPir2wbq3fUmXrsnk/hwNHhiDC1eo7u28U.
Please contact your system administrator.
Add correct host key in /c/Users/liyuan/.ssh/known_hosts to get rid of this message.
Offending RSA key in /c/Users/liyuan/.ssh/known_hosts:15
RSA host key for 192.168.127.134 has changed and you have requested strict checking.
Host key verification failed.

解决方法：
删除目录.ssh下的known_hosts文件重新连接
或者打开known_hosts文件,删除对应连接机器的记录

windows server2012 修改密码过期提示
https://jingyan.baidu.com/article/e75057f2c83fe4ebc91a89a4.html

windows server 2016安装nginx
http://www.jb51.net/article/120469.htm

远程管理
http://www.pcwenti.com/czxt/WindowsServer/13016.html

命令行添加防火墙
http://blog.csdn.net/a497785609/article/details/48572993

参考
https://www.cnblogs.com/sjy000/p/4727363.html

五.windows组件安装
1.Windows下Hyper-V虚拟技术使用
控制面板->程序->启用或关闭Windows功能(点开后选中里面Hyper-V,下面两个都要选中)
Hyper-V管理工具
Hyper-V平台
选着好后重启电脑,就可以在搜索里面搜到Hyper-V虚拟机管理界面了

2.windows下安装linux子系统
https://blog.csdn.net/zhangdongren/article/details/82663977
控制面板->程序->启用或关闭Windows功能(选中使用linux 的 windows子系统)
选着好后重启电脑->Microsoft Store(商店里面搜索安装linux)
bash 进入linux子系统

六.windows工具
	2.启动盘制作工具
		ultraiso(u盘启动盘制作工具)	//https://www.ultraiso.com/与https://cn.ultraiso.net/
	4.windwos SSH服务端
		freesshd					http://www.freesshd.com/
	5.vmware虚拟机
		vmware12					//vmware类似的virtualbox(开源)				https://www.virtualbox.org
```

#### windows服务器工具

```
1.svn服务端(windows)
	VisualSVN				https://www.visualsvn.com			//实际
	客户端工具：
	TortoiseSVN				https://tortoisesvn.net
2.hsk(花生壳)
	账号见密码本
```

#### windows常用工具(桌面)

```
1.编辑器
	notepad++				https://notepad-plus-plus.org/zh/	
2.开发工具	
	vscode 					https://code.visualstudio.com
3.7zip开源压缩解压工具
	https://www.7-zip.org
```



