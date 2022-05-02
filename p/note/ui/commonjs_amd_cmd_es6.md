## JS模块化规范管理丨commonjs丨AMD丨CMD规范丨ES6



#### 介绍

```
1.commonjs
* 每个文件都可以当作一个模块
* 在服务端：模块的加载时运行时同步加载的
* 在浏览器端：模块需要提前编译打包处理
* 在浏览器端编译打包需要用到 browserify
服务端实现：node.js
浏览器端实现：Browserify
http://browserify.org
browserify称为commonjs浏览器的打包工具

2.AMD
* Asynchronous Module Definition(异步模块定义)
* https://github.com/amdjs/amdjs-api/wiki/AMD
* 专门用于浏览器端，模块的加载是异步的
实现浏览器端：Require.js
http://wwww.requirejs.cn
http://wwww.ruanyifeng.com/blog/2012/11/require_js.html

3.CMD
用的不多，综合了commonjs与AMD
浏览器实现：Sea.js

4.ES6
语法：导出模块（export）,导入模块（import）
实现：使用Babel将ES6编译为ES5代码，使用browserify编译打包js

```

#### 一。JS模块化规范管理（模块进化）

#### 二。commonjs

**1.服务端运行**

```
commonjs_node

1.创建项目,两种方式
* 创建文件package.json
{
  "name": "commonjs_node",
  "version": "1.0.0"
}

* npm init
使用模版创建

2.运行
* node app.js
* 配置scripts运行npm run test
"scripts": {
	"test": "node index.js"
}
```

**2.浏览器运行**

```
commonjs_browserify
1.创建项目
2.下载browserify
* 全局：npm install browserify -g
* 局部：npm install browserify --save-dev
3.编写模块代码
4.打包处理js
browserify js/src/app.js -o js/dist/bundle.js		//输出bundle.js文件
5.创建index.html
<script type="text/javascript" src="./js/dist/bundle.js"></script>
```

#### 三。AMD

https://requirejs.org/

https://www.requirejs-cn.cn/

https://github.com/requirejs/requirejs

**1.没用AMD规范**

```

```

**2.使用AMD规范**

```

//使用AMD规范插件 与 没使用AMD插件区别
requirejs.config({
    baseUrl:'/js', // 基本的路径 出发点正在根目录下 不配置时从main.js出发去找
    paths:{
      dataService:'./modules/dataService', //不要加.js 默认会添加后缀
      alerter:'./modules/alerter'
      // jquery:'./libs/jquery-1.10.1',
      // angular:'./libs/angular'
    },
    shim: {
      angular:{
        exports: "angular"
      }
    }
  })
```

#### 四。CMD

```

```

#### 五。ES6

```
1.创建项目

2.安装babel-cli,babel-preset-es2015和browserify
* npm install babel-cli browserify -g
* npm install babel-preset-es2015 --save-dev

3.定义.babelrc(还可以配置react,解析jsx)
{
	"presets": ["es2015"]
}

3.编写模块代码
4.编译
* 使用babel将ES6编译为ES5(包含commonJS语法)：
$ babel js/src -d js/lib
* 使用browserify编译js：browserify:
$ browserify js/lib/main.js -o js/dist/bundle.js		//dist要提前创建好，输出bundle.js文件
5.创建index.html
<script type="text/javascript" src="./js/dist/bundle.js"></script>
```

#### 六。vue教程

能够快速搭建vue工程项目

1.vue基础

vue-basics

2.vue项目

vue-project



**课程：**

一。commonjs规范
https://www.bilibili.com/video/BV1bs411E7pD
二。AMD,CMD,ES6
https://www.bilibili.com/video/BV18s411E7Tj
一。Webpack
https://www.bilibili.com/video/BV1e7411j7T5
二。vue教程
https://www.bilibili.com/video/BV1Zy4y1K7SH





