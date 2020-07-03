# 环境安装

## 一.jdk

```
oracle jdk收费,openjdk免费
oracle jdk 与 openjdk从11后保持一致
oracle jdk 8(2019年1月以后jdk8需要购买许可)后面版本需要购买许可,(JDK 8 u201 和 JDK 8 u202 仍可免费使用)
openjdk 免费版本(6个月更新一个版本)

下载官网:https://www.oracle.com/
	添加:JAVA_HOME=D:\soft\java\jdk8
	编辑:Path=;%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin
	添加:CLASSPATH=.;%JAVA_HOME%\lib;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar
	
openjdk(开源免费):
	http://openjdk.java.net/
	https://blog.csdn.net/lanwp5302/article/details/86612197
	
jdk9后的安装(不需要配置CLASSPATH与rt.jar,tools.jar)
	https://github.com/msbbigdata/javase/blob/master/note/JDK12的安装搭建.md
	jlink命令生成jre:
	bin\jlink.exe --module-path jmods --add-modules java.desktop --output jre
```

### groovy语言:

```
官网:
http://www.groovy-lang.org

1>.安装配置jdk(解压安装)
groovy是基于jdk的,所以要先安装配置好jdk
配置JAVA_HOME
配置path

2>.安装配置groovy(解压安装)
下载
https://groovy.apache.org/download.html
http://www.groovy-lang.org/download.html
配置GROOVY_HOME
配置path

3>.编写运行hello word程序
新建helloworld.groovy文件,内容如下
def name='World'; println "Hello $name!"

控制台运行
groovy helloworld.groovy
groovy helloworld.groovy

3.实例功能概要
https://www.jianshu.com/p/e8dec95c4326
```


## 二.BuildTools

## maven:

http://maven.apache.org/ 	见->maven.txt

## gradle:

https://gradle.org/ 		见->gradle.txt

## 三.IDE

### eclipse:

https://www.eclipse.org/

```
Eclipse IDE for Java EE Developers : java web开发
Eclipse IDE for C/C++ Developers : c/c++ gcc环境
```

### idea:

https://www.jetbrains.com/idea/

```
https://blog.csdn.net/flyingdream123/article/details/77622421/
https://jingyan.baidu.com/article/afd8f4debd60f434e286e91f.html
```
## 四.git

linux install:

```
ubuntu:
sudo apt-get install git
centos:
sudo yum install git
```

windows install:

```
https://gitforwindows.org(https://git-for-windows.github.io)
或
https://git-scm.com
下载
```

## [other](other.md)

```
1.tomcat,jetty(java web容器)
2.subclipse(eclipseSVN插件)
3.WindowBuilder(eclipse gui plugins)
```

