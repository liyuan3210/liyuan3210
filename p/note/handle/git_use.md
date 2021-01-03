# git命令使用

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

