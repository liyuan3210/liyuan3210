# frp,ngrok内网穿透

## 一．frp

```
官网：https://gofrp.org
官网源码:https://github.com/fatedier/frp

	实例:http://iytc.net/wordpress/?p=3299
	https://blog.csdn.net/u010071211/article/details/81182886
	
	frp编译安装
	https://blog.waringid.me/frpbian-yi-zhi-nan/
	https://blog.csdn.net/whereisdog/article/details/83409401
	https://www.612459.com/dnxx/2398.html
```

1.新版frp配置

网络参考：https://blog.csdn.net/loool_/article/details/134146141

```
1.1。frps.toml服务端配置
bindPort = 7000
# http端口
vhostHTTPPort = 8080
vhostHTTPSPort = 1443
# 日志
log.to = "frps.log"
# frps服务端仪表板
webServer.addr = "0.0.0.0"
webServer.port = 7500
webServer.user = "admin"
webServer.password = "xxx"
# 授权
auth.method = "token"
auth.token = "xxx"

服务端仪表盘：
		http://47.116.171.118:7500

1.2。frpc.toml客户端配置
serverAddr = "47.116.171.118"
serverPort = 7000
# 授权
auth.method = "token"
auth.token = "xxx"
# frpc客户端控制台，可以在线刷新配置文件
webServer.addr = "0.0.0.0"
webServer.port = 7400
webServer.user = "admin"
webServer.password = "xxx"
webServer.pprofEnable = false
# 日志配置
log.to = "frpc.log"
# 配置端口映射
[[proxies]]
name = "3389"
type = "tcp"
localIP = "127.0.0.1"
localPort = 3389
remotePort = 3388
# 配置外网访问frpc控制端
[[proxies]]
name = "7400"
type = "tcp"
localIP = "127.0.0.1"
localPort = 7400
remotePort = 7400

客户端控制台：
	http://127.0.0.1:7400
```

2.老版本

```
2.1.服务端：
		[common]
		bind_port = 7000
		vhost_http_port = 8888
		log_file = frps.log
		# 鉴权
		authentication_method=token
		token=123456
	启动服务端
		./frps -c frps.ini &
		
	http://ip:8888


2.2.客户端：
[common]
		server_addr = 94.191.73.142
		server_port = 7000
		# 鉴权
		authentication_method=token
		token=123456
		
		//webTest同一个服务端8888,不能重名
		//远程访问使用nginx.frp.liyuan3210.com:8888
		[web]
		type = http
		local_ip = 127.0.0.1
		local_port = 81
		custom_domains = nginx.frp.liyuan3210.com
		
		//远程连接使用 远程ip:3388
		[3388]
		type = tcp
		local_ip = 127.0.0.1
		local_port = 3389
		remote_port = 3388
	 启动客户端
		frpc.exe -c frpc.ini
		
	测试穿透
	http://frp.liyuan3210.com:8888
```

## 二．ngrok

#### 邮箱ngrok.com

```
官网：https://ngrok.com
邮箱登录(ngrok帐号密码)：
li.yuan3210@163.com
465049568@qq.com
liyuan123456_cool@163.com
li.yuan198907@gmail.com
```

使用：

```
启动映射(8080是web服务)
ngrok http 8080

控制台入口
http://127.0.0.1:4040

1.Installing your Authtoken(ngrok.yml重命名http可以开多个)
ngrok.exe authtoken 5ZKAemtTQJYHzXbV1eDkt_45vpRJEq3P4T7Ly3i1dns

2.Listening on
ngrok.exe tcp 3316

linux保持启动：
	http://blog.csdn.net/xs910115/article/details/50097717

https://blog.csdn.net/zhangguo5/article/details/77848658?utm_source=5ibc.net&utm_medium=referral

国内ngrok:
	http://blog.csdn.net/linshichen/article/details/52670385
```

#### cpolar(国内类似ngrok的工具)

```
官网：
https://www.cpolar.com/

```

#### 搭建私有ngrok（长时间使用容易内存溢出，不推荐服务器使用）

```
官网源码：https://github.com/inconshreveable/ngrok
	https://blog.csdn.net/yjc_1111/article/details/79353718
	https://blog.csdn.net/zhangguo5/article/details/77848658?utm_source=5ibc.net&utm_medium=referral
	
ngrok服务端搭建:
	https://blog.csdn.net/xiahailong90/article/details/79107453
```

1.服务端

