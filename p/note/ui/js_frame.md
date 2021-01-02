# js框架

### 一.angularjs
官网
https://angularjs.org
中文网
www.angularjs.net.cn

有种情况,传统项目必须开发人员了解全部才能进行开发,而angular
能把一个项目拆分成多个任务,由不同人来完成,提高项目项目效率

mvc
用户操作v → 控制器c → 数据模型m > 返回v

三大块
1.指令
2.服务
3.过滤器

### 二.react

```
https://github.com/liyuan3210/ui
```

### 三.vue

```
VUE
官网:https://vuejs.org/
中文网:https://cn.vuejs.org/

中小型项目适合用vue,超大型项目用angular,react
node版本必须大于8.9.0
管理node多个版本？

vue-cli创建脚手架
https://cli.vuejs.org/
https://cli.vuejs.org/zh/guide/creating-a-project.html

安装vue devtools开发工具

有两种项目开发方式
	1.最原始vue html,js引入方式
	引入一个vue.min.js既可
	2.npm webpack项目管理方式
	需要安装nodejs(npm),webpack环境构建
	安装vue cli脚手架工具

vue cli:快速搭建脚手架
一.安装并创建vue项目(环境webpack3.x)
安装最新vue(用的最多版本vueCli2.x)
	npm install -g @vue/cli
拉取2.x模板
	npm install -g @vue/cli-init
	# vue init 效果 与vue-cli@2.x相同
创建2.x项目
	vue init webpack my-project
查看版本
	vue --version
	OR
	vue -V

创建项目:
# vue init 效果 与vue-cli@2.x相同
vue init webpack my-project

vue init webpack vuebase
project name : 填写
project description : 直接回车
author : 直接回车
vue build: 选择(runtime + compiler:reommended for most users)
install vue-router : n
use ESLint to lint your code ? : n	//代码检查
set up unit tests : n
setup e2e tests with nightwatch : n
Should we run `npm install` for you after the project has been created? : No,I will handle that myself



二.模板语法(<HelloWorld/>组件)
Mustache
{{"哈哈"}}
{{20+1}}
{{"ok"?"yes":"no"}}
{{"hello".split('').reverse().join('')}}
{{"Mustache语法只支持单行的语句"}}
{{ msg }}

三.指令
vue是angular与react的合体
v-once 	//只渲染一次
v-html //解析html
v-bind //解析属性中的对象
v-bind简写 (v-bind:href="url" 或者 :href="url")

v-if //条件渲染(真正的条件渲染),不平凡切换时可以用
v-show //条件渲染(惰性的),平凡换时情况用

v-for 
v-for="item in list"	//遍历数组
v-for="(item,index) in list"	//遍历数组key value
v-for="(value,key,index) in obj" //遍历object



四.事件处理
@click="clickHandler"
或
v-on:click="clickHandler"

改变data中的数据

变异方法
	改变原数组，则可以引起视图更新
		push,pop,shift,unshift,splice,sort,reverse 这些会改变视图更新
	不改变原数组，创建新数组，则无法引起视图更新
		filter,concat,slice不会改变数组更新
		
五.计算属性(<VueDemo />组件)
computed
计算属性是基于它们的依赖进行缓存的。只在相关依赖发生改变时它们才会重新求值

------------------------------------------------
组件
	prop类型
	1.父子组件中传递参数(传递字符串)
	props:["title","age"]
	2.父子组件中传递参数(指定类型)
	props:{
	  title:String,
	  age:Number,
	  age2:String
	}
	3.动态传参:age="age"(:age从data里面取)
	4.默认值与必填项
	5.默认值是数组或对象,必须返回一个function

组件深入
	https://cn.vuejs.org/v2/guide/components-edge-cases.html
	访问根实例
	this.$root.变量名称

	访问父级组件实例
	$parent

	访问子组件实例或子元素
	<base-input ref="usernameInput"></base-input>
	this.$refs.usernameInput

插槽
	https://cn.vuejs.org/v2/guide/components-slots.html
	组件内容来自参数

生命周期
	https://cn.vuejs.org/v2/guide/instance.html
	 创建,渲染,修改,销毁(前与后分别两个)方法函数
	
过渡动画
	https://cn.vuejs.org/v2/guide/transitions.html
	
	动画效果网站
	www.swiper.com.cn
	Animate.css //动画效果css
	Font Awesome //奥森图标http://code.zoomla.cn/boot/font.html
------------------------------------------------
路由
	1.创建路由项目

	2.安装路由组件(也可以在创建项目脚手架时直接安装router)
	npm install --save vue-router
	
	3.main.js引入vue-router
	import VueRouter from 'vue-router'
	...
	Vue.use(VueRouter)
	
	4.创建一个路由
	//main.js创建路由
	const router = new VueRouter({
	  routes:[
		{
		  path:"/",
		  name:"HelloWorld",
		  component:HelloWorld
		}
	  ]
	})
	//注入路由
	......
	  el: '#app',
	  router,//注入路由
	  components: { App },
	......
	
	5.给出路由显示的位置
	//App.vue
	<router-view/>
	#把里面的components,不必要的import 文件删掉
	
	#把路由文件提取出来
	start-router已完成

路由跳转
	<ul>
      <router-link tag="a" to="/hello">hello</router-link>
      <router-link tag="a" to="/learn">learn</router-link>
    </ul>
    <router-view />
路由嵌套
	参数传递：
		参数设置(/hello/:id参数传递)
		routes:[
		{
		  path:"/hello/:id",
		  ......
		}
		参数传送
		<router-link tag="a" to="/learn/paramVue">learn</router-link>
		参数接收
		this.$route.params.id
	
	嵌套路由(添加子路由):
		component:LearnVue,
		children:[
		  {
			 path:"base",
			 component:Base
		  },
		  ......
		]
编程式导航
	除了通过router-link来导航，还可以通过
	this.$router.push("/hello/sss"); //push会有记录,点击浏览器回退可以返回
	还有	
	this.$router.replace("/hello/sss"); //replace没有回退记录,把当前页面替换
	this.$router.go(-1);	//回退页面

参数传递
	命名路由
	<router-link tag="a" to="/HelloWorld/paramVue">learn</router-link>	//通过to="/learn/paramVue"
	动态跳转
	路由配置 routes:[{name:"HelloWorld"}
	params:{id是传的参数
	<router-link tag="a" :to="{name:'HelloWorld',params:{id:paramVue}}">learn</router-link>

```

