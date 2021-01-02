# bootstrap生态

```
一.bootstrap
中国官网
www.bootcss.com

视频
https://www.bjsxt.com/down/10123.html

bootstrap主要解决不同屏幕尺寸,设备的匹配

bootstrap框架概述与引用

bootstrap栅格系统

bootstrap全局样式与组件

bootstrap常用自封装插件

bootstrap实例


I.下载
	常用版本为bootstrap3
	https://v3.bootcss.com/getting-started/#download (下载"用于生产环境的 Bootstrap")

	导入
	解压后把里面的文件全部copy到项目(不要向里面添加,修改内容文件及位置)
	导入配置文件
	1.<meta name="viewport" content="width=device-width, initial-scale=1">
	2.<link href="../bootstrap/dist/css/bootstrap.min.css" rel="stylesheet">
	//bootstrap依赖jquery必须引在前面(下面两行应放在body标签里面的最下面)
	3.<script src="../jquery/dist/jquery.min.js"></script>
	  <script src="../bootstrap/dist/js/bootstrap.min.js"></script>
  
II.栅格系统
	1.bootstrap
	描述:栅格创建一个用于页面构建的html布局系统，
	该系统提供的类名来将容器等分12个结构
	说明:
		 响应式布局(一套代码能够在不同页面中有不同加载效果)
		 提供了很多预定义类名,通过使用这些类名能够快速获得css样式
	内容:
		 通过使用.container类名，来定义一个栅格容器
		 通过使用.row,来定义一个栅格中的行容器
		 col-[screenStyle]-[percent]:通过使用复合类名，来定义栅格行钟的一个栅格大小
			A.screenStyle 指的是设备类型(样式能够生效的设备类型)
				可选值:
						col-xs-...:超小型设备:宽度小于768px的设备(手机,平板电脑)
						col-sm-...:小型设备,大于768px，小于992px
						col-md-...:中型设备,大于992，小于1200像素
						col-lg-...:大型设备,大于1200px
			B.percent 栅格一行中占多少列,可选值1-12
		补充:
		自动居中，自动设置了margin,padding
	2.小型设备左6,右6.中型设备左8,右4
	<div class="row">
		<div class="col-xs-6 col-md-8 skyblue">左面的栅格</div>
		<div class="col-xs-6 col-md-4 pink">右面的栅格</div>
	</div>

	3.注意
		1.如非必要，栅格不要嵌套
		2.栅格不只能切割页面，栅格写在哪个元素里面就对哪个元素进行切分
		栅格通常用来切割整个页面,用来页面布局
	
III.全局样式
https://v3.bootcss.com/css/

IV.组件
https://v3.bootcss.com/components/

```

