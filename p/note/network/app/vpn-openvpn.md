# openvpn

## 一．openvpn服务端搭建

下载windows客户端安装包并安装官网为（被墙了）:

www.openvpn.net

```
安装前准备
	//安装openssl和lzo，lzo用于压缩通讯数据加快传输速度(可选)
	sudo apt-get install openssl libssl-dev
	sudo apt-get install lzop
	1.安装openVPN
	sudo apt-get update	//新安装的系统需要更新一下
	sudo apt-get install openvpn easy-rsa
	
	****复制 easy-rsa 模板到 home 目录(当前目录生成openvpn-ca目录)：
	开始之前，我们可以使用make-cadir命令，用于复制easy-rsa临时目录到我们的home目录下面：
	$ make-cadir ~/openvpn-ca
	上面这条命令也用下面的两条命令来做：
	$ mkdir ~/openvpn-ca
	$ cp -r /usr/share/easy-rsa/*  ~/openvpn-ca
	
	2.配置CA变量
	vi vars
	export KEY_COUNTRY="US"
	export KEY_PROVINCE="CA"
	export KEY_CITY="shanghai"
	export KEY_ORG="ly"
	export KEY_EMAIL="li.yuan3210@163.com"
	export KEY_OU="myOrgUtil"
	//然后，还需要将 KEY_NAME 改为你喜欢的，这里简单起见，我们改成 server：
	export KEY_NAME="server"
	
	使变量生效//貌似不用执行
	source vars 
	
	3.构建Certificate Authority
	在刚才的目录中，执行 source vars ，然后，你将会看到如下输出：
	NOTE: If you run ./clean-all, I will be doing a rm -rf on /home/xlzd/openvpn-ca/keys
	然后执行：
	//	./clean-all //清除所有
	./build-ca
	到此，我们就有了创建以下步骤需要的 CA 证书。
	
	4.创建服务器端证书、密钥和加密文件
	./build-key-server server 	//注意后面有两个选择输入y,否则一直回车会出现server.crt空文件
	命令，然后继续一路回车就好了。
	到最后，你需要输入两次 y 注册证书和提交：
	
	然后还需要生成一些其他东西，在终端执行 
	./build-dh，这个操作大约会花费几分钟不等。
	
	然后，我们可以生成 HMAC 签名加强服务器的 TLS 完整性验证功能：
	openvpn --genkey --secret keys/ta.key
	
	5.生成客户端证书、密钥对
	这一步之后可能会执行多次以生成不同的证书，这里我们以 xclient 作为第一组密钥对的名字：
	//cd ~/openvpn-ca
	//source vars
	./build-key client1		  //注意后面有两个选择输入y,否则一直回车会出现client1.crt空文件
	
	6.配置 OpenVPN 服务
	复制文件到/etc/openvpn目录下
	cd ~/openvpn-ca/keys
	sudo cp ca.crt ca.key server.crt server.key ta.key dh2048.pem /etc/openvpn
	
	创建OpenVPN配置文件(解压文件server.conf.gz到/etc/openvpn/[server.conf]下面)：
	sudo gunzip -c /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz | sudo tee /etc/openvpn/server.conf
	
	文件权限(注意)
	cd /etc/openvpn/
	chmod 755 *
	
	7.server.conf配置文件：
		sudo vi /etc/openvpn/server.conf
		去掉如下几行的注释：
		push "redirect-gateway def1 bypass-dhcp"
		push "dhcp-option DNS 208.67.222.222"		//8.8.8.8
		push "dhcp-option DNS 208.67.220.220"		//8.8.4.4
		user nobody
		group nogroup
		tls-auth ta.key 0 # This file is secret
		#在tls-auth ta.key下面添加下面一行
		key-direction 0
		
		# (虚拟网段)默认虚拟局域网网段，不要和实际的局域网冲突即可,路由模式，桥接模式用server-bridge
		server 172.16.0.0 255.255.0.0
		# 10.0.80.0是我这台VPN服务器所在的内网的网段，应该根据自身实际情况进行修改
		push "route 10.0.80.0 255.255.255.0"
		
				<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<非必配项<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
				# 服务监听的ip和端口
				local 10.0.80.156
				port 1194
				# 改成tcp,默认使用udp，如果使用HTTP Proxy，必须使用tcp协议
				proto udp
				# 维护一个记录客户端连接的虚拟IP的记录，如果openvpn出现故障或重启，重新连接的客户端可使用先前分配过的IP
				ifconfig-pool-persist ipp.txt

				# 可以让客户端之间相互访问直接通过openvpn程序转发，根据需要设置
				client-to-client
				
				# 如果客户端都使用相同的证书和密钥连接VPN，一定要打开这个选项，否则每个证书只允许一个人连接VPN
				duplicate-cn
				keepalive 10 120
				tls-auth keys/ta.key 0 # This file is secret
				comp-lzo
				persist-key
				persist-tun
				# OpenVPN的状态日志，默认为/etc/openvpn/openvpn-status.log
				status openvpn-status.log
				# OpenVPN的运行日志，默认为/etc/openvpn/openvpn.log 
				log-append openvpn.log
				# 改成verb 5可以多查看一些调试信息
				verb 5
				# 使用压缩,依赖安装步骤apt-get install lzop
				comp-lzo
				<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<<
	
	8.IP转发
	sudo vi /etc/sysctl.conf
	去掉下面一行的注释：
	net.ipv4.ip_forward=1
	使生效：
	sudo sysctl -p
	
	9.防火墙配置(服务器重启后要重新执行如下语句)：
	iptables -t nat -A POSTROUTING -s 192.188.3.0/15 -o eth0 -j MASQUERADE		//注意eth0表示局域网使用的网卡名称
	iptables -A FORWARD -s 192.188.3.0/15 -p tcp -m tcp --tcp-flags SYN,RST SYN -j TCPMSS --set-mss 1200
	注意:
	貌似上面执行后pptpd(192.188.2.1/15网段的)就不用执行了
	
	ufw确认(非必项目)
	sudo vi /etc/default/ufw
	DEFAULT_FORWARD_POLICY="ACCEPT"
	
	10.启动OpenVPN服务
	首次安装需要执行//sudo systemctl start openvpn@server
	首次安装需要执行//sudo systemctl enable openvpn@server
	service openvpn start	//开始服务，读取配置文件
	service openvpn restart	//重启,重新读取配置文件
	
	11.生成OpenVPN客户端使用的ovpn
	mkdir client
	复制模版配置文件：
	cp /usr/share/doc/openvpn/examples/sample-config-files/client.conf client
	
	编辑：
	vim client.conf
	找到remote一段：
	remote server_IP_or_domain 1194
	把server_IP_or_domain替换为OpenVPN服务IP或域名。

	去掉如下两行的注释：
	user nobody
	group nogroup

	更改ca, cert 和 key的值，修改为[inline]；inline代表本文件。
	ca [inline]
	cert [inline]
	key [inline]
	找到tls-auth一段，去掉注释并在其下添加一行：
	tls-auth [inline] 1
	key-direction 1
	
	拷贝至客户端
	/etc/openvpn/easy-rsa/keys/ca.crt
　　/etc/openvpn/easy-rsa/keys/client.crt
　　/etc/openvpn/easy-rsa/keys/client.key
　　/etc/openvpn/ta.key

	12.生成ovpn文件：
	cat ~/client/client.conf <(echo -e '<ca>') ~/openvpn-ca/keys/ca.crt <(echo -e '</ca>\n<cert>') ~/openvpn-ca/keys/client1.crt <(echo -e '</cert>\n<key>') ~/openvpn-ca/keys/client1.key <(echo -e '</key>\n<tls-auth>') ~/openvpn-ca/keys/ta.key <(echo -e '</tls-auth>') > ~/client/ovpn/client1.ovpn
	上面命令其实就是把#4步生成的客户端证书/key和服务器证书/key组合在一起。
	把~/client/ovpn/client1.ovpn文件传给客户端；OpenVPN客户端就可以使用这个文件连接了，怎么连接就不说了。
	
	cat client/client.conf <(echo -e '<ca>') openvpn-ca/keys/ca.crt <(echo -e '</ca>\n<cert>') openvpn-ca/keys/client1.crt <(echo -e '</cert>\n<key>') openvpn-ca/keys/client1.key <(echo -e '</key>\n<tls-auth>') openvpn-ca/keys/ta.key <(echo -e '</tls-auth>') > client/client1.ovpn
	
windows 客户端连接
	http://www.cnblogs.com/EasonJim/p/8341404.html
	客户端下载
	https://openvpn-2-0-1-rc1.updatestar.com/
	https://www.updatestar.com/

linux 客户端连接
	https://www.cnblogs.com/weifeng1463/p/10157201.html
	https://blog.csdn.net/weixin_34232363/article/details/86257164
```

## 二．问题

```
centos7 搭建openvpn服务器
https://www.cnblogs.com/fuyanwen/p/8195956.html
	
openvpn
https://www.jianshu.com/p/95267af6bbb2
	
Ubuntu 16.04安装OpenVPN客户端GUI
https://www.cnblogs.com/EasonJim/p/8350985.html
	
Ubuntu 16.04搭建OpenVPN服务器以及客户端的使用
https://www.cnblogs.com/EasonJim/p/8339600.html
	
代理上网问题
http://www.365mini.com/page/18.htm
	
Ubuntu 16.04 架设 OpenVPN 服务器
http://blog.topspeedsnail.com/archives/4860
https://www.jianshu.com/p/f198f9a768c0

服务端配置
https://www.cnblogs.com/EasonJim/p/8339600.html
http://www.linuxfly.org/post/84/

阿里云配置
https://www.aliyun.com/jiaocheng/135145.html

配置文件详解
https://www.jianshu.com/p/74817d3a342a

openvpn客户端安装
https://wenku.baidu.com/view/788bb4dd195f312b3169a5a7.html
https://www.jb51.net/os/windows/346447.html

服务端 与 客户端都要修改
proto tcp
```

