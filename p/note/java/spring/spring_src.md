# spring

## 一.spring核心(源码)
```
官网：https://spring.io
核心模块：https://spring.io/projects/spring-framework
github：https://github.com/spring-projects/spring-framework
二进制包获取:https://github.com/spring-projects/spring-framework/wiki/Spring-Framework-Artifacts
```

代码：https://github.com/liyuan3210/spring

#### 1.知识必备

```
1）1.IOC控制反转(反射)
https://www.bilibili.com/video/BV1C4411373T?p=1
链接:https://pan.baidu.com/s/1o5s2sKa_FazhLKHmTLAO7g 提取码:te99
liyuandf账号:共享资源>course>java>【java】反射
实例：https://github.com/liyuan3210/java/tree/master/spring/simple/src/main/java/com/liyuan3210/spring/simple/reflect

2）.AOP(使用aspectj标注，底层实现用标准jdk动态代理)
三种实现方式(详细见“设计模式”)
*.spring-AspectJ
*.jdk动态代理
*.CGLIB
2.1）aspectj动态代理安装如下
	2.1.1).aspectj动态代理安装如下安装配置
		AspectJ介绍
		https://blog.csdn.net/qq_32705919/article/details/124236568
		https://www.hreshhao.com/getting-started-with-aspectj/
	2.1.2）官方下载：
		https://www.eclipse.org/aspectj/downloads.php
		2.3）执行安装(预先准备好aspectj安装目录，安装过程中要指定jdk目录，aspectj安装目录)
		$ java -jar aspectj-1.9.6.jar
	2.1.3）配置环境变量
		export AJC_HOME=/home/ubuntu-a10/Desktop/soft/dev/aspectj
		export CLASSPATH=.:$AJC_HOME/lib/aspectjrt.jar
		export 		   PATH=$JAVA_HOME/bin:$M2_HOME/bin:$NODEJS_HOME/bin:$GRADLE_HOME/bin:$NASM_HOME/bin:$AJC_HOME/bin:$NASM_WT:$PATH
	2.1.4）验证
		$ ajc -v

实例：https://github.com/liyuan3210/java/tree/master/spring/simple
```

#### 2.idea导入spring源码

###### 构建步骤spring-framework-5.3.26
```
一。版本：
    1.版本列表
        1）.jdk11
            引用“https://github.com/spring-projects/spring-framework/wiki/Build-from-Source”
            To build you will need Git and JDK 17. Be sure that your JAVA_HOME environment variable points to the jdk17 folder extracted from the JDK download.
            高于jdk11会报错warnings found and -Werror specified,版本为最新6.x时jdk必须是jdk17
        2）.spring-framework5.3.26
        3）.gradle7.5.1
        4）.ideaIC-2022.3.3

    2.安装配置
        maven,gradle安装配置见../env/index.md

二。构建
	1.克隆项目
	$ git clone https://github.com/liyuan3210/spring
	
	2.导入idea及配置
		2.1）File--->Project Struture(配置工程jdk)
		2.2）File--->Settings--->搜索"File Encodings"(配置字符编码UTF-8)
		2.3）File--->Settings--->搜索"gradle"（配置gradle）
			见[图1.1]
		2.3）Help--->Memory Settings--->设置大小2048M
			
	3.idea构建
        根据工程下.../spring-framework-5.3.26/import-into-idea.md构建
        ## Steps(官方引用)
        //第一步：表示需要预编译spring-oxm，spring-core
        1. Precompile `spring-oxm` with `./gradlew :spring-oxm:compileTestJava`
        //第二部：导入spring源码到idea
        2. Import into IntelliJ (File -> New -> Project from Existing Sources -> Navigate to directory -> Select build.gradle)
        //如果idea没装AspectJ，先要从idea把spring-aspects工程移除（右键选中spring整个项目-->Load/UnLoad Moudles）
        3. When prompted exclude the `spring-aspects` module (or after the import via File-> Project Structure -> Modules)
        4. Code away
            gradle build
		
	4.安装AspectJ（安装好后就可以load整个spring-aspects到工程进行构建了
		安装见上面“知识必备”AOP章节2.1
		菜单选择File-->Project Structure-->Libraries-->添加aspectjrt.jar
		
三。问题
	见源码工程里面笔记
```
图1.1
<img src="img/1.1.png" style="zoom:60%;" />

