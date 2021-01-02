# hexo使用

hexo
	hexo官网:https://hexo.io
	node官网:https://nodejs.org

	安装node
	要安装hexo,先得安装node,解压node-v6.11.0-win-x64.zip,配置好path
	
	安装git
	命令行工具:https://git-for-windows.github.io
	
	1.hexo安装方式(如下两种)
		1）npm install -g hexo-cli 全局安装(即可用hexo命令)
		2）npm install hexo 局部安装,下面两种方式运行hexo
			A . npx hexo <command>
			B . 将hexo所在目录node_modules添加到环境变量中（这个貌似有问题???）
		版本问题:
			需要固定稳定版本可以去掉package.json配置文件里面版本号的`^`．
			`^`,它表示最新版本．否则在运行hexo server后会修改package.json配置文件（自动升级hexo版本）．
			
	2.插件安装:
		·hexo提交git插件
		npm install --save hexo-deployer-git
		配置项目_config.yml(hexo g -d 发布到master分支,需要提前配置好mygithub`ssh`):
			deploy:
			  type: git
			  repo: git@mygithub:liyuan3210/blog.git
			  branch: master
			  
		repository必须是ssh形式(git@github.com:xxx/xxx.github.io.git),不能是https，url形式
			  
	3.hexo命令使用
		1）创建项目blog并且生成blog项目文件夹
		hexo init blog(hexo init也可以,按照指令创建)
		npm install(进入目录安装所需依赖)
		
		2）启动项目并验证
		hexo server
		访问
		http://localhost:4000
		
		3）创建博客
		hexo new "My New Post"
		创建的文件在..\source\_posts\My-New-Post.md
		source/_post目录下可以任意创建目录(如以年/月/日)来管理src文件
		
			创建默认布局(layout):
				hexo new [layout] <title>
				post		source/_posts		//默认布局
				page		source			//在根目录下一个title一个目录,
				draft		source/_drafts		//保存草稿,使用hexo publish [layout] <title>发布到_posts
				１)左边菜单
				　	categories: 前端
				２)标签
					tags: ui
	
		问题
		有一个问题，发现 "My New Post" 被发了2遍，
		在Hexo server所在的git bash窗口也能看到create了2次。
		
		4）hexo清理，生成，发布(利用git插件发布到github或gitee)
		部署顺序
		1>.hexo clean
		2>.hexo generate	//生成文件在..\public目录下生成一系列html,css等文件
		3>.hexo deploy
		
		5）监视文件变动
		hexo generate --watch
		
		6）生成文件并发布
		hexo generate --deploy
		hexo deploy --generate
		上面两个命令可以简写为
		hexo g -d
		hexo d -g
		
	4.hexo配置
		1）配置 _config.yml,github,gitee
		博客项目路径(https://liyuan3210.github.io/blog)方式访问
			url: https://liyuan3210.github.io/blog
			root: /blog
		2）模板themes/3-hexo/_config.yml配置
			博客地址:https://liyuan3210.github.io/blog
			blog_path: /
			改为
			blog_path: /blog
			
		3）换模板
			1>clone模板下来
			git clone https://github.com/yelog/3-hexo.git 3-hexo	//删除隐藏文件
			copy模板3-hexo到theme目录下面
			
			2>修改项目_config.yml 配置文件theme为模板名称
			theme: 3-hexo
			
			3>修改模板3-hexo/_config.yml文件 blog_path 属性
			示例博客地址:https://liyuan3210.github.io/blog
			blog_path: /blog
		
		4) url与source资源管理
			source/_post目录下可以任意创建目录(如以年/月/日)来管理src文件,
			访问时会根据创建的目录作为link.
			1>.这种资源管理方式需要修改permalink属性
				permalink: :year/:month/:day/:title/
				改为
				permalink: :title/
			2>.文件名生成规参数则配置new_post_name(这个不用修改根据默认title)


​		
	问题：
		1.问题2(hexo generate):WARN  No layout:
		需要使用MINGW64命令行来操作,否则缺失模板