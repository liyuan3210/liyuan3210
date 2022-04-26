# html样式css

云盘:
liyuandf@共享资源/基础技能/前端UI/html视频教程

```
					一.html常用标签
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8" />
<!--搜索引擎-->
<title>标题</title>
<meta name="keywords" content="搜索关键字1,搜索关键字2" />
<meta name="description" content="描述" />
<!--
3秒后跳转到baidu
<meta http-equiv="refresh" content="3;URL=http://www.liyuan3210.com/" />
-->
<meta http-equiv="pragma" content="no-cache" />
</head>
<body>
<!--无序列表-->
	<ul>
		<li>列表ul</li>
		<li>列表ul</li>
		<li>列表ul</li>
	</ul>
<!--有序标签-->
<ol>
	<li>列表ol</li>
	<li>列表ol</li>
	<li>列表ol</li>
</ol>
<!--定义列表-->
<dl>
	<dt>苹果</dt>
	<dd>
		苹果是种低热量食物，每 100 克只产生 60 千卡热量；苹果中营养成份可溶性
		大，易被人体吸收，故有“活水”之称，有利于溶解硫元素，使皮肤润滑柔嫩。
	</dd>
	<dt>鸭梨</dt>
	<dd>鸭梨也是一种水果.....</dd>
	<dt>橙子</dt>
	<dd>橙子.........</dd>
</dl>
<!--图片定义链接区域-->
<img src="images/1.jpg" alt="一对猫咪" usemap="#img1" />
<map id="img1" name="img1">
	<area shape="rect" coords="184,33,391,258" href="http://www.baidu.com" alt="百度一下" target="_blank" />
</map>
<!--
表格由 table 标签创建，
tr 标签来创建表格的行，
td 标签或者 th 标签创建表格的列。

-->
<!--
自定义属性

属性名称在 html5 里面我们推荐加上 data-的前缀后面接一个字母或者多个字母都可
以，值我们也可以随便写，我们自己定义的属性默认不会对页面展示造成任何影响！
-->
<!--
标签的类型？

div 标签(块级元素)
可以把 div 标签看成一个可以存放标签的容器！
我们讲的常用标签几乎都可以嵌套在 div 标签的里面
块级元素

span 标签(内联元素)
使用 <span> 来组合内联元素，以便通过 css 来美化它们。
-->
</body>
</html>
```

----------------------------------------------------------------------------------------------------
						二.CSS给网页装潢
1.使用css三种方法
	1>外部样式
		<link rel="stylesheet" type="text/css" href=”css 文件名” />
	2>内页样式(就是把外部样式文件内容copy到style标签里面)
		<style type="text/css">
			p {
				font-size:40px;
				color:red;
			}
			/*
			注释
			*/
		</style>
	3>行内样式
		<p style="color:red;">私房货</p>

2.选择器
	1)简单选择器
		1>id选择器(通过给标签加 id 属性，并给与该属性一个值（这个值我们可以称为 id 名称）)
			#div1 {
				font-size:30px;
				color:red;
			}
			<div id="div1">div容器</div>
		2>类(class)选择器(给标签加 class 属性并给与该属性一个值(这个值我们可以称为类名))
			.red {
				font-size:30px;
				color:red;
			}
			<div class="red">div容器</div>
		3>标签选择器(直接将 html 标签名作为选择器)
			div {
				font-size:30px;
				color:red;
			}
	2)复杂选择器
		1>交集选择器(既要属于是前面的标签并且是后面的id或者类选择器)
			一个标签选择器后边跟一个类选择器或者一个 ID 选择器，中间不能有空格。它要求必
			须是属于某一个标签的，并且声明了类选择器或者 ID 选择器。
			例如:
			div.mycolor{…} 类别为 mycolor 的层才会被选中，应用该样式。
			div#mydiv{…} id 为 mydiv 的层才会被选中，应用该样式。
		2>并集选择器
			就是多个选择器以逗号相连，只要满足其中之一它都会被选中！
			我们上面学的选择器都可以被写入并集选择器
			div,p,h1,div.mycolor,div#mydiv {…}
		3>后代选择器(是指定标签下面的子标签交集选择器)
			是以空格相连的的多个选择器，外层的选择器写在内层的选择器前面！
			根据元素的后代关系来作为选择的筛选条件！
			div h1.first span.firstletter {…}

	3)伪类选择器(鼠标放到链接上,改变字体颜色)
		a:hover {
			color:red;
		}
		<a href="http://www.liyuan3210.com" target="_blank">个人网站</a>
	4)通配符选择器
		它是一种特殊的选择器，它用*符号表示，它可以定义文档中所有元素对象的样式!
		* {}
----------------------------------------------------------------------------------------------------
3.构造文本
	1).CSS 长度值得单位：
		%——百分比
		in——寸
		cm——厘米
		mm——毫米
		pt——point，大约 1/72 寸；
		pc——pica，大约 6pt，1/6 寸；
		px——屏幕的一个像素点；
		em——元素的 font-size；
	2).多个字体用逗号隔开(windows字体在C:\Windows\Fonts目录下面)
		font-family:"Times New Roman",Georgia,Serif;
		系统优先"Times New Roman"字体否则Georgia依次到Serif(以逗号隔开)
	3).样式继承(继承父代的样式)
		<body>
		<span>字体<span>
		</body>
		body{
		font-size:20px;
		}
		上面css默认也给span标签里面的字体进行修饰(默认样式进行继承)，
		通过前面学习的选择器灵活控制样式

