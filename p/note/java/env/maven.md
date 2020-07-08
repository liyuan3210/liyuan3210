# maven常用操作

## 1.常用命令
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
## 2.other

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

