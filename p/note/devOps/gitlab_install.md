# gitlab安装

```
分布式版本管理系统git
官网：https://github.com
GUI工具(github公司推出的):https://desktop.github.com
客户端工具:https://gitforwindows.org(https://git-for-windows.github.io)
		   https://git-scm.com

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
	
二.pull request
	开发流程(pull request流程)
		开源软件开发流程(pull request)
			1.在github上进行fork
			2.将A的仓库clone至本地开发环境
			3.在本地环境中创建特性分支
			4.对特性分支进行代码修改开发并进行提交
			5.特性分支push到A的仓库中
			6.在github上对fork来源仓库发送pull request
		已部署为中心开发流程(github flow)
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
			
	github OAuth实现用户登录
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
	
	gitlab添加用户权限
	settings>members
	
三.A记录,CNAME配置
	githup创建仓库:
	new repository>仓库名:liyuan3210
	settings>github pages(选择模板创建页面，然后就可以看到域名liyuan3210.github.io)

	绑定域名
	githup仓库地址：liyuan3210.github.io
	阿里云注册一个域名www.liyuan3210.org，在阿里云配置域名解析
	先在本地ping一下liyuan3210.github.io指向的ip(151.101.72.133)
	1.配置A记录(记录类型) : 指向ip151.101.72.133(记录值):@(主机记录)
	2.配置CNAME(记录类型) : 指向域名liyuan3210.github.io(记录值):www(主机记录)

	CNAME配置:
	在新建好的仓库liyuan3210跟目录下新建CNAME文件,内容写上要绑定的域名www.liyuan3210.org

	NOTE
	花生壳配置CNAME,A记录时，需要先关闭花生壳域名服务(因为关闭后会清空CNAME,A记录)
	
	泛域名配置
		https://www.bilibili.com/video/av21336992
		1.域名添加泛域名,无限个域名支持服务
		2.配置域名解析
			ngrok		A记录		94.191.73.142
			*.ngrok	    A记录		94.191.73.142
			
	A，TXT记录
		http://www.ezloo.com/2011/04/a_mx_cname_txt_aaaa_ns.html
		https://www.22.cn/help_34.html
		
	网站流量统计
	https://www.cnblogs.com/hejun26/p/9802549.html
	
	网站域名带www和不带的区别
	https://zhidao.baidu.com/question/332729665886275525.html
	
四.gitee马云API调用
	github token ： 3966607594ddbf3fd007f12e02fa0290cae2a9c9
	
	条件:
	设置->私人令牌access tokens(申请一个),还可以OAuth三方应用授权登录
	通过令牌调用api,操作代码片段,issus等等
	
	1.添加代码片段
	curl -X POST --header 'Content-Type: application/json;charset=UTF-8' 'https://gitee.com/api/v5/gists?access_token=83832ab64bc489240c65f2fb94021202' -d '{"files":{"API-call-file.txt":{"content":"Api call content liyuan3210"}},"description":"API-TEST-3210","public":"true"}'

	2.添加issus
	curl -X POST --header 'Content-Type: application/json;charset=UTF-8' 'https://gitee.com/api/v5/repos/liyuan3210/issues' -d '{"access_token":"83832ab64bc489240c65f2fb94021202","repo":"pages","title":"API-Call-test","body":"test content haha","labels":"question"}'

```

