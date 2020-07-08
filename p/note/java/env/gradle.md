# gradle使用

gradle视频教程 	

https://www.bilibili.com/video/av31574438/?p=1

## 一．gradle安装

官网 ：https://gradle.org

下载二进制包：https://services.gradle.org/distributions/gradle-6.5.1-bin.zip

解压并配置环境变量：

> GRADLE_HOME=D:\soft\dev\gradle-5.6.3 	
>
> PATH=%GRADLE_HOME%\bin

使用本地maven仓库(非必)

>环境变量配置：GRADLE_USER_HOME=D:\soft\dev\mvnRepo
>
>项目配置build.gradle添加mavenLocal()(先从maven本地仓库mavenLocal()找,如找不到再从中央仓库mavenCentral() 找) 	
>
>	repositories {
>		mavenLocal()
>		mavenCentral() 	
>	}



验证安装：

> gradle -v

## 二．配置介绍

1.默认安装本地jar缓存目录在当前用户目录.gradle下

2 .项目构建文件build.gradle

3.全局配置类似setting.xml

>环境变量配置：GRADLE_USER_HOME=D:\soft\dev\mvnRepo
>
>项目配置build.gradle添加mavenLocal()(先从maven本地仓库mavenLocal()找,如找不到再从中央仓库找) 	
>
>	repositories {
>		mavenLocal()
> 		mavenCentral() 	
>	}
> 

4.全局配置类似setting.xml

???

## 三.创建聚合项目

1.idea创建web项目

>1>main下新建webapp/WEB-INF/web.xml文件与目录
>
>2>build.gradle添加apply plugin: 'war'
>
>3>导入相关依赖即可

2.拆分聚合项目

> ???

http://ddrv.cn/a/263902

## 四.使用命令创建项目

https://www.cnblogs.com/xingyunblog/p/10428178.html  

gradle init 	

配置 https://www.cnblogs.com/doublegi/p/5956575.html