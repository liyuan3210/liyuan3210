# java_toDo
[tomcat_jetty](#tomcat_jetty)

```
java 编译与执行
https://blog.csdn.net/dzning123/article/details/80561934

一.jwt(JSON Web Token)
https://www.cnblogs.com/cjsblog/p/9277677.html
http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html
https://www.jianshu.com/p/e88d3f8151db
https://www.jianshu.com/p/34512980a53e
JWT官网： https://jwt.io/

二.GraphQL(REST API)接口标准
https://www.cnblogs.com/Wolfmanlq/p/9094418.html
https://www.bilibili.com/video/av53645573	//视频
https://www.bilibili.com/video/av53644785/?p=13


三.不再支持源选项 6。请使用 7 或更高版本。
https://blog.csdn.net/moshowgame/article/details/81369327
https://www.cnblogs.com/0820LL/p/10586593.html
 <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <java.version>1.8</java.version>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
  </properties>
  
四.jwt(JSON Web Token)
https://www.cnblogs.com/cjsblog/p/9277677.html
http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html
https://www.jianshu.com/p/e88d3f8151db
https://www.jianshu.com/p/34512980a53e
JWT官网： https://jwt.io/

五.GraphQL(REST API)接口标准
https://www.cnblogs.com/Wolfmanlq/p/9094418.html
https://www.bilibili.com/video/av53645573	//视频
https://www.bilibili.com/video/av53644785/?p=13
  
  
六.java进程与线程区别,java rmi
https://blog.csdn.net/weixin_41890097/article/details/82026820
java进程
https://segmentfault.com/q/1010000007641124?_ea=1413596
java进程之间通信（rmi）
https://www.iteye.com/blog/javasam-1848840

七.logback+ELK日志系统
https://www.cnblogs.com/wangzhuxing/p/9665905.html
https://www.jianshu.com/p/b3dedb8fb61e			//logback
https://www.cnblogs.com/free-java/p/7532371.html
```

# cors_lombok_security

```
一.cors跨域问题解决
	跨域介绍cors
	https://www.cnblogs.com/sloong/p/cors.html

	spring boot 跨域问题解决
	https://blog.csdn.net/Lammonpeter/article/details/78602732

	cors项目地址
	https://github.com/ebay/cors-filter

	web.xml配置

	以spring boot为例介绍
	1.内部类WebMvcConfigurerAdapter
	@Configuration
	public class CustomCorsConfiguration {

		@Bean
		public WebMvcConfigurer corsConfigurer() {

			return new WebMvcConfigurerAdapter() {
				@Override
				public void addCorsMappings(CorsRegistry registry) {
					// 设置了可以被跨域访问的路径和可以被哪些主机跨域访问
					registry.addMapping("/api/**").allowedOrigins("http://localhost:8081", "http://localhost:8082");
				}
			};
		}
	}

	2.集成WebMvcConfigurerAdapter
	@Configuration    
	public class CorsConfig extends WebMvcConfigurerAdapter {    
	  
		@Override    
		public void addCorsMappings(CorsRegistry registry) {    
			registry.addMapping("/**")    
					.allowedOrigins("*")    
					.allowCredentials(true)    
					.allowedMethods("GET", "POST", "DELETE", "PUT")    
					.maxAge(3600);    
		}    
	  
	} 

	3.标签@CrossOrigin(有权限访问的主机ip:port)
	@CrossOrigin({"http://localhost:8081", "http://localhost:8082"})
	@RequestMapping("/get")
	public Map<String, Object> get(@RequestParam String name) {
		Map<String, Object> map = new HashMap<>();
		map.put("title", "hello world");
		map.put("name", name);
		return map;
	}


	测试cors_test.html
	<!DOCTYPE html>
	<html lang="en">
	<head>
		<meta charset="UTF-8">
		<title>cors test page</title>
		<script src="jquery.min.js"></script>
		<script>
			function loadData(){
				$.ajax({
					url: "http://192.168.10.61:8080/api",
					type:"GET",
					dataType:"json",
					timeout:10000,
					success:function(data){
						$("#result").append(data.result+"<br />");
						console.log(data);
					},
					error:function(e){
						$("#result").append(e.statusText+"<br />");
					}
				});
			}
			$(function(){
				$("#host").append("origin:"+window.location.origin);
			});
		</script>
	</head>
	<body>
	<button onclick="loadData()">onclick</button>
	<div id="host"></div>
	<div id="result" style="height:200px;width:100%"></div>
	</body>
	</html>
	
二.lombok
	lombok的作用：
	在开发过程中，通常都会定义大量的JavaBean，然后通过IDE去生成其属性的构造器、getter、setter、equals、hashcode、toString方法，当要对某个属性进行改变时，比如命名、类型等，都需要重新去生成上面提到的这些方法。 lombok的产生就是为了省去我们手动创建上述方法的麻烦，它能够在我们编译源码的时候自动帮我们生成上述方法。即它最终能够达到的效果是：在源码中没有上述方法，但是在编译生成的字节码文件中有上述方法 。

使用：
    下载地址：
    https://projectlombok.org/downloads/lombok.jar

    如果用的是eclipse,将 lombok.jar 复制到 myeclipse.ini / eclipse.ini 所在的文件夹目录下。
    在ini文件下最后面插入以下两行并保存：
    -Xbootclasspath/a:lombok.jar
    -javaagent:lombok.jar
    或者
    执行java -jar lombok.jar,在弹出界面选择eclipse安装目录（选中文件），安装后eclipse.ini文件后面会追加配置

    然后重启 eclipse / myeclipse

    lombok工程pom
    <dependency>
     <groupId>org.projectlombok</groupId>
     <artifactId>lombok</artifactId>
     <version>1.16.18</version>
     <scope>provided</scope>
    </dependency>

	lombok的罪恶
	使用lombok虽然能够省去手动创建setter和getter方法的麻烦，但是却大大降低了源代码文件的可读性和完整性，降低了阅读源代码的舒适度。 另外lombok无法支持多种参数构造器的重载。
	
三.security
get与post区别
	1.请求方式:
	POST
	参数主要放在包体,理论上没有大小限制(也可以通过url传参)

	GET
	参数通过url传参,理论上http协议没有做大小限制,但IE对url长度限制是2083字节
	 
	HEAD
	OPTIONS
	PUT
	DELETE
	TRACE
	CONNECT

session,cookie,token区别

	1.session
	session保存在服务器上,应用重启会导致session丢失

	2.cookie
	cookie保存在浏览器客户端(可以设置浏览器属性设置cookie有效期)

	3.token
	用户输入用户名密码
	↓
	服务端验证成功
	↓
	返回到客户端token令牌(可以设置token有效期)
	↓
	后续业务接口都需要带着token令牌进行权限验证


加密解密方法
	http://blog.csdn.net/pengzp/article/details/6556674

	1.加解密种类:
	对称(加密,解密使用相同的秘钥)
		DES
		3DES
		AES
		
	非对称(有公钥,私钥。公钥加密,私钥解密)
		RSA
		DSA
		ECC
		
	散列算法(不可逆的加密,通常用着签名及验证信息是否被修改)
		MD5
		SHA
```

# spring_framework

```
1.spring-framework
2.spring boot
3.spring mobile
4.spring cloud

使用spring Cloud和Docker构建微服务
http://www.open-open.com/lib/view/open1437363835818.html

dbcp连接池配置
http://blog.csdn.net/fairyhawk/article/details/7565391

tomcat https

mysql ssl

                               spring mobile
http://blog.sina.com.cn/s/blog_c19a6fe10102vo5d.html

----------------------------------------------------------------------------------------------------
                                    spring-framework
Download:

https://spring.io/
>
PROJECTS
>
spring-framework
>
DOCUMENTATION>Reference
>
Distribution Zip Files
----------------------------------------------------------------------------------------------------
Jar:
spring-framework-4.2.6.RELEASE
no need:
spring-jms-4.2.6.RELEASE.jar
spring-test-4.2.6.RELEASE.jar
spring-websocket-4.2.6.RELEASE.jar

other jar
//spring log
1.commons-logging-1.2
//jstl
2.jstl-1.2
//DataSource dbcp
3.commons-dbcp-1.4
4.commons-pool-1.6
//database connector
5.mysql-connector-java-5.1.39-bin
//TransactionManager
6.aopalliance-1.0
7.aspectjweaver-1.6.12(eclipse>project>AspectJ)
//json(http://wiki.fasterxml.com/)
8.jackson-annotations-2.8.0-20160525.165210-50.jar
9.jackson-databind-2.8.0-20160528.014108-307.jar
10.jackson-core-2.8.0-20160529.041810-165.jar
//servlet-api
11.servlet-api.jar
//log4j
12.log4j-1.2.17.jar
----------------------------------------------------------------------------------------------------
spring源码阅读分析
https://www.bilibili.com/video/av56715741?from=search&seid=11244654249485019531
Spring源码分析

1.spring源码构建（gradle）
2.spring环境初始化(创建一个gradle项目spring测试项目)
3.spring中间插件(新建自己的spring项目插件)
4.bean工厂原理

正常流程
	AnnotationConfigApplicationContext	//标签
	ClassPathXmlApplicationContext		//老版本xml
	上面功能相同
	
	DefaultListableBeanFactor		//bean工厂实现

	GenericBeanDefinition			//描述bean类型的对象

	map<string, GenericBeanDefinition>	//存储所有bean对象

	preInstantiateSingletons		//实例对象

容器自定义（干预容器生产）,修改类名验证
	BeanFactoryPostProssessor(List)
----------------------------------------------------------------------------------------------------
Js:
jquery-ui-1.11.4(jquery ui core)
Mottie-tablesorter-v2.17.8-0(tablesorter)
zTree_v3-3.5.23(zTree)
jquery-validation1.16.0(from)
kindeditor

jdGrid使用
http://www.360doc.com/content/14/0310/09/16002580_359201920.shtml
很好的文本编辑器
http://kindeditor.net/demo.php
```

# spring_web

```
                                       maven创建web
分两种：使用骨架,不使用骨架
一.使用骨架
1.使用eclipse创建maven项目,(不要选上Create a simple project)

2.选择maven-archetype-webapp

3.右键项目new>Source Folder(如果发现右键项目properties>java Build Path>Source有报错，先删除然后再创建如下目录)
src/main/resources
src/main/java
src/test/resources *
src/test/java *

4.右键项目properties>java Build Path>Source修改输出路径4个Source 下面Output folder路径应该为
src/main/resources    >    target/classes
src/main/java    >    target/classes
src/test/resources    >    target/test-classes *
src/test/java    >    target/test-classes *

5.右键项目properties>project facets
点击Convert to faceted form...,并勾上Dynamic web Module点击ok,
java项也得保证勾上(如果已经选上，去掉后点击ok再打开此界面选上点击ok)
>
如果出现Further configuration availabe点击配置如下
Content directory:/src/main/webapp

6.properties>Deployment Assembly
remove:删除WebContent目录，与所有test目录
add:重新指定web路径，选择webapp，点击finish
add:Java Build Path Entries 然后next,再Maven Dependencies最后哦点击Finish

7.WebContent里面两个目录剪切到src/main/webapp目录下面,并删掉WebContent

8.基于web项目创建完毕，配置pom.xml


二.不使用骨架
1.使用eclipse创建maven项目

2.勾上Create a simple project(不使用骨架)

3.填好项目名称并选择packaging:war

4.右键项目properties>project facets
点击Convert to faceted form...,并勾上Dynamic web Module点击ok,
java项也得保证勾上(如果已经选上，去掉后点击ok再打开此界面选上点击ok)
接下来会多出WebContent目录
>
如果出现Further configuration availabe点击配置如下
Content directory:/src/main/webapp


5.WebContent里面两个目录剪切到src/main/webapp目录下面,并删掉WebContent

6.properties>project facets>Deployment Assembly
remove:删除WebContent目录，与所有test目录
add:重新指定web路径，选择webapp，点击finish
add:Java Build Path Entries 然后next,再Maven Dependencies最后哦点击Finish

7.最后基于maven的webapp就建立好了，最后配置pom.xml文件
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-core</artifactId>
        <version>3.0.7.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
        <version>3.0.7.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-webmvc</artifactId>
        <version>3.0.7.RELEASE</version>
    </dependency>
    <dependency>
        <groupId>org.codehaus.jackson</groupId>
        <artifactId>jackson-mapper-asl</artifactId>
        <version>1.7.1</version>
    </dependency>
  </dependencies>

问题：
Could not resolve archetype
方法：
window>Preferences>Maven>archetypes
选择Add Remote catalog配置http://repo1.maven.org/maven2/archetype-catalog.xml
Description:maven catalog
好了，创建maven项目时Catalog选择刚才创建的maven catalog就好了


svn导入 or 导出：
1.导入
只需导入src文件夹与pom.xml

2.导出
Project Explorer区右键选择import>SVN
>选择(做为工作空间中的项目检出)>填写检出新的项目名称>finsh
项目转换:
项目检出后，需要对项目进行转换 右键项目>Configure
>
Convert to Maven Project
(转换后如果properties>java Build Path>Source里面两个报错，需要删除。重新Add Folder引用的java源代码目录)
>
右键项目properties>project facets(如果不能添加项目到tomcat里面)>选中Dynamic web Module 与 java>(点击Further configuration availabe配置default output folder与Content directory)
default output folder:target/classes
Content directory:/src/main/webapp
>
properties>Deployment Assembly>add>Java Buid Path Entries>Maven Dependencies>Finsh

上面可能转换的有点慢,因为后台需要maven下载相关文件,下载不动时建议通过hero vpn翻墙软件,连接国外网络进行下载
```

# eclipse_mingw_gcc

```
eclipse C GCC mingw开发环境
一.eclipse java EE安装插件
1.eclipse需要安装CDT插件
    进入www.eclipse.org,到PROJECTS搜索CDT插件,下载

2.www.mingw.org下载安装mingw-get-setup.exe
    点击install，指定好MinGw安装目录,再点击Continue.稍后弹出一个
windows窗体，选中好要安装的插件，然后选择Installation>apply changes

3.配置好mingw PATH路径(mingw/bin)

4.eclipse中安装CDT插件

5.eclipse中配置好MinGw

配置管理
在安装目录../libexec/mingw-get/guimain.exe，可以管理运行函数库

二.eclipse c/c++开发包
1.新建一个项目选址c project

2.toolchains如果没有MinGw,点击一下
show project types and toolchains only if they are supported on the platform

3.确定MinGw环境
右键项目properties>c/c++ build>Environment
配置好系统环境变量(切记一定要配置PATH)
MINGW_HOME:D:\soft\mingw(eclipse只要配置好MINGW_HOME,PATH就配置好了)
PATH:D:\soft\mingw

如果pc要用命令行编译运行必须手动在pc上配置好环境变量MINGW_HOME,PATH

4.运行时需要右键build project一下项目
右键运行的文件->run as选中的mian方法文件
eclipse/c下开发 添加mysql驱动:
1.mysql官网下载c驱动(下载32位的驱动)

2.新建一个c项目工程，然后解压驱动包

3.配置c工程，右键工程>properties>C/C++ Build>Settings
    1>右边Tool settings>GCC C Compiler>Includes>
          Include paths(选中驱动包里面的Inludes目录)
    2>右边MinGw C Linker>Libraries>
           Library search path(选中驱动包里面的lib目录)
注意:上面Libraries(-l)(添加值为libmysql)

4.最后右键build project一下项目就可以了

erro
加入按照上面做了编译出现mysql could not be resolved问题，请检查eclipse配置
右键工程>properties>C/C++ General>Indexer项里面如下被选中
Enable project specific settings

Enable indexer
Index source files not included in the build
Index unused headers
Allow heuristic resolution of includes



配置：
http://blog.csdn.net/hzqnju/article/details/6416940
操作：
http://blog.csdn.net/hustspy1990/article/details/7414572


解决此类问题的方法
http://blog.csdn.net/mzlogin/article/details/5605320

正则表达式:
下载正则包
http://blog.creke.net/766.html
http://www.gnu.org/directory/regex.html
学习
http://c.biancheng.net/cpp/html/1428.html
regex学习:
http://blog.chinaunix.net/uid-479984-id-2114941.html

一次取多条数据：
http://www.cnblogs.com/qigaohua/p/5754832.html

http://bbs.csdn.net/topics/280003883


sscanf方法：
http://www.cnblogs.com/lyq105/archive/2009/11/28/1612677.html
http://blog.sina.com.cn/s/blog_4a0824490102vz6o.html

抓取网页方法：
http://blog.csdn.net/jiju8484/article/details/3427262
c实现网页类容获取并保存到文件
http://blog.csdn.net/xiahn1a/article/details/41680959
c实现http请求
http://blog.chinaunix.net/uid-579481-id-163612.html
c实现下载：
http://blog.csdn.net/xiongyangg/article/details/50767482


请求网页的三种方法：
wininet
http://www.jb51.net/article/44706.htm

c语言读取html:
http://www.oschina.net/p/streamhtmlparser
http://www.oschina.net/p/streamhtmlparser?fromerr=orKmRIIQ
http://www.open-open.com/lib/view/open1324371654843.html

libxml2使用:
http://blog.csdn.net/allen_ydc/article/details/49002455

http://blog.csdn.net/king_on/article/details/7544003

c指针与字符串:
http://blog.csdn.net/wusuopubupt/article/details/17284423


正则表达式学习:

http://www.runoob.com/regexp/regexp-tutorial.html

```

# tomcat_jetty 

<div id="tomcat_jetty"/>

tomcat
下载官网:http://tomcat.apache.org/(建议下载tar.gz包)

1>.如果下载的是windows64位则需要下载替换tcnative-1.dll
Download>tomcat Native(下载此插件)

2>.替换tomcat/bin目录下的tcnative-1.dll文件

tomcat配置(..\conf\server.xml)
修改端口:
	Server port="8005" shutdown="SHUTDOWN" > 要改的端口
	Connector port="8080" protocol="HTTP/1.1"  > 要改的端口
	Connector port="8009" protocol="AJP/1.3" > 要改的端口

修改默认访问项目(默认项目是ROOT):
	conf目录server.xml文件下Host节点添加如下:
	<Context path="" docBase="项目名称" debug="0" reloadable="true"/>

服务启动(第一次可能生效不了):
	startup
	catalina run

windows host配置
C:\Windows\System32\drivers\etc\hosts

https配置:
	1.生成秘钥文件
	keytool -genkeypair -alias "tomcat" -keyalg "RSA" -keystore "d:\tomcat.keystore"
	密码:123456
	您的名字与姓氏:通常输入域名(test.com)
	组织单位:tomcat
	组织名称:tomcat
	所在城市:shanghai
	所在省/市/自治区:shanghai
	该单位双字母国家/地区代码:cn
	[否]:y
	继续回车...

	配置tomcat8(server.xml)
	<Connector port="8443"
	  maxThreads="150" SSLEnabled="true" 
	keystoreFile="D:\tomcat.keystore"  keystorePass="123456">
	</Connector>
	
	高版本 RFC 7230 and RFC 3986 问题
	java.lang.IllegalArgumentException: Invalid character found in the request target. The valid characters are defined in RFC 7230 and RFC 3986
	
	tomcat-8.0.36以后的高的版本tomcat对url参数进行了严格的校验,需要对参数进行编码.

----------------------------------------------------------------------------------------------------
jetty
jetty官网:http://www.eclipse.org/jetty/

eclipse插件：
官网的"eclipse Tooling"链接进去下载"Run-Jetty-Run"(拖动链接到eclipse进行在线安装)
还有"eclipse Jetty"是做什么的？

进入jetty根目录
修改端口:etc/jetty.xml默认端口8080
默认war包存放位置:webapps
配置文件:start.ini

命令启动：
java -jar start.jar 或 java -jar start.jar jetty.http.port=8081

----------------------------------------------------------------------------------------------------
weblogic
1>.进入weblogic目录
cd /u02/weblogic/user_projects/domains/base_domain

2>.停止weblogic服务
cd  ../bin
./stopWebLogic.sh

3>.杀死后台进程
ps -ef|grep weblogic
kill -9 29070

4>.后台启动weblogic(base_domain目录下)
nohup ./startWebLogic.sh &
```


```