4.参数简化设置
	1).属性元素直接设置(简化设置)
		一个一个设置
		/*background-color:#66FF99;*/
		/*background-image:url(../images/1.jpg);*/
		/*background-repeat:no-repeat;*/
		/*
			1)background-position:center left;
			使用关键字 ：top、bottom、left、right 和 center时，表示
			第一个值是 y轴（垂直方向）,第二个值是x轴（水平方向）
			
			2)background-position:50% 3%;
			使用具体的数字，例如百分比的时候、或者像素（px为单位）
			第一个值 指得是X轴（水平方向）,第二个只是Y轴（垂直方向的偏移）
		*/
		/*background-position:50px bottom;*/
		可以简写
		background:#66FF99 url(../images/1.jpg) no-repeat 50px bottom;
		/*
			background:颜色值 图片的地址 图片是否平铺 水平偏移值 垂直偏移值;
			background:#cccccc;
			不需要的设置选项可以不写
		*/
		
		/*
		border-left:10px solid #cccccc;
		border-top:10px solid red;
		border-right:10px solid green;
		border-bottom:10px dashed #FF00FF;
		*/
		可以简写
		border:20px dashed #cccccc;
		下面覆盖
		border-bottom:none;

5.构造块级元素

	1)后代元素长度大于祖辈元素的大小时候的处理方法：
		overflow可能的值：
			visible：默认，内容不会被修剪，会呈现在元素框之外。
			hidden：超出的内容会被修剪掉，直接不现实。
			scroll：超出内容会被修剪，但是浏览器会显示滚动条以便查看其余的内容。
			auto：如果内容被超出，则浏览器会显示滚动条以便查看其余的内容。
			inherit：规定应该从父元素继承 overflow 属性的值。
			
	2).元素性质相互转化：
			display:block;//强制转换块级元素
			display:inline;//设置成内联元素
			display:inline-block;//不独占一行
			display:none;//隐藏		
盒子模型
	1).内边距：边框和内容区之间的距离，通过 padding 属性设置
		内边距设置方法：
		padding-top:10px;
		padding-right:10px;
		padding-bttom:10px;
		padding-left:10px;
		简些：
			padding:10px;
			padding:上下 左右;
			padding:上 右 下 左;
		padding:10px 20px 40px 30px;
	2).外边距：元素边框的外围空白区域是外边距，通过 margin 属性设置
		margin:;用法同上！
		
6.浮动
	可以解决多个div如果要并排在一行显示不换行(也可以使用display:inline-block解决,
	但多个div需要并排显示时就需要用到浮动了),
	1).浮动的设置方法：
		float:left;
		float:right;
	2).不想标准流中的元素受到浮动的影响怎么办？
	clear:both;
		none:默认值。允许两边都可以有浮动对象
		left:不允许左边有浮动对象
		right:不允许右边有浮动对象
		both:不允许有浮动对象
	

	结论：
		被设置浮动的元素会组成一个流，并且会横着紧挨着排队，直到父元素的
		宽度不够才会换一行排列。

7.相对定位与绝对定位
	1)相对定位(相对于上一个标签流)
		position:relative;
				left:;(设置了left就不要设置right)
				right:;
				top:;(设置了top就不要设置bottom)
				bottom:;
				为元素设置相对定位之后，元素依然会占据原来的空间，依然在标准流中！
	2)绝对定位(相对于浏览器左上角位置(相对于外层标签的定位))
		position:absolute;

		为元素设置绝对定位之后，元素不会占据原来的空间，脱离了原来的队伍！
	3)固定定位(相对于浏览器窗口,多行数据有滚动条可以 固定浮动)
		position:fixed;
	
		相对于浏览器的窗口进行定位
	4)重叠元素的堆叠顺序设置
		使用 z-index:;对设置了 相对,绝对,固定定位的元素进行堆叠顺序的设置
		(设置了上面三种定位的才有效果),置的数值越大即堆叠在越上层,该属性可以是负值。
	
	定位
	<div style="position:absolute;top:0mm;left:0mm;width:100mm;height:15mm;border:1px solid #000;">
			top
	</div>
8.构造列表
	1)多张图片放在一张图片里面？？？
		#div1 {
			margin:10px 0 0 0;
			width:25px;//图片宽
			height:18px;//图片高
			background:url(images/icon.png) -120px -3px no-repeat;//取图片的位置
		}
	
9.布局
	1)首页上下左右布局
	？？？
	
----------------------------------------------------------------------------------------------------
浮动居中
margin:0 auto;

控件居中
margin-left:auto;
margin-right:auto;
margin-top:auto;
margin-bttom:auto;

//vertical-align: middle;

工具箱代码
.box{
width:200px;
height:200px;
background:rgba(109,154,219,0.08);
border-radius:8px;
border:1px solid rgba(109,154,219,1);
display:inline-block;
}
.group{
background:red;
text-align: center;
}

<div class="panel">
	<div class="group">
		<div class="box">盒子</div>
		<div class="box">盒子2</div>
		<div class="box">盒子3</div>
	</div>
</div>

样式优先级box1覆盖box
class="box box1"

CSS函数
width:calc(100% - 70px);
----------------------------------------------------------------------------------------------------
less安装
https://www.cnblogs.com/olivianate/p/5354695.html

sass安装
https://www.sass.hk/install/

2019前端趋势
https://www.douban.com/group/topic/138636004/