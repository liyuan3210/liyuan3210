# maven常用操作

一.常用命令
二.上传jar至本地/中央仓库
三.私服创建

maven视频教程:

https://www.bilibili.com/video/BV12q4y147e4

### 一.常用命令

```

	前提:
		安装jdk,并配置JAVA_HOME环境变量
	1.官网:http://maven.apache.org
	2.下载并解压:apache-maven-xxx-bin.zip
	3.环境变量配置
		M2_HOME:D:\soft\apache-maven-3.5.0
		PATH:%M2_HOME%\bin
	4.验证安装:mvn -v

	3.创建maven骨架(maven项目位置)
		mvn archetype:generate（如果是maven3简单的运行如下）
		或(如果是maven2最好运行如下)
		mvn org.apache.maven.plugins:maven-archetype-plugin:2.0-alpha-5:generate

	4.命令：
		mvn编译：mvn clean compile
		mvn测试：mvn clean test
		mvn打包运行：mvn clean install
		mnv跳过测试打包: mvnw clean install -DskipTests

		java命令行运行jar:
		java -cp hello-1.0-SNAPSHOT.jar hello.App

	java命令编译源码,运行class文件:
		Hello.java包结构
			com.liyuan
			lib(依赖jar目录)>fastjson-1.2.43.jar
		编译:
			源码目录下面运行
			javac -cp .;lib/fastjson-1.2.43.jar Hello.java
			或
			javac -classpath .;lib/fastjson-1.2.43.jar Hello.java

		运行:
			新建包结构目录层级
			com>liyuan>Hello.class(把编译好的class放在这个目录下)
			lib(依赖jar目录)>fastjson-1.2.43.jar

			java -cp .;lib/fastjson-1.2.43.jar com.liyuan.Hello

		多个jar
			javac -cp .;lib/*
	

Maven依赖的Scope去除部署不需要的jar包(打包)
	<dependency>
	< groupId>javax.servlet</groupId>
	< artifactId>jsp-api</artifactId>
	< version>2.0</version>
	< scope>provided</scope>
	< /dependency>
	compile 默认的scope，表示  dependency 都可以在生命周期中使用。而且，这些dependencies 会传递到依赖的项目中。适用于所有阶段，会随着项目一起发布 
	provided 跟compile相似，但是表明了dependency 由JDK或者容器提供，例如Servlet  AP和一些Java EE APIs。这个scope 只能作用在编译和测试时，同时没有传递性。         
	runtime 表示dependency不作用在编译时，但会作用在运行和测试时，如JDBC驱动，适用运行和测试阶段。 
	test 表示dependency作用在测试时，不作用在运行时。  只1.下载maven
		http://maven.apache.org/

	2.windows安装maven
		解压包后配置环境变量
		M2_HOME:D:\soft\apache-maven-3.5.0
		PATH:%M2_HOME%\bin
		注意(需要配置JAVA_HOME环境变量,不然mvn会报错的)

		查看maven版本:mvn -v

	3.创建maven骨架(maven项目位置)
		mvn archetype:generate（如果是maven3简单的运行如下）
		或(如果是maven2最好运行如下)
		mvn org.apache.maven.plugins:maven-archetype-plugin:2.0-alpha-5:generate

	4.命令：
		mvn编译：mvn clean compile
		mvn测试：mvn clean test
		mvn打包运行：mvn clean install
		mnv跳过测试打包: mvnw clean install -DskipTests

		java命令行运行jar:
		java -cp hello-1.0-SNAPSHOT.jar hello.App

	java命令编译源码,运行class文件:
		Hello.java包结构
			com.liyuan
			lib(依赖jar目录)>fastjson-1.2.43.jar
		编译:
			源码目录下面运行
			javac -cp .;lib/fastjson-1.2.43.jar Hello.java
			或
			javac -classpath .;lib/fastjson-1.2.43.jar Hello.java

		运行:
			新建包结构目录层级
			com>liyuan>Hello.class(把编译好的class放在这个目录下)
			lib(依赖jar目录)>fastjson-1.2.43.jar

			java -cp .;lib/fastjson-1.2.43.jar com.liyuan.Hello

		多个jar
			javac -cp .;lib/*
	

Maven依赖的Scope去除部署不需要的jar包(打包)
	<dependency>
	< groupId>javax.servlet</groupId>
	< artifactId>jsp-api</artifactId>
	< version>2.0</version>
	< scope>provided</scope>
	< /dependency>
	compile 默认的scope，表示  dependency 都可以在生命周期中使用。而且，这些dependencies 会传递到依赖的项目中。适用于所有阶段，会随着项目一起发布 
	provided 跟compile相似，但是表明了dependency 由JDK或者容器提供，例如Servlet  AP和一些Java EE APIs。这个scope 只能作用在编译和测试时，同时没有传递性。         
	runtime 表示dependency不作用在编译时，但会作用在运行和测试时，如JDBC驱动，适用运行和测试阶段。 
	test 表示dependency作用在测试时，不作用在运行时。  只在测试时使用，用于编译和运行测试代码。不会随项目发布。 
	system 跟provided  相似，但是在系统中要以外部JAR包的形式提供，maven不会在repository查找它。

jar添加到本地仓库
	pom.xml配置:
	<dependency>
		<groupId>com.yunda.util.LogUlp</groupId>
		<artifactId>LogUlp</artifactId>
		<version>1.0</version>
	</dependency>

	命令及属性:
	mvn install:install-file
	-Dfile= jar文件所存放的地址
	-DgroupId= jar文件所属的group：包名
	-DartifactId=  jar的项目名 名称，一般就是去掉后缀的文件名   
	-Dversion=版本号
	-Dpackaging=jar：此包的打包形式，就是jar
	-DgeneratePom=true

	添加到本地库:
	mvn install:install-file -Dfile=D:/lib/LogUlp_jakson2.2.3.jar -DgroupId=com.yunda.util.LogUlp -DartifactId=LogUlp -Dversion=1.0 -Dpackaging=jar -DgeneratePom=true -DcreateChecksum=true

	mvn install:install-file -Dfile=D:/lib/ojdbc14.jar -DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=14 -Dpackaging=jar -DgeneratePom=true -DcreateChecksum=true

	直接引用jar
	<dependency>
		<groupId>openapi-sdk-6.0</groupId>
		<artifactId>openapi-sdk-6.0</artifactId>
		<version>6.0</version>
		<scope>system</scope>
		<systemPath>${project.basedir}/src/lib/openapi-sdk-6.0.jar</systemPath>
	</dependency>

pom.xml项目引用依赖:
	创建util jar 项目,配置如下:
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.liyuan3210</groupId>
	<artifactId>liyuan3210-util</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>liyuan3210-util</name>
	<url>http://maven.apache.org</url>

	<properties>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>

	↓

	mvn install 				生成jar包
	mvn install:install-file	安装到本地或者私有仓库

	↓

	项目引用
	<dependency>
		 <groupId>com.liyuan3210</groupId>
		 <artifactId>liyuan3210-util</artifactId>
		 <version>0.0.1</version>
	</dependency>


	pom.xml配置modules属性:
	?

xml打包进去:
	<!-- 打包包含xml -->
	<resources>
		<resource>  
			<directory>src/main/java</directory>  
			<includes>  
				<include>**/*.xml</include>  
			</includes>  
			<filtering>true</filtering>  
		</resource>
	</resources>

maven教程
http://www.yiibai.com/maven/在测试时使用，用于编译和运行测试代码。不会随项目发布。 
	system 跟provided  相似，但是在系统中要以外部JAR包的形式提供，maven不会在repository查找它。

jar添加到本地仓库
	pom.xml配置:
	<dependency>
		<groupId>com.yunda.util.LogUlp</groupId>
		<artifactId>LogUlp</artifactId>
		<version>1.0</version>
	</dependency>

	命令及属性:
	mvn install:install-file
	-Dfile= jar文件所存放的地址
	-DgroupId= jar文件所属的group：包名
	-DartifactId=  jar的项目名 名称，一般就是去掉后缀的文件名   
	-Dversion=版本号
	-Dpackaging=jar：此包的打包形式，就是jar
	-DgeneratePom=true

	添加到本地库:
	mvn install:install-file -Dfile=D:/lib/LogUlp_jakson2.2.3.jar -DgroupId=com.yunda.util.LogUlp -DartifactId=LogUlp -Dversion=1.0 -Dpackaging=jar -DgeneratePom=true -DcreateChecksum=true

	mvn install:install-file -Dfile=D:/lib/ojdbc14.jar -DgroupId=com.oracle -DartifactId=ojdbc14 -Dversion=14 -Dpackaging=jar -DgeneratePom=true -DcreateChecksum=true

	直接引用jar
	<dependency>
		<groupId>openapi-sdk-6.0</groupId>
		<artifactId>openapi-sdk-6.0</artifactId>
		<version>6.0</version>
		<scope>system</scope>
		<systemPath>${project.basedir}/src/lib/openapi-sdk-6.0.jar</systemPath>
	</dependency>

pom.xml项目引用依赖:
	创建util jar 项目,配置如下:
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.liyuan3210</groupId>
	<artifactId>liyuan3210-util</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>liyuan3210-util</name>
	<url>http://maven.apache.org</url>

	<properties>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	</properties>

	↓

	mvn install 				生成jar包
	mvn install:install-file	安装到本地或者私有仓库

	↓

	项目引用
	<dependency>
		 <groupId>com.liyuan3210</groupId>
		 <artifactId>liyuan3210-util</artifactId>
		 <version>0.0.1</version>
	</dependency>


	pom.xml配置modules属性:
	?

xml打包进去:
	<!-- 打包包含xml -->
	<resources>
		<resource>  
			<directory>src/main/java</directory>  
			<includes>  
				<include>**/*.xml</include>  
			</includes>  
			<filtering>true</filtering>  
		</resource>
	</resources>

maven教程
http://www.yiibai.com/maven/
```
###### other

