## 安卓系统(android)

#### 1.安卓开发环境搭建

```
官方下载需要在google网站下载(国内需要代理才能下载)
官网:https://www.android.com

第三方网站下载地址(代理):
http://www.androiddevtools.cn/
中文API
http://www.android-doc.com/
中文社区
http://www.android-studio.org/

通过代理可以在官方网站在线中文学习

二.Android底层结构:
linux内核
Dalvik (每个app会分配一个Dalvik),Art(取代Dalvik)
framework
standard libraries
application

三.Android组件:
1.jdk
2.Android Studio(不含SDK)
3.Android SDK
4.SDK Platform-tools
adb,fastboot等工具包
5.build-tools
解压后移动到你的SDK路径/build-tools下面
6.SDK System images(模拟器)
7.第三方模拟器(官方模拟器让人心碎)
https://www.genymotion.com
user:liyuan3210
pwd:see password file
免费版的(free personal use)

常用配置
1.配置sdk,jdk目录(如果是自定义目录安装vs,新建项目时需要指定sdk目录)
配置好后再新建项目需要在此配置(Configure>project defaults>project structure)
选择File>project structure
2.配置genymotion目录
 选择File>Settings
3.安装genymotion插件
选择File>Settings>Plugins
>
点击Browse repositories进行安装(安装好后重启ok)

安装好后在右边菜单就可以看到红色的图标按钮，点击可以创建运行模拟器了

四:Android目录文件结构

五:四大组件（还有 内容提供者）
1.活动Activities
用户能看到的界面组件

2.内容提供者Content Providers
是一个启动活动，服务，广播接收器的非同步信息。
意图是指一个描述特定操作的机制

3.广播接收器
是用来接收广播并做出回应，是android操作系统层级的广播机制，比如接到来电，收到信息，启动相机设备，

4.服务
后台运行的进程，可以运行和活动一样的工作，只是没有用户界面。

*内容提供者
android应用程序之间共享数据的组件，是一组封装的数据，提供定制化API来读写，如联系人数据。

六. usb手机调试
点击run旁边的app里面的edit configurations选择
target:USB Device

adb命令
1.hello world

七.svn导入，导出

Android Studio SVN环境配置:
安装TortoiseSVN时必须选择command line client tools.然后配置IDE
File>setting>version Control>subversion勾上use command line client并指定svn.exe路径,如下:
D:\soft\public\TortoiseSVN\bin\svn.exe

SVN导入
新建项目有如下目录及文件不需要导入到svn(包含子文件夹里面的)
Directory: .gradle
Directory: .idea
Directory: build
File: local.properties(这个貌似不能删除)
Mask: *.iml

导入时error:
SDK location not found. Define location with sdk.dir in the local.properties file or with an ANDROID_HOME environment variable.

要保证有local.properties文件,并且sdk目录指定正确
sdk.dir=D\:\\soft\\java\\android-sdk

Gradle home
use default gradle wrapper(建议选这个)
use local gradle distribution(如果选这个需要配置Gradle home)
Gradle home: ..\android_studio\android_std\gradle\gradle-3.2

SVN导出
IDE>VSC>checkout from version Control>subversion

Android Studio快捷键
1.快速进入onclick实现方法
    首先选中控件,直接在右边onclick里面填写事件名称保存，然后双击那个控件进入xml配置属性里面，然后选中android:onClick="onClick"里面的onClick，按快捷键alt+enter>create onclick event handler

svn命令安装配置
http://www.cnblogs.com/shenchanghui/p/5703927.html
导入忽略文件
http://www.cnblogs.com/jingmo0319/p/6112430.html


问题：
virtualBox打不开，模拟器使用不了，在oracle官方下载最新的virtualBox就解决了

有人说是网络问题
http://blog.csdn.net/beiminglei/article/details/17399333
```

#### 2.安卓ADB调试(windows平台)

```
有一部安卓系统手机或设备,usb数据线

1>android官方下载adb工具
		国内下载地址
		https://www.androiddevtools.cn/	//SDK Platform-Tools
2>安卓设备利用usb线连接设备,启动开发模式
		设置->系统->关于手机->连续点击`版本号`提示开启(启动开发模式).
		然后点击`返回`键进入`开发人员选项`开启(usb调试,`仅充电`模式下允许ADB调试)
	打开windows`设备管理`->发现一个未知设备`ADB INTERFACE`
3>安装adb驱动
		windows安装驱动精灵解决
		或者
		驱动下载
		https://adb.clockworkmod.com/
		
	4>启动adb服务并查看设备
		adb devices
	
	5>验证指令(测试验证发送返回键)
		adb shell input keyevent 4
	
	问题：
	1.adb devices(failed to start daemon)
		netstat -ano | findstr "5037"	//查看进程
		打开任务管理根据Pid直接kill
	2.adb devices出现unauthorized解决
		https://blog.csdn.net/qq_32157729/article/details/88743881
```

