# env_nodejs

一.commonjs 与 AMD 规范

commonjs 与 AMD
https://www.cnblogs.com/yangguoe/p/8474173.html
https://www.cnblogs.com/chenguangliang/p/5856701.html

```
commonjs
	优点：可复用于nodejs环境下并运行,例如做同构应用
		  通过npm发布的很多第三方模块都采用了commonJS规范
	缺点：这样代码无法直接运行在浏览器下,必须通过工具转换成标准的ES5
AMD
	优点：可不转换代码情况直接在浏览器中运行
		  可异步加载依赖
		  可并行加载多个依赖
		  代码可以运行在浏览器环境和nodejs下
	缺点：javascript运行环境没有原生支持AMD,需要先导入实现了AMD的库后才能正常使用
```

ES6将逐渐取代commonjs与AMD(典型的requirejs插件)规范,前端样式支持模块化SCSS
ES6语法详细介绍:
https://es6.ruanyifeng.com/

	JavaScript、ES5和ES6的介绍和区别
	https://www.cnblogs.com/fuheng01/articles/JS.html
	
	Loader
	文件转换翻译员,配置里的module.rules数组配置了一组规则,
	告诉webpack在遇到哪些文件时使用哪些loader去加载和转换
	https://www.cnblogs.com/tugenhua0707/p/9452471.html
	
	Babel
	.babelrc是Babel的配置文件,放在项目根目录下。
	Babel是一个javascript编译器,能将ES6代码转换为ES5代码

less sass 区别？
	https://www.jianshu.com/p/e3f1fcbbccc3

官网:https://nodejs.org
一.nodeJs安装

```
下载解压安装版本,配置好环境变量path(最简只要配置node.exe环境变量path即可):
	NODE_HOME(不是固定的):		D:\soft\dev\node-v12.14.1-win-x64
	Path:				%NODE_PATH%
	Path:				%NODE_PATH%\node-global
目录命令设置(可略)：
	查看变量:
	npm config get cache										//压缩包缓存目录
	npm config get prefix										//全局安装位置
	npm config get registry										//查看镜像代理
	设置变量:
	npm config set registry https://registry.npmjs.org/			//默认官方仓库
	npm config set cache "D:\soft\dev\node-v12.18.3-win-x64\node-cache"					//设置node-cache
	npm config set prefix "D:\soft\dev\node-v12.18.3-win-x64\node-global"				//设置node-global
	npm config set registry http://registry.npm.taobao.org/		//设置淘宝镜像仓库	
```

​	hello world示例：
​	保存文件为helloWord.js


	var http = require('http');
	http.createServer(function (req, res) {
		res.writeHead(200,{'Content-Type':'text/plain'}); res.end('Hello World\n'); 
	}).listen(1337, "127.0.0.1"); 
	console.log('Server running at http://127.0.0.1:1337/');
	
	运行
	node helloWord.js
	浏览器输入http://127.0.0.1:1337

NPM使用:
	官网:https://www.npmjs.com/
	
	NPM是同nodejs一起安装的包管理工具
	npm -v	//查看版本
	npm install npm -g	//升级npm
	
	安装指定模块版本
	npm install 模块@1.0.0
	
	npm安装node.js模块
	npm install <module name>
	
	安装express
	npm install express		#本地安装
	npm install express -g 	#全局安装
	
	查看安装信息
	npm list
	npm list -g
	
	查看某个模块的版本号
	npm list grunt
	
	卸载模块
	npm uninstall express
	
	更新模块
	npm update express
	
	搜索模块
	npm search express
	
	创建模块
	npm init
	
	使用package.json
		name - 包名。
		version - 包的版本号。
		description - 包的描述。
		homepage - 包的官网 url 。
		author - 包的作者姓名。
		contributors - 包的其他贡献者姓名。
		dependencies - 依赖包列表。如果依赖包没有安装，npm 会自动将依赖包安装在 node_module 目录下。
		repository - 包代码存放的地方的类型，可以是 git 或 svn，git 可在 Github 上。
		main - main 字段指定了程序的主入口文件，require('moduleName') 就会加载这个文件。这个字段的默认值是模块根目录下面的 index.js。
		keywords - 关键字

NPM配置
	npm(发布机制)
	http://www.cnblogs.com/penghuwan/p/6973702.html#_label3_0
	

	npm 模块安装机制简介
	http://www.ruanyifeng.com/blog/2016/01/npm-install.html
	
	显示所有环境变量
	npm config ls -l
	
	查看变量
	npm config get cache										//压缩包缓存目录
	
	设置变量
	npm config set cache "D:\nodejs\node-cache"					//设置node-cache
	
	npm install -S -D -g 有什么区别
	-S 等于--save 写入dependencies
	-D 等于--save-dev 写入devDependencies
	-g 全局安装
	npm i xxx 等同于 npm install xxx
	
	dependencies与devDependencies区别
	devDependencies 里面的插件只用于开发环境,不用于生产环境(会打包到js里面)
	dependencies 是需要发布到生产环境的(可以通过网络地址引用)
	-S -D -g 区别
	https://www.jianshu.com/p/2e7f3b69e51e
	
	从指定私服安装(张小鹏)
		npm i element-ui -S --registry=https://registry.npm.taobao.org
		npm install --registry=https://registry.npm.taobao.org

**npm包发布**

```
npm发布站点：
https://www.npmjs.com
1.登陆
	npm login
2.发布(需要在../node_modules/npmrepo3210执行发布)
	npm publish
3.撤销发布(即使撤销发布,再次发布的包名称不能和前面撤销的包明相同)
	根据规范,只有在发包的24小时内才允许撤销发布的包,
	npm unpublish 包名称 --force		//报权限问题要加--force
	unpublish推荐使用如下命令
	npm deprecate 包名称	//并不会撤销你已有的包,但会在任何人尝试安装这个包的时候得到警告"这个包不再维护了"
	
问题：
1.npm ERR! Unexpected token in JSON at position 1 while parsing near
npm config set registry http://www.npmjs.org		//仓库地址修改
npm cache clean --force													//清理缓存

2.npm ERR! Unexpected end of JSON input while parsing near" "
npm config ls -l 																//使用下面命令查询npmrc文件位置npm config ls -l 	
删除.npmrc文件夹

3.发布时（npm ERR! 403 403 Forbidden）
每一次发布都要修改package.json文件版本号（ "version": "1.0.7"）
```

**npm提交问题**

```
1.npm ERR! Unexpected token < in JSON at position 1 while parsing near '问题解决如下
$ npm cache clean --force //然后重新 npm install
实例：
https://blog.csdn.net/zhangdaren/article/details/104855813

2.npm ERR! Unexpected end of JSON input while parsing near“ “
$ npm config ls -l		//查看
然后删除userconfig = "/Users/yuanli/.npmrc"的.npmrc目录

```

nexus上搭建npm本地服务器
https://www.jianshu.com/p/9085f47726a2

node.js实现请求成功返回静态页面的功能
https://blog.csdn.net/kiddingstreet/article/details/80112303

