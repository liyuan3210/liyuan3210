# 一。bootstrap生态

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

# 二。js插件

### js组建介绍：
```
1.bootstrap
  官网：https://getbootstrap.com
  中文官网: https://www.bootcss.com
  
  插件:
	A>Bootstrap-3-Typeahead(autocomplete模糊匹配)
		官网:https://github.com/bassjobsen/Bootstrap-3-Typeahead
	B>bootstrap-datetimepicker(时间控件)
		官网:http://www.bootcss.com/p/bootstrap-datetimepicker
		github:https://github.com/uxsolutions/bootstrap-datepicker
	C>bootstrap-table(列表组件)
		官网：https://examples.bootstrap-table.com/
		github:https://github.com/wenzhixin/bootstrap-table
		实例
			https://www.cnblogs.com/ziyoublog/p/9055790.html
			http://www.cnblogs.com/landeanfen/p/4976838.html
			
2.jquery-ui(jquery样式框架)
	官网:https://jqueryui.com
	
3.jquery-validation(form表单校验,ajax submit提交)
	官网:https://jqueryvalidation.org/
	github:https://github.com/jquery-validation/jquery-validation
	
4.zTree(树形插件)
	官网:http://www.treejs.cn
	github:https://github.com/zTree/zTree_v3
	
5.kindeditor(在线编辑器)
	官网:http://kindeditor.net
	
6.SVG
  A>Method-Draw
	https://editor.method.ac
	https://github.com/duopixel/Method-Draw
  B>SVG-Edit
	https://raw.githack.com/SVG-Edit/svgedit/master/editor/svg-editor.html
	https://github.com/SVG-Edit/svgedit
	
7.时间日期
	下载
		http://www.my97.net/

	包引入
		<script type="text/javascript" src="/js/WdatePicker/WdatePicker.js"></script>

	html页面
		<input type="text" th:id="startTime" name="startTime"  onclick="WdatePicker({dateFmt:'yyyy-MM-dd HH:mm:ss'})"/>

8.form表单校验,ajax submit提交
	官网
		https://jqueryvalidation.org/
		https://github.com/jquery-validation/jquery-validation
	包引入
		<script type="text/javascript" src="/js/jquery.validate.min.js"></script> //form表单验证提交
		<script type="text/javascript" src="/js/jquery.form.js"></script>  //ajaxSubmit
	html页面
		<form id="manualEntry" action="/order/recordingForm" method="post" >
			<input type="text" id="contactNameSender" name="contactNameSender" />
			<button type="submit" id="submitId" name="submitName">提交</button>
		</form>
	js调用
		$("#manualEntry").validate({
			success: "valid",
			submitHandler: function (form) {//验证没问题执行
			   alert('提交');
			   var objParam = $("#manualEntry").serializeJson();
			   var option = {
						url : '/order/manualEntry',
						type : 'post',
						resetForm: true,
						//dataType : 'json',//指定返回的数据类型，无需再对json字符串进行解析。
						data : {postData:JSON.stringify(objParam)},
						success : function(data) {
							alert(data.message);
						}
					};
			   $('#manualEntry').ajaxSubmit(option);
			},
			rules: {
			  contactNameSender: {
				required: true
			  }
			},
			messages: {
			  contactNameSender: {
				required: "姓名不能为空"
			  }
			}
		});
		
9.ajax同步异步参数(让ajax执行完后再继续往下执行)
	$.ajax({
		cache: false,
		async: false,// 太关键了,同步和异步的参数
		dataType: 'json',
		type: 'post',
		url: "action",
		success: function (data){
			alert("1");
		}
	);
	alert("2")
10.js正则全部替换
	var r= "1\n2\n3\n";
	//将字母\n替换成分号
	alert(r.replace("\n",";"));
	结果：1;2\n3\n 只替换了第一个
	
	var r= "1\n2\n3\n";
	//将字母\n替换成分号
	alert(r.replace(/\n/g, ";"));
	结果：1;2;3; replace 的第一个参数可以是正则表达式，/g标识全文匹配。
11.load html
	实例：https://www.cnblogs.com/lixiaoran/p/6251397.html
	$("#div").load("test.html #content");  
	
	实例：
	$.post("url",{url:href},function(data){
		$("#span").html('');
		$("#span").html(data);
	});
```
### bootstrap时间控件

