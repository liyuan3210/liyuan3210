# GitHub,gitee,gitlab,git常用命令及配置

[一.git开发配置,ssh免密登录,及openssh](#gituse1)<br/>
[二.git命令使用](#gituse2)<br/>
[三.githubOAuth实现用户登录](#gituse3)<br/>
[四.github站点托管,CNAME配置,A记录,泛域名解析](#gituse4)<br/>
[五.gitee马云API调用](#gituse5)<br/>
[六.gitlab安装](#gituse6)<br/>
[七.gitlab开发模式](#gituse7)

### 一.git开发配置,ssh免密登录,及openssh
<div id="gituse1"/>
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
	授权:chmod 600 id_rsa
-------------------------------------------------------------------------
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
### 二.git命令使用
<div id="gituse2"/>
```
分布式版本管理系统git
官网：https://github.com
GUI工具(github公司推出的):https://desktop.github.com
客户端工具:https://gitforwindows.org(https://git-for-windows.github.io)
		https://git-scm.com
一.组件
	git-arch
	git-cvs
	git-svn
	git-gui
	gitk
	gitweb：图形界面浏览器浏览版本库
	git-email：通过电子邮件发送git补丁
	git-daemon-run：共享你的版本库

	ubuntu下安装方式：
	sudo apt-get update 更新apt-get
	sudo apt-get install git
	sudo apt-get git-daemon-run

二.配置：
	1>环境变量
	1..git/config:版本库特定的配置,可用--file选项修改
	2.~/.gitconfig:用户特定设置，可用--global选项修改
	3./etc/gitconfig:系统范围权限设置

	2>git commit期间,让git打开最爱的编辑器
	bash
	export GIT_EDITOR=vim

	3>配置提交作者
	全局版本库
	git config --global user.name "liyuan"
	git config --global user.email "li.yuan3210@163.com"
	单个版本库
	git config user.name "liyuan"
	git config user.email "li.yuan3210@163.com"

	4>.gitignore文件
	允许版本库任何目录有这个文件，每个文件只影响当前目录
	echo file > .gitignore(忽略一个文件)
	需要忽略一个列表的有一套git机制


三.基本命令使用
	1.创建初始版本库（有克隆,空版本库两种方法）
	git init(创建空版本库)
	git status(显示状态)
	git ls-files --stage(查看暂存的文件)

	2.将新文件添加到版本库中
	git add test.txt
	git commit -m "desc"(desc注释必须写)

	3.修改文件后提交
	git add test.txt
	git commit -m "desc"

	4.删除文件(是根据版本库来删除的,同时删除本地文件)
	git rm test.txt		//删除指定文件
	git rm -r *			//递归目录删除当前目录所有文件
	git commit -m "desc"

	4.查看分支提交日志
	git log [master][files]
	git reflog [master][files]

	树形结构显示分支
	git show-branch


四.分支
	默认为master分支
	git branch(查看分支)
	git branch -a(查看远程分支)
	git show-branch master(查看详细)
	1.创建分支
	git branch dev 

	2.检出分支
	git checkout dev

	3.删除分支(不能删除当前分支)
	git branch -d dev				//删除本地分支
	git push origin -d dev	//删除远程分支

	4.提交分支到远程库
	git push origin dev

	5.tag(releases)
	打标签有两种方式
	git tag v1.0					//轻量级的(一个commit的引用)
	git tag -a v1.0 -m "描述"		//含附注的(包含创建时间,备注,指向对应commit)

	显示标签:git tag
		标签作用范围:git commit -m "提交后的"

	提交标签到远程仓库:
	git push origin v1.0
	git push origin -tags

	删除标签:
	删除本地:git tag -d v1.0
	删除远程:git push origin :refs/tags/v2.0 

	查看此版本修改内容
	git show v1.0

	检出tag
	git checkout v1.0
	 
	轻量级,含附注标签的区别:
	?

五.diff
	工作目录:当前工作目录可以看到的文件
	索引:git add file后的
	HEAD版本:每一次git commit后的

	工作目录与索引
	git diff

	工作目录与HEAD版本
	git diff HEAD

	索引与HEAD版本
	git diff --cached
	
	比较工具
	git difftool

六.合并
	合并到master分支下面,更新索引.但没提交到远程库
	git checkout master
	git merge other_branch

	相对提交名：
	?

	git reset命令:
	git reset --soft	//影响 HEAD
	git reset --mixed	//影响 HEAD 索引
	git reset --hard	//影响 HEAD 索引 工作目录
	如果要还原到最原始需要如下操作
	git add *
	git reset --hard

	还原到某个提交节点
	git reset --hard commit_id
	
	没有push:
		1.还原整个仓库(不管在仓库哪个子路径下,还原的是整个仓库)
		--hard(添加的文件,修改的内容都会消失)
			1>.已经add,commit
			git reset --hard head^
			2>.只add(使用git log查看hash)
			git reset --hard <第一个hash>
			OR或者
			git reset --hard hard
		--mixed(恢复到没有add的状态,前面添加,修改的文件不会消失)
			1>.已经add,commit
				git reset --mixed head^
		--soft
			1>.恢复到只add的状态
				git reset --soft head^
		
		2.无法更新(pull),由于远程仓库已有用户提交,本地文件也有修改.(单个文件还原)
			导致无法更新,解决办法如下:
				1>.查看并复制hash(第一个hash值)
				git log src/file
				2>.还原checkout单个文件
				git checkout <hash> <file>
				3>.解决后就可以git pull更新了
	已经push:
		1.revert(用git log查看hash,保证仓库是干净的)
			git revert 9f959288f9a4afe3c395785ef71e53d9abdcc95e
				或者(or)
			git revert head
		
		2.最后执行提交还原
			git push
		
		revert是用一次新的commit提交回退之前的
		https://www.cnblogs.com/zichuan/p/10314065.html
				
七.服务端配置
	git fetch:从远程版本库抓取对象
	git ls-remote:显示给定的远程版本库引用列表(在上游发引用列表)
	git pull:跟fetch类似，但合并修改到相应的本地分支
	git push:转移对象及相关的元数据到远程版本库

	安装git
	apt-get install git //ubuntu一般都不需要安装
	

	创建裸库(ubuntu server默认指定创建到/srv目录下)
	cd /srv
	mkdir r2.git
	cd r2.git
	git init --shared --bare
	或者
	cd /srv
	git init --bare r2.git
	
	
	创建用户(其实就是新建linux账户)
	sudo adduser git用户
	...设置密码

	授权
	sudo chown -R linux账户:linux账户 r2.git

	克隆远程版本库(克隆r2开发库)其实就是ssh协议,
	后面的目录就是用户的linux目录地址
	git clone linux用户@192.168.5.21:/srv/r2.git r2								//默认端口22
	git clone ssh://liyuan@liyuan.imblog.in:40025/home/liyuan/git/src.git src	//指定端口

	注意(按照上面创建库后需要按照下面方式来初始化)：
	1>克隆后配置name,email
	2>新建一个文件(readme.txt),git add *,git commit -m "init"
	3>git push
	git clone克隆后默认的远程库为origin

	创建一个分支并提交到远程库
	git branch dev //创建分支
	git checkout dev //切换到分支
	git push origin dev //提交分支到远程库


八.本地git init后提交到远程库
	1.初始化库
	git init

	2.配置name,email还有配置远程库地址(克隆默认为origin,下面设置为org2)
	git remote add org2 git@192.168.159.130:/srv/r2.git

	3.根据设置的org2地址更新远程库索引
	git pull org2

	4.查看远程分支
	git branch -a

	5.检出远程分支到本地并在本地库新建分支
	git checkout -b dev origin/dev	//如果-b参数 重命名,则提交后会在远程新建一个分支,如果相同则在原来分支上进行提交修改

	git checkout origin/dev 	//只检出远程分支,当在这个分支做修改后切换分支,所做的修改会还原


	6.删除远程分支
	git push origin --delete dev

	创建一个分支并提交到远程库
	git branch dev //创建分支
	git checkout dev //切换到分支
	git push origin dev //提交分支到远程库

	查看远程配置：
	开发库:cat .git/config
	
九.其它
	1.操作git的API第三java方库
		官网
		http://www.eclipse.org/jgit
		下载
		http://www.eclipse.org/jgit/download/
		实例
		http://www.importnew.com/19970.html
		https://www.cnblogs.com/songshu120/p/6180747.html
	
问题
		git init提交分支问题:
		http://www.cnblogs.com/jeremylee/p/5715289.html

		git服务端搭建
		http://www.cnblogs.com/linuxprobe/p/5720223.html

		github怎么搭建博客
		http://blog.csdn.net/gdutxiaoxu/article/details/53576018
		http://blog.csdn.net/renfufei/article/details/37725057/
		http://www.cnblogs.com/liuxianan/p/build-blog-website-by-hexo-github.html
```
### 三.githubOAuth实现用户登录
<div id="gituse3"/>
```
https://blog.csdn.net/kobe24lmlps/article/details/80838329
	https://juejin.im/post/59c0aef06fb9a00a6436b89a
	https://newsn.net/say/github-oauth-flow.html
	http://www.ruanyifeng.com/blog/2019/04/github-oauth.html
	https://www.jianshu.com/p/a9c0b277a3b3
		1.请求
		client_id就是从github上面申请到的那个appid。
		scope就是说你的应用会请求用户的那些数据，这个是会在登陆页面上展示的。
		state是个随机数，用于数据验证，所以，这个值越随机越好。
		https://github.com/login/oauth/authorize?client_id=7f32efa8a2b55c6b3eb7&scope=user&state=213412341234121112
		如果浏览器没登录会跳转到github登录页面，如果已登录会跳转到配置的页面Authorization callback URL
		或者
		https://github.com/login/oauth/authorize?client_id=7f32efa8a2b55c6b3eb7

		2.返回配置好的callback跳转,state与code
		http://127.0.0.1:8080/ebk/servlet/servletLogin?code=32578d4fc116be3dad83&state=213412341234121112
	
	
		返回token
		https://github.com/login/oauth/access_token?client_id=7f32efa8a2b55c6b3eb7&client_secret=9ad68695f5938494e0909bdcca5aaf68d5b89900&code=16ee31a9af4620ea29bb&redirect_uri=http://127.0.0.1:8080/
		或者
		https://github.com/login/oauth/access_token?client_id=7f32efa8a2b55c6b3eb7&client_secret=9ad68695f5938494e0909bdcca5aaf68d5b89900&code=16ee31a9af4620ea29bb
		返回的token数据
		access_token=271728c71dd0a250a4ac8a1c6f04eb0002df6f0a&scope=&token_type=bearer
		access_token=2036f8094abacbb3512234375b52b407d3f81a63&scope=gist%2Cuser&token_type=bearer
		
		3.使用token调用API请求数据
		https://api.github.com/user
		curl -H "Authorization: token f039c797ebb3cb0f1eea691cef90a4857b059b12" https://api.github.com/user
		
	Github API使用
	https://segmentfault.com/a/1190000015144126?utm_source=tag-newest
	
	windows(凭据管理)
	https://www.jianshu.com/p/0ad3d88c51f4
	
	md转html
https://blog.csdn.net/liujiaojiao666/article/details/86154677
```
### 四.github站点托管,CNAME配置,A记录,泛域名解析
<div id="gituse4"/>
```
	1.CNAME，A记录配置
        1.1>githup创建仓库:
            new repository>仓库名:liyuan3210
            settings>github pages(选择模板创建页面，然后就可以看到域名liyuan3210.github.io)

		1.2>绑定域名:
			githup仓库地址：liyuan3210.github.io
			注册一个域名www.liyuan3210.com，配置域名解析
			先在本地ping一下liyuan3210.github.io指向的ip(151.101.72.133)
				1.配置A记录(记录类型) : 指向ip 151.101.72.133(记录值):@(主机记录)
				2.配置CNAME(记录类型) : 指向域名liyuan3210.github.io(记录值):www(主机记录)

		1.3>CNAME配置:
			在新建好的仓库liyuan3210跟目录下新建CNAME文件,内容写上要绑定的域名www.liyuan3210.com，最后就可以通过域名访问托管在github上的静态网站了

		NOTE
	花生壳配置CNAME,A记录时，需要先关闭花生壳域名服务(因为关闭后会清空CNAME,A记录)
	
	
	2.泛域名配置
		https://www.bilibili.com/video/av21336992
		1.域名添加泛域名,无限个域名支持服务
		2.配置域名解析
			ngrok		A记录		94.191.73.142
			*.ngrok	    A记录		94.191.73.142
			
	3.TXT记录
		一般配置网站https才需要配置
    
    同一个域名配置多个A记录指向不同ip，可以实现一定程度负载均衡
    
	A，TXT记录
	http://www.ezloo.com/2011/04/a_mx_cname_txt_aaaa_ns.html
		https://www.22.cn/help_34.html
		
	网站域名带www和不带的区别
https://zhidao.baidu.com/question/332729665886275525.html
		
	4.网站流量统计
	https://www.cnblogs.com/hejun26/p/9802549.html
```

A记录,TXT记录，CANME配置如下:

![](img\www-a-canme-txt.png)
### 五.gitee马云API调用
<div id="gituse5"/>
```
github token ： 3966607594ddbf3fd007f12e02fa0290cae2a9c9
	
	条件:
	设置->私人令牌access tokens(申请一个),还可以OAuth三方应用授权登录
	通过令牌调用api,操作代码片段,issus等等
	
	1.添加代码片段
	curl -X POST --header 'Content-Type: application/json;charset=UTF-8' 'https://gitee.com/api/v5/gists?access_token=83832ab64bc489240c65f2fb94021202' -d '{"files":{"API-call-file.txt":{"content":"Api call content liyuan3210"}},"description":"API-TEST-3210","public":"true"}'

	2.添加issus
	curl -X POST --header 'Content-Type: application/json;charset=UTF-8' 'https://gitee.com/api/v5/repos/liyuan3210/issues' -d '{"access_token":"83832ab64bc489240c65f2fb94021202","repo":"pages","title":"API-Call-test","body":"test content haha","labels":"question"}'
```
### 六.gitlab安装
<div id="gituse6"/>
```

一.gitlab(私有类github)
	https://about.gitlab.com/

	ubuntu gitlab安装
	https://www.cnblogs.com/xishuai/p/ubuntu-install-gitlab.html

	摘抄中文官网
	https://www.cnblogs.com/gabin/p/6385908.html

	gitlab ubuntu 安装
	https://about.gitlab.com/installation/#ubuntu?version=ce
	
	gitlab版本类型
	企业版(收费版本)EE
	社区办(免费版)CE

	安装步骤：
	1.首先下载gitlab-ce_10.6.4-ce.0_amd64.deb包
		官方下载
		https://packages.gitlab.com/gitlab/gitlab-ce
	
		版本页面
		https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/xenial/gitlab-ce_10.6.4-ce.0_amd64.deb

		实际地址下载
		https://packages.gitlab.com/gitlab/gitlab-ce/packages/ubuntu/xenial/gitlab-ce_10.6.4-ce.0_amd64.deb/download.deb
		
	2.根据官方文档进行安装
		sudo apt-get update	//更新索引
		sudo apt-get install -y curl openssh-server ca-certificates	//配置安装依赖
	
		sudo apt-get install -y postfix	//配置邮件发送
		
	3.安装
		sudo dpkg -i gitlab-ce_10.6.4-ce.0_amd64.deb
		
	4.安装好后启动服务
		gitlab-ctl reconfigure
		访问:http://IP:port
		
	5.登录设置密码(默认设置root用户密码)
		root/adm123456
		
	6.修改配置
		vi /etc/gitlab/gitlab.rb
		
		external_url 'http://192.168.5.26' //或者ip:port
		
		sudo gitlab-ctl reconfigure  //执行后生效
		
		配置后web访问,git clone访问都会改变
		
	7.git clone
		貌似现在只能使用http协议下载
		
	8.ssh配置
	
	gitlab部署、配置更改、备份及恢复
	http://blog.51cto.com/yangrong/1659880
	https://www.cnblogs.com/kevingrace/p/7821529.html
	
	ubuntu发布代号
	ubuntu-18.04.2-live-server-amd64.iso 发布代号bionic
	ubuntu-16.04.3-server-amd64.iso 发布代号xenial
	xenial bionic trusty
	
 gitlab添加用户权限
	settings>members
```
### 七.gitlab开发模式
<div id="gituse7"/>
```
开发流程(pull request流程)
		1.开源软件开发流程(pull request)
			1.在github上进行fork
			2.将A的仓库clone至本地开发环境
			3.在本地环境中创建特性分支
			4.对特性分支进行代码修改开发并进行提交
			5.特性分支push到A的仓库中
			6.在github上对fork来源仓库发送pull request
		2.已部署为中心开发流程(github flow)
			1.令master分支时常保持可以部署的状态
			2.进行新的作业时要从master分支创建新分支,新分支名称要具有描述性
			3.在B新建的本地仓库分支中进行提交
			4.在github端仓库创建同名分支,定期push
			5.需要帮助或反馈时创建pull request,以pull request进行交流
			6.让其他开发者进行审查,确认作业完成后与master分支合并
			7.与master分支合并后立即部署
	
	issues
		issues作用
			1.发现软件的bug并报告
			2.有事想向作者询问,讨论
			3.事先列出今后准备实施的任务
	
	projects
		projects作用
			1.项目管理工具
			2.包含直接转换成issues
			3.还可以包含 pull request
		
	wiki
		wiki作用
			1.编写文档
			2.可以设置权限多人同时进行编辑(默认只允许当前账户编辑)
			3.文档是单独的创库存储(https://github.com/465049568/test.wiki.git)
		wiki提交问题(wiki权限控制)
		https://juejin.im/post/5a3216c8f265da43333e6b54
	
	gist
		gist作用
			1.代替记事本记录简短代码
			2.给对方发送示例代码与共享
		(github page 与 github api)发布动态博客,代码分享
			https://www.moerats.com/archives/624/
			https://segmentfault.com/a/1190000009224437
			http://gohom.win/2015/11/26/gist/
			
			https://www.jianshu.com/p/26351f7c9380
			Lepton：
			https://github.com/hackjutsu/Lepton

gist.github.com 被墙无法访问解决办法
		https://www.cnblogs.com/ryans/p/8196151.html
```