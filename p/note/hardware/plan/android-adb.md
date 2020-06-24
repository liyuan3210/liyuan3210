# 安卓adb

一.windows调试

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