```
原理
仓库四种类型
	1.group(仓库组)
	2.hosted(宿主)
	3.proxy(代理)
	4.virtual(虚拟)？
	
概念:
	group public :	多个仓库聚合,nexus表示对外提供统一的地址
	hosted : 用户可以把自己的第三方jar发布deploy到私有仓库
	proxy  ：远程仓库代理

上传到私有仓库
https://blog.yoodb.com/yoodb/article/detail/1313
	
发布注意的问题	
https://www.cnblogs.com/yucy/p/7509561.html

根据版本号选择发布正式与快照
https://www.cnblogs.com/ctxsdhy/p/6105805.html

长传本地仓库
https://www.cnblogs.com/ctxsdhy/p/6105805.html

上传Jar方法
https://www.cnblogs.com/javastack/p/8776302.html

eclipse上传jar
https://blog.csdn.net/zhou_xtao/article/details/72833959
----------------------------------------------------------------------------------------------------
base_java\thymeleaf\branches\thymeleaf-bootstrap	//可以打包成功
openjdk mvn install 问题
[WARNING] Error injecting: org.codehaus.plexus.archiver.jar.JarArchiver

pom.xml添加
<build>
	<plugins>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-jar-plugin</artifactId>
			<version>2.5</version>
			<!-- <version>3.1.0</version> -->
		</plugin>
	</plugins>
</build>
```

