# 环境安装

[*.git使用](git_use.md)

[*.ssh免密登录及openssh](ssh_openssh.md)

[*.gpg加解密](gpg_use.md)
## 一.jdk安装

### 1.oracle jdk

oracle jdk收费,openjdk免费
oracle jdk 与 openjdk从11后保持一致
oracle jdk 8(2019年1月以后jdk8需要购买许可)后面版本需要购买许可,(JDK 8 u201 和 JDK 8 u202 仍可免费使用)
openjdk 免费版本(6个月更新一个版本)

```
官网:https://www.oracle.com/
    export JAVA_HOME=/data/soft/jre1.8.0_162
    export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
    export PATH=$JAVA_HOME/bin:$PATH
	
jdk9后的安装(不需要配置CLASSPATH与rt.jar,tools.jar)
	https://github.com/msbbigdata/javase/blob/master/note/JDK12的安装搭建.md
jlink命令生成jre:
	bin\jlink.exe --module-path jmods --add-modules java.desktop --output jre
```

### 2.open jdk

```
openjdk(开源免费):
	官网:https://www.oracle.com
	主页:http://openjdk.java.net
	https://blog.csdn.net/lanwp5302/article/details/86612197
```

### 3.groovy语言

```
官网: http://www.groovy-lang.org
前提:
	安装配置好jdk
	groovy是基于jdk的,所以要先安装配置好jdk
	配置好JAVA_HOME,path环境变量
	
1>.下载并解压apache-groovy-sdk-3.0.4.zip
配置GROOVY_HOME,path

2>验证
groovy　-v

问题：
apache-groovy-binary-3.0.4.zip　与　apache-groovy-sdk-3.0.4.zip不同？？？
```


## 二.BuildTools

### 1.maven

	前提:
		安装jdk,并配置JAVA_HOME环境变量
	1.官网:http://maven.apache.org
	2.下载并解压:apache-maven-xxx-bin.zip
	3.环境变量配置
		M2_HOME:D:\soft\apache-maven-3.5.0
		PATH:%M2_HOME%\bin
	4.验证安装:mvn -v
	
	指定阿里镜像仓库：
	https://www.cnblogs.com/feson/p/12405577.html
	添加配置(../apache-maven-3.6.3/conf/settings.xml)：
	 <mirror>
	     <id>nexus-aliyun</id>
	     <mirrorOf>central</mirrorOf>
	     <name>Nexus aliyun</name>
	     <url>http://maven.aliyun.com/nexus/content/groups/public</url> 
	 </mirror>
	指定仓库存储位置:
	<settings>
		<localRepository>D:\soft\dev\maven-repository</localRepository>
	</settings>
[maven常用操作](maven.md)

[上传jar至本地/中央仓库](maven-upload-center.md)

[私服创建](maven-server.md)

### 2.gradle

官网 ：https://gradle.org

下载二进制包：https://services.gradle.org/distributions/gradle-6.5.1-bin.zip

解压并配置环境变量：

> GRADLE_HOME=D:\soft\dev\gradle-5.6.3 	
>
> PATH=%GRADLE_HOME%\bin

使用自定义缓存目录(默认在当前用户.gradle下.也可以是maven本地仓库地址,保证M2_HOME环境变量指定,也是maven安装目录),

**环境变量配置(非必)：**

GRADLE_USER_HOME=/home/liyuan/.gradle

>项目配置build.gradle添加mavenLocal()(先从maven本地仓库mavenLocal()找,如找不到再从中央仓库mavenCentral() 找) 	
>
>	repositories {
>		mavenLocal()
>		maven { url'http://maven.aliyun.com/nexus/content/groups/public/' }	//阿里仓库代理
>		jcenter()
>	}



验证安装：

> gradle -v

[gradle使用](gradle.md)



gradle user home作用？？？

https://www.yisu.com/zixun/309965.html



**GRADLE_USER_HOME(环境变量,命令行gradlew目录，与gradle命令是不同的)：**

指定Gradle用户主目录位置，即.gradle目录位置。

**Gradle user home(eclipse与idea都有自己的Gradle user home)：**



**gradle与gradlew不同版本问题解决(idea工具)：**

idea里面配置Gradle->Use Gradle from->specified location->gradle安装目录

eclipse里面要配置好(Local installation directory与Gradle user home)



## 三.IDE

### 1.eclipse

https://www.eclipse.org/

```
Eclipse IDE for Java EE Developers : java web开发
Eclipse IDE for C/C++ Developers : c/c++ gcc环境
```

**eclipse导入项目（java常规项目，web项目...）:**

现有web项目从git克隆下来后只有src,WebContent目录（或src目录），这样直接从eclipse导入项目时,是识别不了导不进去的。

下面两种方法可以导入项目到eclipse。
方法1.在eclipse上新建一个web工程,然后把工程里面.classpath与.project文件拷贝到要导入eclipse项目的根目录下面,
然后修改.project文件里面的name节点为项目名称为对应导入的项目的名称
方法2.直接在要导入项目跟目录下面新建.project文件,在里面添加如下内容
<?xml version="1.0" encoding="UTF-8"?>
<projectDescription>
	<name>ebk</name>
</projectDescription>
执行上面两种方法任意一种后就可以顺利导入项目了

**导入后需要配置项目：**

右键项目properties>project facets（只是java项目要勾选上`java`,不同项目工程需要勾选不同打模块）



### 2.idea

https://www.jetbrains.com/idea/

```
https://blog.csdn.net/flyingdream123/article/details/77622421/
https://jingyan.baidu.com/article/afd8f4debd60f434e286e91f.html
```
idea导入项目：

File->new->Project from existing sources（选择对应类型项目）
idea环境配置：

File->Settings

idea项目配置：

File->Project Structure

## 四.git

### 1.linux install

```
ubuntu:
sudo apt-get install git
centos:
sudo yum install git
```

### 2.windows install

```
https://gitforwindows.org(https://git-for-windows.github.io)
或
https://git-scm.com
下载
```

## 五.vscode

https://code.visualstudio.com/

## [other](other.md)

```
1.tomcat,jetty(java web容器)
2.subclipse(eclipseSVN插件)
3.WindowBuilder(eclipse gui plugins)
```