```
图1.1上其中，Build and run using与Run tests using可以设置成Default或Intelli IDEA都可以
```
###### 构建步骤spring-framework-5.2.23
```
一。版本
    1.版本列表
        1）.jdk11
            引用“https://github.com/spring-projects/spring-framework/wiki/Build-from-Source”
            To build you will need Git and JDK 17. Be sure that your JAVA_HOME environment variable points to the jdk17 folder extracted from the JDK download.
            高于jdk11会报错warnings found and -Werror specified,版本为最新6.x时jdk必须是jdk17
        2）.spring-framework5.2.23
        3）.gradle-5.6.4-bin.zip（降级）
        4）.ideaIC-2022.3.3

    2.安装配置
        maven,gradle安装配置见../env/index.md

二。构建

待验证（不可行）：
	修改spring-jdbc模块下的spring-jdbc.gradle文件，找到optional(“com.h2database:h2”)。
	改成compile(“com.h2database:h2”)。
	
三。问题
//貌似还有
4.H2DatabasePopulatorTests（TimeoutException: executesHugeScriptInReasonableTime() timed out after 1 second）
https://blog.csdn.net/loveyouyuan/article/details/121541324
	解决办法(貌似不行)：
	修改spring-jdbc模块下的spring-jdbc.gradle文件，找到optional(“com.h2database:h2”)，将optional
改成compile
```
###### 构建步骤spring-framework-6.0.7

```
一。版本
    1.版本列表
        1）.jdk18
            引用“https://github.com/spring-projects/spring-framework/wiki/Build-from-Source”
            To build you will need Git and JDK 17. Be sure that your JAVA_HOME environment variable points to the jdk17 folder extracted from the JDK download.
            高于jdk11会报错warnings found and -Werror specified,版本为最新6.x时jdk必须是jdk17
        2）.spring-framework-6.0.7
        3）.gradle-7.6-bin.zip（升级）
        4）.ideaIC-2022.3.3

    2.安装配置
        maven,gradle安装配置见../env/index.md

二。构建
	1.克隆项目
	$ git clone https://github.com/liyuan3210/spring
	
	2.导入idea及配置
		2.1）File--->Project Struture(配置工程jdk)
		2.2）File--->Settings--->搜索"File Encodings"(配置字符编码UTF-8)
		2.3）File--->Settings--->搜索"gradle"（配置gradle）
			见[图1.1]
		2.4）Help--->Memory Settings--->设置大小2048M
	
	3.idea构建
        根据工程下.../spring-framework-5.3.26/import-into-idea.md构建
        ## Steps(官方引用)
        //第一步：表示需要预编译spring-oxm，spring-core
        1. Precompile `spring-oxm` with `./gradlew :spring-oxm:compileTestJava`
        //第二部：导入spring源码到idea
        2. Import into IntelliJ (File -> New -> Project from Existing Sources -> Navigate to directory -> Select build.gradle)
        //如果idea没装AspectJ，先要从idea把spring-aspects工程移除（右键选中spring整个项目-->Load/UnLoad Moudles）
        3. When prompted exclude the `spring-aspects` module (or after the import via File-> Project Structure -> Modules)
        4. Code away
            gradle build
		
	4.安装AspectJ（安装好后就可以load整个spring-aspects到工程进行构建了）
		安装见上面“知识必备”AOP章节2.1
		菜单选择File-->Project Structure-->Libraries-->添加aspectjrt.jar

三。问题
1.载入spring-aspects工程，没引入aspectjrt.jar报错
ReactorNetty2StompBrokerRelayIntegrationTests > relayReconnectsIfBrokerComesBackUp() FAILED
    java.lang.AssertionError at AbstractStompBrokerRelayIntegrationTests.java:275
    错误详细见笔记test2
2.> Task :spring-core:test
OpenJDK 64-Bit Server VM warning: Option AllowRedefinitionToAddDeleteMethods was deprecated in version 13.0 and will likely be removed in a future release.
OpenJDK 64-Bit Server VM warning: Sharing is only supported for boot loader classes because bootstrap classpath has been appended

> Task :spring-context:test
OpenJDK 64-Bit Server VM warning: Sharing is only supported for boot loader classes because bootstrap classpath has been appended

```

###### 创建spring子工程并验证

```

```

#### 3.spring源码核心(框架)

```
代码轮廓

1）idea的调试（debug）

2）快捷键
	2.1）查看当前接口或抽象类的实现：	ctrl+H
	2.2) 查看此方法在哪调用：			ctrl+B
```

#### 4.spring源码debug

```
1）调试方法

2）添加子项目
```