### 二.上传jar至本地/中央仓库
######  1.上传到本地仓库
```
1.settings.xml配置用户名和密码(上传内部releases版本,内部/个人快照版本,根据使用情况应该分两个账号)
	一定要是C:\Users\用户名\.m2\settings.xml下面的settings.xml
	<server>  
		<id>releases</id>  
		<username>admin</username>  
		<password>admin123</password>  
	</server>
	<server>  
		<id>snapshots</id>  
		<username>admin</username>  
		<password>admin123</password>  
	</server>

	2.pom.xml文件配置(id对应上面配置的账号,url根据nexus仓库地址配置)
	maven2会根据模块的版本号(pom文件中的version)中是否带有-SNAPSHOT来判断是快照版本还是正式版本
	<distributionManagement>
		<repository>  
			<id>releases</id>  
			<url>http://192.168.5.15:8081/repository/maven-public/</url>  
		</repository>
		<snapshotRepository>
			<id>snapshots</id>
			<url>http://192.168.5.15:8081/repository/maven-snapshots/</url>
		</snapshotRepository>
	</distributionManagement>

	打包测试验证
	mvn clean install
	
	deploy(发布)
	运行命令mvn -Dmaven.test.skip=true deploy
	mvn clean deploy -P release -Dmaven.test.skip=true


单个jar上传(貌似不行)
	mvn deploy:deploy-file -DgroupId=com -DartifactId=eureka_server -Dversion=0.0.1 -Dpackaging=jar -Dfile=D:/eureka_server-0.0.1-releases.jar -Durl=http://192.168.5.15:8081/repository/liyuan_hosted/ -DrepositoryId=liyuan_hosted

指定本地目录(settings.xml)
	settings节点下面
	<localRepository>D:\soft\dev\mavenRepository</localRepository>

指定私有仓库settings.xml(指定从私服下载,下载后jar的会在私服缓存)
	settings>mirrors节点下面
	<mirror>
		<id>nexus</id>
		<mirrorOf>*</mirrorOf>
		<url>http://10.1.193.100:8081/nexus/content/groups/public/</url>
	</mirror>
```