```
一.编译,运行依赖的插件
		apt-get install build-essential mercurial 	//编译时需要的
		或
		apt-get install binutils bison //运行时需要的(不安装binutils,bison会运行不了),要用root运行

		卸载(可能会用到)
		apt-get --purge remove golang

	二. 安装go环境
		go下载地址
		https://golang.org/dl/
		https://golang.google.cn/dl/

		#go环境变量配置
		vi /etc/profile
		export GOROOT=/home/liyuan/soft/go
		export PATH=$GOROOT/bin:$PATH
		
		//立即生效
		source /etc/profile

	证书生成
		openssl genrsa -out rootCA.key 2048
		openssl req -x509 -new -nodes -key rootCA.key -subj "/CN=ngrok.liyuan3210.com" -days 5000 -out rootCA.pem
		openssl genrsa -out device.key 2048
		openssl req -new -key device.key -subj "/CN=ngrok.liyuan3210.com" -out device.csr
		openssl x509 -req -in device.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out device.crt -days 5000

		#替换默认的证书文件
		cp rootCA.pem assets/client/tls/ngrokroot.crt
		cp device.crt assets/server/tls/snakeoil.crt 
		cp device.key assets/server/tls/snakeoil.key

	多环境编译
		https://blog.csdn.net/tonyfreak/article/details/80016577?utm_source=blogxgwz0
		
		linux环境
		GOOS=linux GOARCH=amd64 make release-server release-client
		
		windows环境
		GOOS=windows GOARCH=amd64 make release-server release-client
		
		mac环境
		GOOS=darwin GOARCH=amd64 make release-server release-client
		
	服务端测试
		生产运行时需要安装如下
		apt-get install binutils bison
		
		服务端运行命令
		./ngrokd -domain="ngrok.liyuan3210.com" -httpAddr=":8080"
		nohup ./ngrokd -domain="ngrok.liyuan3210.com" -httpAddr=":8080" -log="ngrok.log" &
```

2.客户端

```
客户端使用
		新建配置文件ngrok.cfg
			server_addr: "ngrok.liyuan3210.com:4443"
			trust_host_root_certs: false
		1.http穿透
			ngrok -config=ngrok.cfg -subdomain=test 84
		2.tcp穿透
			ngrok -config=ngrok.cfg -proto=tcp 3389
		
	ngrokd后端运行脚本
		https://www.jianshu.com/p/98697b0743eb
		
	测试地址
	ssh -l liyuan ngrok.liyuan3210.com -p 33052
```

安装中的问题：

```
环境问题
		https://blog.csdn.net/u011596455/article/details/60322568

		ps -A | grep apt
		kill -9 processnumber
		rm /var/lib/dpkg/lock
		rm /var/lib/apt/lists/lock
		rm /var/cache/apt/archives/lock
		最后更新
		apt update
			根据提示可能需要执行如下
			dpkg --configure -a

编译问题
		最下面
		https://segmentfault.com/a/1190000010338848?utm_source=tag-newest
		https://github.com/rakyll/hey/issues/9
		1.问题1
		net/http/httptrace import path does not begin with hostname
		本机go版本太低，需要1.7.x以上的,安装go1.11.2.linux-amd64.tar.gz
		配置环境变量

		2.问题2
		code.google.com/p/log4go: Get https://code.google.com/p/log4go/source/checkout?repo=:
		dial tcp 216.58.197.110:443: i/o timeout
		因为google被墙，如果服务器不在墙外或者没有FQ则无法访问到code.google.com.
		解决方法：在 ngrok/src/ngrok/log 目录下找到 logger.go 文件，修改其中第4或5行的：
		log "code.google.com/p/log4go"
		为
		log "github.com/keepeye/log4go"
		
		3.问题3
		ngrok https://gopkg.in/yaml.v1?go-get=1: timeout
		解决方法:
		执行install build-essential mercurial
```

## 三．其他

1.概要

```
穿透原理
	1.p2p原理,java,c++实现p2p
	2.n2n(是一个款开源软件)
	3.frp(是一个go语言软件)
	4.tunnel协议
	5.arp协议
内网映射技术
	1.STUN,打洞方式
	2.TRUN,STUN结合TRUN server中继模式
	3.ICE框架,整合了TRUN,STUN
	4.UPnP技术
	5.ALG等其他技术
```

2.笔记

```
----------------------------------------------------------------------------
r2ray
https://zshttp.com/1310.html
----------------------------------------------------------------------------
		内网映射概述
		http://blog.csdn.net/wang805447391/article/details/54633104

		网络技术
		http://www.h3c.com.cn/MiniSite/Technology_Circle/Net_Reptile/

		p2p点对点通信
		http://www.cnblogs.com/bingguang/p/4552764.html
		http://www.cnblogs.com/pannengzhi/p/4800526.html
		http://www.cnblogs.com/pannengzhi
		
		ARP与MITM原理
		http://www.cnblogs.com/pannengzhi/p/4838971.html
		
		
		目前常用网络爬虫框架:http://www.cnblogs.com/76er/archive/2012/10/24/2737966.html
		代理lanter(国外代理):https://github.com/getlantern/lantern/releases/tag/latest
		流行的内网穿透技术:http://blog.csdn.net/wang805447391/article/details/54633104
```

