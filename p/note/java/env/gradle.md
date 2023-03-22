# gradle使用

gradle视频教程 	

https://www.bilibili.com/video/av31574438/?p=1

## 二．配置介绍

**gradle基于groovy语言**

1.单项目配置build.gradle

先从maven本地仓库mavenLocal()找,如找不到再从阿里仓库代理找

repositories {
	mavenLocal()
	maven { url'http://maven.aliyun.com/nexus/content/groups/public/' }
	jcenter()
}

配置全局：

```
allprojects {
	repositories {
		mavenLocal() 
		maven { name "Alibaba" ; url "https://maven.aliyun.com/repository/public" } 
		maven { name "Bstek" ; url "https://nexus.bsdn.org/content/groups/public/" } 
		mavenCentral()
	}
	buildscript {
		repositories { 
			maven { name "Alibaba" ; url 'https://maven.aliyun.com/repository/public' } 
			maven { name "Bstek" ; url 'https://nexus.bsdn.org/content/groups/public/' } 
			maven { name "M2" ; url 'https://plugins.gradle.org/m2/' }
		}
	}
}
```

启用 init.gradle 文件的方法有:

```
1.在命令行指定文件,例如：gradle --init-script yourdir/init.gradle -q taskName。你可以多次输入此命令来指定多个init文件
2.把init.gradle文件放到 USER_HOME/.gradle/ 目录下
3.把以.gradle结尾的文件放到 USER_HOME/.gradle/init.d/ 目录下
4.把以.gradle结尾的文件放到 GRADLE_HOME/init.d/ 目录下
如果存在上面的4种方式的2种以上，gradle会按上面的1-4序号依次执行这些文件，如果给定目录下存在多个init脚本，会
按拼音a-z顺序执行这些脚本，每个init脚本都存在一个对应的gradle实例,你在这个文件中调用的所有方法和属性，都会
委托给这个gradle实例，每个init脚本都实现了Script接口。
```

仓库地址说明：

```
1）mavenLocal(): 指定使用maven本地仓库，而本地仓库在配置maven时settings文件指定的仓库位置。如E:/repository，gradle 
查找jar包顺序如下：USER_HOME/.m2/settings.xml >> M2_HOME/conf/settings.xml >> USER_HOME/.m2/repository maven { url 地址}，指定maven仓库，一般用私有仓库地址或其它的第三方库【比如阿里镜像仓库地址】。

2）mavenCentral()：这是Maven的中央仓库，无需配置，直接声明就可以使用。

3）jcenter():JCenter中央仓库，实际也是是用的maven搭建的，但相比Maven仓库更友好，通过CDN分发，并且支持https访
问,在新版本中已经废弃了，替换为了mavenCentral()。

总之, gradle可以通过指定仓库地址为本地maven仓库地址和远程仓库地址相结合的方式，避免每次都会去远程仓库下载
依赖库。这种方式也有一定的问题，如果本地maven仓库有这个依赖，就会从直接加载本地依赖，如果本地仓库没有该
依赖，那么还是会从远程下载。但是下载的jar不是存储在本地maven仓库中，而是放在自己的缓存目录中，默认在
USER_HOME/.gradle/caches目录,当然如果我们配置过GRADLE_USER_HOME环境变量，则会放在
GRADLE_USER_HOME/caches目录,那么可不可以将gradle caches指向maven repository。我们说这是不行的，caches下载
文件不是按照maven仓库中存放的方式。
```



2 .哪些项目文件不能删除(提交git)

```
src					//源码
build.gradle		//项目依赖配置
settings.gradle		//全局配置
-非必（运行gradlew|gradlew.bat并不是运行的本地安装的gradle,这个适合本地不安装gradle也能构建项目，但通常都是统一使用本地安装的gradle,所以下面的项目文件都可以删除）
gradle
	wrapper
		gradle-warpper.jar
		gradle-warpper.properties
gradlew			//其他平台启动脚本
gradlew.bat		//windows下启动脚本
```

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

```
$ gradle clean 	//清理并打包
$ gradle build 	//构建项目
$ gradle classes //编译业务代码和配置文件
$ gradle test //编译测试代码
$ gradle build -x test	//跳过测试 
```

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
https://www.yisu.com/zixun/309965.html
https://blog.csdn.net/xiaoxiao_su123/article/details/113482331
```