###### 2.上传到中央仓库
```
实例
	https://www.sojson.com/blog/250.html
	
	默认中央仓库
	https://www.cnblogs.com/EasonJim/p/6784328.html
	
	Hudson插件持续集成
	
	上传到中央仓库

申请中央仓库	
	· maven中央仓库
	https://issues.sonatype.org/
	sonatype帐号登录
	申请流程
	1.创建好账号登录进去后,点击create创建一个工单
	project:选择Community Support-Open...
	Issue Type:New Project
	Group id:现有域名com.liyuan3210 或 (io.github.liyuan3210 或 com.github.liyuan3210)
	Project URL: https://github.com/liyuan3210/base_java
	SCM url: https://github.com/liyuan3210/base_java.git
	
	2.创建后Group id 分两种情况
	  自己域名
	   通过邮件提示配置TXT记录 如
		@			OSSRH-51896
		配置好后坐等成功邮件通知(通常)
	  github地址
	     需要根据邮件创建一个OSSRH-51896仓库
		 然后坐等成功邮件通知
	
申请成功邮件回复如下
	com.github.liyuan3210 has been prepared, now user(s) liyuan3210 can:
	* Deploy snapshot artifacts into repository https://oss.sonatype.org/content/repositories/snapshots
	* Deploy release artifacts into the staging repository https://oss.sonatype.org/service/local/staging/deploy/maven2
	* Promote staged artifacts into repository 'Releases'
	* Download snapshot and release artifacts from group https://oss.sonatype.org/content/groups/public
	* Download snapshot, release and staged artifacts from staging group https://oss.sonatype.org/content/groups/staging

	please comment on this ticket when you promoted your first release, thanks
	
	· 仓库查看地址(sonatype帐号登录)
	https://oss.sonatype.org
	
	上传方法
	https://www.jianshu.com/p/4105e072a259
	https://blog.csdn.net/oufua/article/details/81506254
	https://blog.csdn.net/anumbrella/article/details/78887195
	
	GitHub 开源代码库的便捷发布渠道
	https://jitpack.io/
	实例介绍
	https://blog.csdn.net/weixin_33724046/article/details/91439563
	
	中央仓库发布实例见
	https://github.com/liyuan3210/project/util2
```

###  三.maven私服搭建

```
实例
	https://blog.csdn.net/rickyit/article/details/54927101

	官网下载
	https://www.sonatype.com/download-oss-sonatype

	配置环境变量
	NEXUS_HOME:C:\soft\nexus-3.9.0-01-win64\nexus-3.9.0-01
	Path:;%NEXUS_HOME%\bin

	setx NEXUS_HOME "C:\soft\nexus-3.9.0-01-win64\nexus-3.9.0-01"
	setx PATH "%PATH%;%NEXUS_HOME%\bin"

	直接运行服务：
	nexus.exe /run

	安装服务
	nexus.exe /install Nexus

	浏览器访问
	http://127.0.0.1:8081/

	默认账号/密码
	admin/admin123

linux下安装
	https://www.jianshu.com/p/2dc35820c523
	
	配置java,nexus环境变量
	
	启动命令
	nexus start
	
	编辑添加如下内容(vi /lib/systemd/system/nexus.service):
	// 添加如下内容
	[Unit]
	Description=Nexus Service
	After=network.target

	[Service]
	Type=forking
	ExecStart=/opt/nexus/nexus-2.14.2-01/bin/nexus start
	ExecReload=/opt/nexus/nexus-2.14.2-01/bin/nexus restart
	ExecStop=/opt/nexus/nexus-2.14.2-01/bin/nexus  stop
	PrivateTmp=true

	[Install]
	WantedBy=multi-user.target
	
	配置nexus.service文件权限
	# chomd 754 nexus.service
	
	设置开机自启动
	# systemctl enable nginx.service
	
	如果是使用root用户启动nexus，请添加系统环境变量：
	RUN_AS_USER=root
```