```
1.时间控件
	官网
		http://www.bootcss.com/p/bootstrap-datetimepicker/
	源码
		https://github.com/uxsolutions/bootstrap-datepicker
	包引入
		<script type="text/javascript" src="/js/datetimepicker/bootstrap-datetimepicker.min.js"></script>
		<script type="text/javascript" src="/js/datetimepicker/bootstrap-datetimepicker.zh-CN.js"></script>
	html页面
		<input type="text" id="startTime" name="startTime" data-date-format="yyyy-mm-dd hh:ii:ss"/>
		<input type="text" id="endTime" name="endTime" data-date-format="yyyy-mm-dd hh:ii:ss"/>
	js实例化
		$('#startTime').datetimepicker({
			language:'zh-CN',
			minView: 2,	//去掉小时分钟选择
			autoclose: 1 //自动关闭
		});
		$('#endTime').datetimepicker({
			language:'zh-CN',
			minView: 2,	
			autoclose: 1
		});
	初始化值
	function startDate() {
		var date = new Date();
		date.setDate(date.getDate()-7);
		var seperator1 = "-";
		var seperator2 = ":";
		var month = date.getMonth()+1;
		var strDate = date.getDate();
		var currentdate = date.getFullYear() + seperator1 + month + seperator1 + strDate
		        + " " + date.getHours() + seperator2 + date.getMinutes()
		        + seperator2 + date.getSeconds();
		var currentdate2 = date.getFullYear() + seperator1 + month + seperator1 + strDate
		        + " " + "00" + seperator2 + "00"
		        + seperator2 + "00";
		return currentdate2;
	}
	function endDate() {
		var date = new Date();
		var seperator1 = "-";
		var seperator2 = ":";
		var month = date.getMonth()+1;
		var strDate = date.getDate();
		var currentdate = date.getFullYear() + seperator1 + month + seperator1 + strDate
		        + " " + date.getHours() + seperator2 + date.getMinutes()
		        + seperator2 + date.getSeconds();
		var currentdate2 = date.getFullYear() + seperator1 + month + seperator1 + strDate
	        + " " + "23" + seperator2 + "59"
	        + seperator2 + "59";
		return currentdate2;
	}
	
	$("#startTime").val(startDate());
	$("#endTime").val(endDate());
	
2.bootstrap typeahead
官网:https://getbootstrap.com

下载examples(模块栏>examples)
	https://getbootstrap.com/docs/4.1/examples/
	
模糊匹配autocomplete
	下载:
		https://github.com/bassjobsen/Bootstrap-3-Typeahead

	包引入
		<script type="text/javascript" th:src="/js/bootstrap/bootstrap3-typeahead.min.js"></script>

	html页面
		<input id="senderSreach" type="text" data-provide="typeahead" />

	js调用
		$("#senderSreach").typeahead({
			minLength:1,
			items:8,
			source: function (query, process) {
				return $.ajax({
					url: '/order/querContact',
					type: 'post',
					data: {postData: query,paramType:'寄件'},
					success: function (result) {
						//要去掉if,不然有相同值时,用户自定义输入时会输入不进
						//if(result.length){
							process(result);
						//}
					}
				});
			},updater: function (item) {
				//这里一定要return，否则选中不显示，外加调用display的时候null reference错误。
				return item;
			},displayText: function (item) {//返回显示字符串
				//alert('displayText:'+item);
				return "选: " + item.result;
			},afterSelect: function (item) {//选择项之后的事件 ，item是当前选中的。
				$("#contactProvSender").val(item.contactProv);

				
			}
		});
3.省市区三级联动
https://www.sunzhongwei.com/js-provinces-three-level-linkage-selection-component
jquery plugs
http://fengyuanchen.github.io/distpicker/

4.列表组件
https://github.com/wenzhixin/bootstrap-table
https://examples.bootstrap-table.com/

https://www.cnblogs.com/ziyoublog/p/9055790.html
http://www.cnblogs.com/landeanfen/p/4976838.html
```
### web打印组件LODOP：
```
	下载
		http://www.lodop.net/
		http://www.lodop.net/download.html
		技术手册：Lodop6.2NoteBook20170904.zip
		驱动: Lodop6.223_Clodop3.046.zip
	网上实例
		https://blog.csdn.net/dsl815721035/article/details/54670565
	安装(解压Lodop6.223_Clodop3.046.zip)：
		1.首先安装CLodop_Setup_for_Win32NT.exe(必须)
		2.根据系统位数安装install_lodop32/64.exe(web打印组件)
		
	web页面引用
		<script type="text/javascript" src="LodopFuncs.js"></script>

	js调用打印
		var LODOP=getLodop(document.getElementById('LODOP_OB'),document.getElementById('LODOP_EM'));
		LODOP.PRINT_INITA(0,0,374,682,"二联单");
		//参数Top,Left,Width,Height,content
		LODOP.ADD_PRINT_TEXT("28.5mm","31.99mm","45.27mm","mm","hello word!");
		
		//图片
	    //LODOP.ADD_PRINT_IMAGE("15mm","58.5mm","10mm","10mm","<img src=\"../../images/bj.png\"/>");
		//二维码
		//LODOP.ADD_PRINT_BARCODE('94mm','78mm',"16mm","16mm","QRCode","签收人信息,二维码");
		//条形码
		//LODOP.ADD_PRINT_BARCODE("15mm","18mm","35mm","12mm","128Auto","210901");
		
		LODOP.PRINT_DESIGN();//设计器
		LODOP.PREVIEW();//预览
		LODOP.PRINT();//打印

	水印打印
		function addWaterMark(code,top,left,width,height) {
			  var c = document.createElement('canvas');
			  c.top = top;
			  c.left = left;
			  c.width = width;
			  c.height = height;

			  var ctx = c.getContext('2d');
			  ctx.fillStyle = 'white';
			  ctx.fillRect(0, 0, c.width, c.height);

			  ctx.font = 'normal normal 800 120px arial';
			  ctx.textAlign = 'center';
			  ctx.textBaseline = 'middle';
			  ctx.fillStyle = '#888';
			  ctx.fillText(code, c.width / 2, c.height / 2);
			  return c.toDataURL('image/png');
		}
		
		//打印水印，先打印水印，否则会将其他内容覆盖
		var strBASE64Code = addWaterMark(twoCode,206,0,378,106);
		LODOP.ADD_PRINT_IMAGE("54.36mm","0mm","100mm","28mm", strBASE64Code);
	打印机选择
		<select id="PrinterList"></select>
		//打印机读取
		function CreatePrinterList(){
			if (document.getElementById('PrinterList').innerHTML!="") return;
			var LODOP=getLodop(); 
			var iPrinterCount=LODOP.GET_PRINTER_COUNT();
			var emo=document.createElement('option');
			for(var i=0;i<iPrinterCount;i++){
				var option=document.createElement('option');
				option.innerHTML=LODOP.GET_PRINTER_NAME(i);
				option.value=i;
				document.getElementById('PrinterList').appendChild(option);
			}
		}
		//设置并打印
		var printer=$('#PrinterList').val();
		if(null == printer){
			LODOP.SET_PRINTER_INDEXA(-1);//设置默认打印机
		}else{
			LODOP.SET_PRINTER_INDEXA(printer);//设置选中打印机
		}
		LODOP.PRINT();
	
	条形码与二维码
		https://blog.csdn.net/arrowzz/article/details/80656510
			条形码
			https://github.com/lindell/JsBarcode
			使用
			JsBarcode("#barcode"+indNum, waybillNo, {
				 // format: "pharmacode",
				  width:2,
				  height:26,
				  lineColor:"#444",
				  displayValue: true
				});
			二维码
			https://github.com/davidshimjs/qrcodejs
	
问题:
    需要设置系统默认打印机
----------------------------------------------------------------------------------------------------
7.spring boot thymeleaf问题坑
	页面js运算符问题
		"&&"等特殊运算符出现在页面会出现问题
		对html标签要求会非常严,比如标签结尾必须有"/"结束节点
		
	pom.xml配置问题(编译时会进行二进制转码(乱码问题),指定哪些文件不进行转码)
		<plugins>
			   <plugin>
					<groupId>org.apache.maven.plugins</groupId>
					<artifactId>maven-resources-plugin</artifactId>
					<configuration>
						<nonFilteredFileExtensions>
							<!-- xls进行转码 -->
							<nonFilteredFileExtension>xls</nonFilteredFileExtension>
						</nonFilteredFileExtensions>
					</configuration>
				</plugin>
		<plugins/>
```
### cookie操作:
```
1.cookie JS 操作
	//写入ck
	function setCookie(name,value){
		var Days = 30;
		var exp = new Date();
		exp.setTime(exp.getTime() + Days*24*60*60*1000);
		document.cookie = name + "="+ escape (value) + ";expires=" + exp.toGMTString();
	}
	//读取ck
	function getCookie(name){
		var arr,reg=new RegExp("(^| )"+name+"=([^;]*)(;|$)");
		if(arr=document.cookie.match(reg))
		return unescape(arr[2]);
		else
		return null;
	}
	//删除ck
	function delCookie(name){
		var exp = new Date();
		exp.setTime(exp.getTime() - 1);
		var cval=getCookie(name);
		if(cval!=null)
		document.cookie= name + "="+cval+";expires="+exp.toGMTString();
	}

13.cookie localstorage(sessionStorage) indexedDB
js客户端存储
```
### mermaidjs（广泛集成于Markdown中）
```
官网：https://mermaidjs.github.io/
Github 项目地址：https://github.com/knsv/mermaid
参考：https://blog.csdn.net/fenghuizhidao/article/details/79440583
```