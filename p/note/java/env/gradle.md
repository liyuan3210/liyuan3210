# gradle使用

gradle视频教程 	

https://www.bilibili.com/video/av31574438/?p=1

## 二．配置介绍

**gradle基于groovy语言**

1.配置阿里代理

repositories {
	mavenLocal()
	maven { url'http://maven.aliyun.com/nexus/content/groups/public/' }
	jcenter()
}

2 .哪些项目文件不能删除(提交git)

```
src					//源码
build.gradle		//项目依赖配置
settings.gradle		//全局配置
```

3.全局配置类似setting.xml

> ???

## 二.创建聚合项目

1.idea创建web项目

>1>main下新建webapp/WEB-INF/web.xml文件与目录
>
>2>build.gradle添加apply plugin: 'war'
>
>3>导入相关依赖即可

2.拆分聚合项目

> ???

http://ddrv.cn/a/263902

## 四.常用命令

### 1.使用命令创建项目

gradle init 	

https://www.cnblogs.com/xingyunblog/p/10428178.html  

配置 https://www.cnblogs.com/doublegi/p/5956575.html

### 2.使用命令清理，打包项目

gradle clean build	//清理并打包

gradle build -x test	//跳过测试 

## 问题
```
1.图解idea不能新建JavaClass和Package解决办法(要选中java目录才行)
https://blog.csdn.net/qq_36838191/article/details/80918200
https://www.jb51.net/article/142791.htm

2.Run with --stacktrace option to get the stack trace. Run with --info or --debug
https://blog.csdn.net/qq_42071369/article/details/122626444

3.gradle user home作用？？？
https://www.yisu.com/zixun/309965.html

* GRADLE_USER_HOME(环境变量,命令行gradlew目录，与gradle命令是不同的)：
指定Gradle用户主目录位置，即.gradle目录位置。
* Gradle user home(eclipse与idea都有自己的Gradle user home)：
```