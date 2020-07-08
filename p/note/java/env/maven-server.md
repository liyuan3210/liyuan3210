# maven私服搭建

```
实例
	https://blog.csdn.net/rickyit/article/details/54927101

	官网下载
	https://www.sonatype.com/download-oss-sonatype

	配置环境变量
	NEXUS_HOME:C:\soft\nexus-3.9.0-01-win64\nexus-3.9.0-01
	Path:;%NEXUS_HOME%\bin

	setx NEXUS_HOME "C:\soft\nexus-3.9.0-01-win64\nexus-3.9.0-01"
	setx PATH "%PATH%;%NEXUS_HOME%\bin"

	直接运行服务：
	nexus.exe /run

	安装服务
	nexus.exe /install Nexus

	浏览器访问
	http://127.0.0.1:8081/

	默认账号/密码
	admin/admin123

linux下安装
	https://www.jianshu.com/p/2dc35820c523
	
	配置java,nexus环境变量
	
	启动命令
	nexus start
	
	编辑添加如下内容(vi /lib/systemd/system/nexus.service):
	// 添加如下内容
	[Unit]
	Description=Nexus Service
	After=network.target

	[Service]
	Type=forking
	ExecStart=/opt/nexus/nexus-2.14.2-01/bin/nexus start
	ExecReload=/opt/nexus/nexus-2.14.2-01/bin/nexus restart
	ExecStop=/opt/nexus/nexus-2.14.2-01/bin/nexus  stop
	PrivateTmp=true

	[Install]
	WantedBy=multi-user.target
	
	配置nexus.service文件权限
	# chomd 754 nexus.service
	
	设置开机自启动
	# systemctl enable nginx.service
	
	如果是使用root用户启动nexus，请添加系统环境变量：
	RUN_AS_USER=root
```

