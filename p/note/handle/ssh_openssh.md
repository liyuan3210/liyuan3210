# ssh免密登录及openssh

```
linux登录方式：
	安装ssh(确认是root用户)
	apt-get install openssh-server
	ps aux | grep sshd 查看是否启动成功

	1.密码登录
		命令行登录:
		ssh -l root 192.168.11.214 -p 22
		或
		ssh root@192.168.11.214
		
		利用ssh协议git仓库克隆(创建基础仓库见git笔记)
		git clone linux用户@192.168.5.21:/srv/r2.git r2								//默认端口22
		git clone ssh://liyuan@liyuan.imblog.in:40025/home/liyuan/git/src.git src	//指定端口
		
		SSH Secure工具登录:
		"algorithm negotiation failed"问题(配置):
		/etc/ssh/sshd_config最后一行添加如下：
		Ciphers aes128-cbc,aes192-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr,3des-cbc,arcfour128,arcfour256,arcfour,blowfish-cbc,cast128-cbc
		MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd160,hmac-sha1-96,hmac-md5-96
		KexAlgorithms diffie-hellman-group1-sha1,diffie-hellman-group14-sha1,diffie-hellman-group-exchange-sha1,diffie-hellman-group-exchange-sha256,ecdh-sha2-nistp256,ecdh-sha2-nistp384,ecdh-sha2-nistp521,diffie-hellman-group1-sha1,curve25519-sha256@libssh.org
		
		重启生效:
		service sshd restart
	
	2.免密登录(无密码免登录)
		A(客户端)免登录B(服务端)
		
		1>在A机器命令生id_rsa.pub公钥与id_rsa私钥
		ssh-keygen -t rsa -P ""
		或
		ssh-keygen
		
		一直回车
		
		2>B机器配置
		把生成的公钥copy至B机器做如下操作(.ssh目录的权限必须是700,authorized_keys文件权限必须是600)
		//没有.ssh文件夹需要自己新建
		cat /home/liyuan/.ssh/id_rsa.pub >> /home/liyuan/.ssh/authorized_keys
		
		B机器配置修改
		vi /etc/ssh/sshd_config
		AuthorizedKeysFile		%h/.ssh/authorized_keys //注释拿掉
		
		重启生效
		service sshd restart
		
		3>A机器配置(客户端访问者配置)
		在.ssh目录创建config 或者
		配置/etc/ssh/ssh_config文件
		Host centos
		 Port 27807
		 User liyuan
		 HostName 15256m136e.imwork.net
		 PreferredAuthentications publickey
		 IdentityFile C:\Users\liyuan\.ssh\id_rsa_nopwd_centos
		
		本地验证？
		ssh local
		
		然后在A上就可以免登录B了
		ssh liyuan@B机器IP地址
		
		windows多个秘钥对配置:
		http://blog.csdn.net/huluedeai/article/details/51417289
	3.git ssh配置
		在.ssh目录下新建config文件添加如下配置:
		# github key
		Host my_github
			Port 22
			User git
			HostName github.com
			PreferredAuthentications publickey
			IdentityFile C:\Users\liyuan\.ssh\id_rsa_git	//私钥文件
		# linux ssh
		Host my_ssh
			Port 22
			User git
			HostName 192.168.80.164
			PreferredAuthentications publickey
			IdentityFile C:\Users\liyuan\.ssh\id_rsa_ssh	//私钥文件
		
		客户端配置
		在.ssh目录创建config 或者
		配置/etc/ssh/ssh_config文件
		
		服务端配置
		/etc/ssh/sshd_config
			
		使用实例：
		git clone git@my_github:liyuan3210/test.git test
		ssh liyuan@my_ssh
	4.集群批量配置(可以相互访问)
		创建.ssh目录,配置好秘钥对 与 config。
			Host node1
			 Port 22
			 User liyuan
			 HostName node1
			 PreferredAuthentications publickey
			 IdentityFile /home/liyuan/.ssh/id_rsa_nopwd
			Host node2
			 ...
			Host node3
			 ...
	
问题
	1.Permissions 0664 for '.../id_rsa' are too open.
	授权:chmod 700 id_rsa
----------------------------------------------------------------------------
github SSH配置:
	一.密码登录
		1.创建公钥,私钥文件(如果已经创建了的就不用再次创建了)
		运行命令:ssh-keygen -t rsa -C "li.yuan3210@163.com"

		$ ssh-keygen -t rsa -C "li.yuan3210@163.com"
		Generating public/private rsa key pair.
		Enter file in which to save the key (/c/Users/liyuan/.ssh/id_rsa):	//按回车
		Enter passphrase (empty for no passphrase):
		Enter same passphrase again:	//输入密码
		Your identification has been saved in /c/Users/liyuan/.ssh/id_rsa.
		Your public key has been saved in /c/Users/liyuan/.ssh/id_rsa.pub.
		The key fingerprint is:		//再次输入密码
		SHA256:MgMuuvqKlBYMNWZozc6ta+ivaP53Zs+tNHMwnL/jO/k li.yuan3210@163.com
		The key's randomart image is:
		+---[RSA 2048]----+
		| .B              |
		|.= +             |
		|o o o            |
		|o  + o  . .      |
		| o. o + S=       |
		| .oo   +  +      |
		|.+. .    + o.    |
		|=+ o . +o =+.    |
		|@**o. + .+o==E   |
		+----[SHA256]-----+

		执行成功后C:\Users\liyuan\.ssh目录会生成两个文件
		公钥:id_rsa.pub
		私钥:id_rsa

		如果已经创建了的就不用再次创建了,把id_rsa.pub,id_rsa
		两个文件copy到本机C:\Users\liyuan\.ssh目录就行了

		3.github中添加公钥
		登录github
		>
		settings
		>
		SSH and GPG keys
		>
		new ssh key(新建一个ssh keys)
		title:输入一个名字
		key:把文件id_rsa.pub内容复制进去保存

		4.授权确认(用来测试配置是否成功)
		$ ssh -T git@github.com
		Enter passphrase for key '/c/Users/liyuan/.ssh/id_rsa': //输入创建秘钥时的密码
		Hi liyuan3210! You've successfully authenticated, but GitHub does not provide shell access.

		5.最后授权成功就可以clone项目了
		克隆项目时需要配置好user.name,user.email,密码输入创建公钥与私钥时的密码
		
	二.无密码克隆,提交
		1.根据上面步骤生成无密码登录秘钥对(2.公钥登录(无密码免登录))
		2.github中添加公钥(就可以了)
		每一个github,gitee系统里面只能用一次"无密码登录秘钥对"否则会报key已存在
		
gitee码云
	单个私有仓库公钥添加:只有读取权限
	需要推送权限的需要在安全设置>SSH公钥>里面添加公钥
	
```

