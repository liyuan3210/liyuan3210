# js插件

```
js 组件介绍
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
----------------------------------------------------------------------------------------------------
1.时间日期
	下载
		http://www.my97.net/

	包引入
		<script type="text/javascript" src="/js/WdatePicker/WdatePicker.js"></script>

	html页面
		<input type="text" th:id="startTime" name="startTime"  onclick="WdatePicker({dateFmt:'yyyy-MM-dd HH:mm:ss'})"/>
----------------------------------------------------------------------------------------------------
2.form表单校验,ajax submit提交
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
		
----------------------------------------------------------------------------------------------------
3.web打印组件
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
4.js正则全部替换
	var r= "1\n2\n3\n";
	//将字母\n替换成分号
	alert(r.replace("\n",";"));
	结果：1;2\n3\n 只替换了第一个
	
	var r= "1\n2\n3\n";
	//将字母\n替换成分号
	alert(r.replace(/\n/g, ";"));
	结果：1;2;3; replace 的第一个参数可以是正则表达式，/g标识全文匹配。
----------------------------------------------------------------------------------------------------
5.ajax同步异步参数(让ajax执行完后再继续往下执行)
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
----------------------------------------------------------------------------------------------------
6.bootstrap时间控件
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
----------------------------------------------------------------------------------------------------
8.bootstrap typeahead
官网:https://getbootstrap.com

下载examples(模块栏>examples)
	https://getbootstrap.com/docs/4.1/examples/
	
1.模糊匹配autocomplete
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
----------------------------------------------------------------------------------------------------
9.load html
	实例：https://www.cnblogs.com/lixiaoran/p/6251397.html
	$("#div").load("test.html #content");  
	
	实例：
	$.post("url",{url:href},function(data){
		$("#span").html('');
		$("#span").html(data);
	});
----------------------------------------------------------------------------------------------------
10.cookie JS 操作
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
----------------------------------------------------------------------------------------------------
11.省市区三级联动
https://www.sunzhongwei.com/js-provinces-three-level-linkage-selection-component
jquery plugs
http://fengyuanchen.github.io/distpicker/
----------------------------------------------------------------------------------------------------
https://www.cnblogs.com/ziyoublog/p/9055790.html
http://www.cnblogs.com/landeanfen/p/4976838.html
12.列表组件
https://github.com/wenzhixin/bootstrap-table
https://examples.bootstrap-table.com/
----------------------------------------------------------------------------------------------------
13.cookie localstorage(sessionStorage) indexedDB
js客户端存储
```

