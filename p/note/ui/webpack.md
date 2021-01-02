# webpack

```
webpack(js项目打包工具)
	官网:https://webpack.js.org/
	安装:npm install webpack -g

	创建项目:
	创建app目录>
		新建runoob1.js文件
		新建index.html文件
	webpack打包>
		webpack runoob1.js bundle.js
		
	网上学习	
	https://segmentfault.com/a/1190000012631766

	webpack视频教程
	https://www.bjsxt.com/down/9275.html
----------------------------------------------------------------------------------------------------
云盘:
liyuandf@共享资源/基础技能/前端UI/webpack
一.webpack是什么？
	1.webpack构建工具
	2.自带模块化(commonjs规范)
	3.编译：es6 -> es5 , jsx -> es5 , ts(typescript) ->  js
	4.gulp所做的事情，webpack都可以做到
	5.自带服务器，服务器也是基于Node（webpack-dev-server）
	6.那些环境经常使用到webpack：react 、 vue
	7.webpack版本：1.x 、2.x 、3.x 、4.x版本

二.安装webpack3.x并打包测试运行
	1.安装全局webpack
		npm install -g webpack@3.x
		npm install -g webpack-cli@2.x
	2.项目初始化package.json
		npm init -y
	3.安装项目依赖的webpack
		npm install -D webpack@3.x
	4.项目根目录创建两个文件夹src和dist(两个文件夹可以自己定义)
		src : 源码文件
		dist : 编译之后的文件
	5.编写代码
		app.js:
		document.write("hello webpackc");
	6.执行webpack
		webpack 路径/入口文件  路径/出口文件
		webpack src/app.js    dist/bundle.js
		webpack src/app.js -o dist/bundle.js //webpack4.x打包命令

	7.编写dist/index.html页面
		引入<script src="bundle.js"></script>
		打开index.html就可以看到效果了

	8.模块化导出,导入
		hello.js：
			module.exports = function(){
			var hello = document.createElement("div");
			hello.textContent = "hello Webpack3.x";
			return hello;
			}

		修改app.js
			var hello = require("./hello.js");
			document.getElementById("root").appendChild(hello());
			编译打开index.html页面同样可以达到效果
			注意要把bundle.js引入放到body下面

	9.跟目录新建webpack.config.js文件
		配置入口和出口,添加如下内容
		module.exports = {
			entry:__dirname +"/src/app.js",
			output:{
				path:__dirname + "/dist",
				filename:"bundle.js"
			}
		}
	上面配置好后webpack src/app.js dist/bundle.js可以简写成webpack命令
	__dirname是nodejs提供的当前目录

	10.在package.json文件中代替webpack的执行
		webpack执行的快捷方案
		"scripts": {
			"build":"webpack"
		}
	执行：npm run build 代替了webpack命令
	11.调试
		生成错误信息文件
		配置webpack.config.js文件
		添加devtool:"eval-source-map"
		devtool:
			eval
			source-map
			hidden-source-map
			inline-source-map
			eval-source-map
			cheap-source-map
			cheap-module-source-map

三.服务器
	1.全局安装服务器
		npm install -g webpack-dev-server@2.x
	2.安装项目依赖
		npm install -D webpack-dev-server@2.x
	3.运行webpack服务器
		webpack-dev-server
	4.配置服务器的快捷执行方案
		"scripts": {
			"build": "webpack",
			"dev":"webpack-dev-server"
		}
	执行：npm run dev
	5.修改服务器根路径
		"dev":"webpack-dev-server --content-base dist"
	6.热更新
		"dev":"webpack-dev-server --content-base dist --inline --hot"
	7.服务器配置
		--content-base：指定服务器运行根目录
		--inline：在线更新(--hot可省略)
		--port: 修改端口
		注意
		webpack版本必须是3.x

四.module
	loaders(use)
		loader是webpack可以通过配置脚本，或者外部依赖来执行一些功能
		例如：es6 -> es5  jsx -> js  less -> css
		配置loaders
			1.test：一个匹配loader要做操作的文件的一个正则表达式（必须）
			2.loader(use)：loader要执行的任务的名字（必须）
			3.options:为loader提供一些外部选项配置(可选项)
		json格式的数据转化成js的对象
			注意：当前的json-loader只是为了测试，我们当前安装的webpack的版本3.x
			事实上，在当前版本中，已经集成了json-loader,不需要单独安装了
	
	1.安装json-loader(webpack3.x已集成不必安装)
		npm install -D json-loader

	2.创建json格式数据./data/data.json
		{
			"name":"liyuan",
			"desc":"json数据测试"
		}
	3.编写hello.js
		var data = require("./data/data.json");
		module.exports = function(){
			var hello = document.createElement("div");
			hello.textContent = data.name+":"+data.desc;
			return hello;
		}
	4.编写配置文件代码webpack.config.js(如果没集成需要编写配置文件的)
		webpack3.x或老的版本配置
		module:{
			rules:[
				{
					test:/\.json$/,
					use:"json-loader"
				}
			]
		}
		webpack4.x版本(不需要安装npm install -D json-loader)
		{
			test: /\.special\.json$/,
			type: "javascript/auto",
			use: "special-loader"
		}

	webpack4.x json loader
		http://www.vccoo.com/v/3wvh11_4
		https://www.cnblogs.com/wmhuang/p/8967639.html
		https://blog.csdn.net/web_xyk/article/details/79971742
		webpack.config.js配置
		{
		test: /\.special\.json$/,
		type: "javascript/auto",
		use: "special-loader"
		}

	webpack4.x babel-loader
		https://blog.csdn.net/DreamFJ/article/details/81671973
		安装依赖
		https://www.npmjs.com/package/babel-loader
		
		1.安装依赖
		npm install -D babel-loader@7 babel-core babel-preset-env
		2.webpack.config.js配置
		rules: [
			{
				test: /\.js$/,
				exclude: /node_modules/,
				loader: 'babel-loader',
				options: {
					presets: ['env']
				}
			}
		]
		
五.es6 -> es5
	1.安装依赖
		npm install -D babel-core babel-loader babel-preset-es2015
	2.配置webpack.config.js文件
		module:{
			rules:[
				{
					test:/\.json$/,
					use:"json-loader"
				},
				{
					test:/\.js$/,
					use:"babel-loader",
					//可以不用加
					//options:{
					//	presets:["es2015"]
					//}
				}
			]
		}

		hello.js
		module.exports = function(){
		  let hello = document.createElement("div");
		  hello.textContent = `${data.name}:${data.desc}`;	//反引号
		  return hello;
		}

		app.js
		let hello = require("./hello.js");
		document.getElementById("root").appendChild(hello());
		
六.构建react环境(webpack+ES6+React)
  1.安装react
    npm install --save react react-dom
    npm install --save-dev babel-preset-react
  2.编写配置文件
    {
      test:/\.(js|jsx)$/,
      use:"babel-loader",
	  //webpack2.0方案,
	//options:{
	//	presets:["es2015"]
	//}
    }
	注意react的jsx语法文件后缀名是.jsx
	//按理说下面就可以,也可以用.babelrc文件来替代带
	{
		"presets": ["es2015", "stage-2","react"]
	}
    增加.babelrc文件
		{
		"presets": ["es2015", "stage-2","react"],
		//下面是react兼容性处理,也可以留着
		"env": {
		"development": {
		  "plugins": [["react-transform", {
			"transforms": [{
			  "transform": "react-transform-hmr",
			  "imports": ["react"],
			  "locals": ["module"]
			}]
		  }]]
		}
		}
		}
  3.编写代码app.js
    import React from "react"
    import ReactDOM from "react-dom"
    class App extends React.Component{
      render(){
        return(
          <div>React EVN</div>
        )
      }
    }
    ReactDOM.render(<App />,document.getElementById("root"))
	
	4.hello组件
	创建
	import React from "react"
	export default class Hello extends React.Component{
	  render(){
		return(
		  <div>
			<p>Hello React</p>
		  </div>
		)
	  }
	}
	引用
	import Hello from "./hello.js"
	return(
	  <div><Hello/></div>
	)

10.CSS处理
  1.安装css相关依赖
    npm install -D css-loader style-loader
	
	app.js引入css
	import "./app.css"
	样式引用
	render(){
		return(
			<div className="container"><Hello /> <Header /></div>
		)
	}
	
  2.添加配置
    {
      test:/\.css$/,
      use:[
        "style-loader",
        "css-loader"
      ]
    }

11.图片配置
  1.安装依赖：
    npm install -D file-loader url-loader
  2.添加配置
    {
      test:/\.(png|jpg|gif|jpeg|svg)$/,
      use:"url-loader?limit=2048" // 大于2M进行压缩
    }

12.Less和Sass
  1.安装
    npm install --save-dev less less-loader
  2.添加配置
    {
      test:/\.less$/,
      use:[
        "style-loader",
        "css-loader",
        "less-loader"
      ]
    }

13.插件(plugins)
  1.打开浏览器(每次运行服务自动打开浏览器)
    安装：
      npm install -D open-browser-webpack-plugin
    配置webpack.config.js：
	var borwerOpen = require("open-browser-webpack-plugin");
	//与module同级
	plugins:[
		// 自动打开浏览器插件
		new borwerOpen({
		url:"http://localhost:8080"
		})
	]
  2.HTML模板
    安装：
      npm install -D html-webpack-plugin
    配置：
	var borwerOpen = require("open-browser-webpack-plugin");
	var HTMLTemp = require("html-webpack-plugin");
    new HTMLTemp({
      template:__dirname + "/src/index.temp.html"
    })
	npm run build
	npm run dev //不会生成缓存缓存文件
  3.内置插件（省略后缀名）
  import Header from "./header/index.js"可以省略"./header/index"
  //与module同级
    resolve:{
       extensions:['.js','.jsx']
    }
14.生产环境的搭建
  生产环境
  
  安装
    npm install -D cross-env
    npm install -D babel-plugin-react-transform
    npm install -D react-transform-hmr
    npm install -D babel-preset-stage-2

```

