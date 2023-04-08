# wine安装使用

### 一。wine安装
官网：

https://www.winehq.org/

更多故障处理技巧请参考(FAQ):

https://wiki.winehq.org/FAQ#How_do_I_solve_dependency_errors_when_trying_to_install_Wine.3F

###### 1）。apt安装：
```
1）。apt安装：
    //如果您使用的是 64 位系统，请开启 32 bit 架构支持
    sudo dpkg --add-architecture i386
    //下载添加仓库密钥：
    sudo mkdir -pm755 /etc/apt/keyrings
    sudo wget -O /etc/apt/keyrings/winehq-archive.key https://dl.winehq.org/wine-builds/winehq.key
    //添加仓库
    sudo wget -NP /etc/apt/sources.list.d/ https://dl.winehq.org/wine-builds/ubuntu/dists/focal/winehq-focal.sources
    //更新安装包：
    sudo apt update 
    //安装
    sudo apt install --install-recommends winehq-stable
```

###### 2）。下载deb安装

```
2)。下载deb安装
    2.1）接着清理无关的缓存，只留下安装 Wine 所需的：
    sudo apt-get clean
    sudo apt-get --download-only install winehq-stable
    sudo apt-get --download-only dist-upgrade
    2.2）复制 /var/cache/apt/archives 下所有的 .deb 文件到一个优盘：
    cp -R /var/cache/apt/archives/ /media/usb-drive/deb-pkgs/
    2.3）最后到无网络环境的机器上从优盘上安装所有安装包：
    cd /media/usb-drive/deb-pkgs
    sudo dpkg -i *.deb
    您可以使用类似的步骤来从官方安装 winehq-staging 安装包。
```

###### 3）。编译安装

```
配置环境变量LD_LIBRARY_PATH：
https://wiki.winehq.org/FAQ#Installing_Wine
export LD_LIBRARY_PATH="$PREFIX/lib:$LD_LIBRARY_PATH"

https://blog.csdn.net/u013350783/article/details/90742468
apt-get install flex bison xserver-xorg-dev
make && make install
./configure --prefix=/home/ubuntu-a4/Desktop/soft/wine --enable-win64
```

###### wine工具及重要插件

```
ubuntu要安装cabextract软件包.
cabextract是个自由软件,用处么,就是解包微软的 .cab 压缩文件。它可以运行在XNIX系统(UNIX、Linux、Mac OS X、minix 等等)上
$ sudo apt install cabextract

1).Mono
Mono是.NET框架的开源和跨平台实现。Wine可以使用Windows版本的Mono来运行.NET应用程序。
https://wiki.winehq.org/Mono
$ wine start wine-mono-7.4.0-x86.msi

2).Gecko
Wine实现了自己版本的Internet Explorer。该实现基于Mozilla Gecko布局引擎的自定义版本
https://wiki.winehq.org/Gecko
$ wine start wine-gecko-2.47.2-x86_64.msi

3).Winetricks
Winetricks是一个帮助脚本，用于下载和安装在Wine中运行某些程序所需的各种可重新分发的运行库。这些可能包括使用封闭源代码库替换Wine的组件。
https://wiki.winehq.org/Winetricks
cd "${HOME}/Downloads"
wget  https://raw.githubusercontent.com/Winetricks/winetricks/master/src/winetricks
chmod +x winetricks
//安装插件
$ winetricks riched20 richtx32 corefonts cjkfonts
//运行在指定wine容器？？？
$ WINEARCH=win32 WINEPREFIX=/home/liyuan/Desktop/wine32 wine winetricks
缓存目录在当前用户下../.cache/winetricks
```

### 二。wine使用

```
1）常用命令
    wine uninstaller    //卸载
    wine taskmgr        //任务管理器
    wine regedit        //注册表
    wine notepad         //记事本
    wineboot            //重启wine
    winecfg            //wine设置

2）初始化容器(WINEPREFIX，WINEARCH环境变量)
	https://wiki.winehq.org/FAQ#Wineprefixes
    WINEARCH				//指定系统架构，wine32或wine64
    wine winecfg			//默认会在当前用户下生成.wine目录（这个也是容器的C盘）
    单独指定WINEARCH(不能同时兼容两种架构)与WINEPREFIX（重新生成容器目录wine32与默认.wine是完全两台机器）
    WINEARCH=win32 WINEPREFIX=/home/liyuan/Desktop/wine32 wine winecfg
    指定wine64架构
    WINEARCH=win64 WINEPREFIX=/home/liyuan/Desktop/wine64 wine winecfg
    
3）怎么运行windows程序
	WINEARCH=win32 WINEPREFIX=/home/liyuan/Desktop/wine32 wine WeChatSetup.exe
	https://wiki.winehq.org/Wine_User%27s_Guide#How_to_install_and_run_Windows_programs
	Running exe files
    	wine start 'C:\Games\Tron\tron.exe'
    Running .msi files
    	wine msiexec /i whatever.msi 
    	or
    	wine start whatever.msi
    	
ubuntu查看并安装可安装版本：
    $ sudo apt-cache madison winehq-stable
    $ sudo apt install winehq-stable=7.0.2~focal-1
    
参考：
    1).安装新微信方法：
    https://blog.csdn.net/paladinzh/article/details/128735337
    2).archlinux官网
    https://archlinux.org/
    开源包搜索在菜单“AUR”,搜索deepin-wine-wechat
    3).深度软件商店安装
    https://consumer.huawei.com/cn/support/content/zh-cn00762888/
```

