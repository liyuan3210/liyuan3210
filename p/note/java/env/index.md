# 环境安装

## [工具链](tools.md)

[*.git配置,ssh免密登陆及openssh](../../devOps/github_gitee_gitlab.md)

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
	groovy语言可以与java语言无缝对接
前提:
	安装配置好jdk
	groovy是基于jdk的,所以要先安装配置好jdk
	配置好JAVA_HOME,path环境变量
	
1>.下载并解压apache-groovy-sdk-3.0.4.zip
配置GROOVY_HOME,path

2>验证
groovy　-v

3>hello world
编辑文件helloWorld.groovy
System.out.println("hello world!");
#运行
$ groovy helloWorld.groovy

* binary与sdk包内容：
apache-groovy-binary-3.0.4.zip		//只包含二进制运行文件			      
apache-groovy-sdk-3.0.4.zip			//all，二进制包，源文件，文档
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
	     <url>https://maven.aliyun.com/nexus/content/groups/public</url> 
	 </mirror>
	指定仓库存储位置:
	<settings>
		<localRepository>D:\soft\dev\mvnrepo</localRepository>
	</settings>

#### [maven详细](maven.md)

### 2.gradle

官网 ：https://gradle.org

下载二进制包：https://services.gradle.org/distributions/gradle-6.5.1-bin.zip

考虑与idea兼容问题：
> 可以查看安装的idea目录下（ideaIC-2021.1.1.win\plugins\gradle）默认支持的gradle版本

解压并配置环境变量：

> GRADLE_HOME=D:\soft\dev\gradle-5.6.3 	
>
> PATH=%GRADLE_HOME%\bin

**环境变量配置GRADLE_USER_HOME(非必)：**

默认在.gradle下，类似maven的localRepository

1.全局配置init.gradle

在gradle安装目录..\init.d下新建init.gradle(需要安装maven并配置M2_HOME环境变量)

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



验证安装：

> gradle -v

#### [gradle详细](gradle.md)

#### idea与eclipse的配置

```
1.idea里面配置Gradle->Use Gradle from->specified location->gradle安装目录
2.eclipse里面要配置好(Local installation directory与Gradle user home)
```


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
idea导入项目：
    File->new->Project from existing sources（选择对应工作目录 或 类型项目）
    idea环境配置：
    File->Settings
    idea项目配置：
    File->Project Structure

1）快捷键
	1.1）查看当前接口或抽象类的实现：	ctrl+H
	1.2) 查看此方法在哪调用：		   ctrl+B
	1.3）查看当前类变量，方法		   alt+7
	1.4）查看当前工程类				 shift(连续按两下)
	1.5）全局搜索文件里的字符		   ctrl+shift+R 或 ctrl+shift+F
	1.6）返回跟进的上一个代码位置	  alt+"←"

2）工程导入
	2.1）。File -> Open（打开一个目录，不会构建工程）
    2.2）。File -> New - > project from existing Source(直接打开工程并构建)
    	Create module from existing sources
    	Import module from external model
    2.3）。File -> Project Settings -> Modules(模块方式导入)
    	Create module from existing sources
    	Import module from external model
    	
3）工程跳过测试方法
	maven工程idea右上侧，有个跳过测试按钮
	
问题：
	idea工程失效问题(File->settings->build,excution...->compiler->shared buid vm optoin)
	配置项：-Djps.track.ap.dependencies=false

参考：
https://blog.csdn.net/flyingdream123/article/details/77622421/
https://jingyan.baidu.com/article/afd8f4debd60f434e286e91f.html
```
### 3.vscode
https://code.visualstudio.com/

## [other](other.md)

```
1.tomcat,jetty(java web容器)
2.subclipse(eclipseSVN插件)
3.WindowBuilder(eclipse gui plugins)
```

### 4.git

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

## 四.python安裝（linux平台）

```
linux平台只能源码安装

linux安装python（网上参考）
https://baijiahao.baidu.com/s?id=1742782855454338816&wfr=spider&for=pc
http://wjhsh.net/yjp372928571-p-12758564.html

1.下載源码 Python-3.9.16.tgz
安装必须
$ apt-get update && apt-get install git -y && apt-get install gcc -y && apt-get install make -y && apt-get install wget -y

2.参考README.rst安装依赖
	Build Instructions
	Refer to the
	`Install dependencies <https://devguide.python.org/setup/#install-dependencies>`

$ sudo apt-get install build-essential gdb lcov pkg-config \
      libbz2-dev libffi-dev libgdbm-dev libgdbm-compat-dev liblzma-dev \
      libncurses5-dev libreadline6-dev libsqlite3-dev libssl-dev \
      lzma lzma-dev tk-dev uuid-dev zlib1g-dev

3.编译安装
$ mkdir /opt/python3
$ ./configure --prefix=/opt/python3 && make && make install

4.配置环境变量
export PYTHON_HOME=/usr/local/python3
export PATH=$PYTHON_HOME/bin:$PATH

Pip更换源：
		root用户的
    # mkdir /root/.pip
    # cd /root/.pip
    # vi pip.conf	//新增pip.conf配置文件
    # 更换源配置
    [global]
    index-url = https://pypi.tuna.tsinghua.edu.cn/simple

问题：
    1.ImportError: cannot import name 'Mapping' from 'collections'
    因为Python3.10版本以后对requests库进行调整，collections中不能直接调用方法Mapping，MutableMapping
    * 由于Python的跨平台特性，因此兼容性最好的还是python3.4
    https://www.muzhuangnet.com/show/13574.html

    2.python, 解决：WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager
    * 假设Python3安装在/usr/local/python3
    $ cd /usr/local/python3
    $ python3 -m venv tutorial-env
    $ source tutorial-env/bin/activate
```
资料
```
https://www.bilibili.com/video/BV1qW4y1a7fU
p94-p97 模块，包，第三房包概念
```
## 五.Emscripten环境安装

```
介绍：
emscripten官网：
https://emscripten.org/
https://github.com/emscripten-core/emscripten
webassembly官网：
https://webassembly.org/
实例：
http://www.uwenku.com/question/p-whwpcqvc-rb.html
https://blog.csdn.net/shuipingtoy/article/details/90512139

准备容器：
$ docker run --name myubuntu_build --net dev-net --ip 172.18.1.30 -it -v /home/ubuntu-a10/Desktop/soft/data:/data -d registry.cn-shanghai.aliyuncs.com/liyuan3210-repo/myubuntu:v2
$ docker exec -it myubuntu_build /bin/bash

1.克隆
$ git clone https://github.com/emscripten-core/emsdk.git

2.安装配置Emscripten：
# Fetch the latest registry of available tools.
$ ./emsdk update
# Download and install the latest SDK tools. Need install Python first. 
$ ./emsdk install latest
# Make the "latest" SDK "active" for the current user. (writes ~/.emscripten file)
$ ./emsdk activate latest
# Activate PATH and other environment variables in the current terminal
$ ./emsdk_env.sh
# To update your environment variables 注意 需要source一下，否则emcc不生效
$ source emsdk_env.sh 
# Verifying Emscripten 验证
$ emcc -v

3.配置环境变量(vi编辑在最后一行添加如下一行)
vi .bashrc
source /opt/emsdk/emsdk_env.sh

4.实例：
编写c代码 hello.c：
#include <stdio.h>
int main() {
  printf("hello, world!\n");
  return 0;
}

编译运行：
# 生成 a.out.js
$ emcc.bat helloworld.cpp
# 生成 helloworld.js
$ emcc.bat helloworld.cpp -o helloworld.js
# 生成 helloworld.html 和 helloworld.js,helloworld.wasm
$ emcc.bat helloworld.cpp -o helloworld.html

nodejs运行：
$ node a.out.js或helloworld.js
```
资料
```
Emscripten简介及初体验
		https://blog.csdn.net/cgs1999/article/details/109032278
webAssembly调用c字节码
		https://www.ruanyifeng.com/blog/2017/09/asmjs_emscripten.html
```


## 六.golang环境安装

```
官网：https://golang.google.cn

视频：
https://www.bilibili.com/video/BV1ME411Y71o
环境变量：GOROOT,Path,GOPATH(项目目录)

解压安装go1.19.3.linux-amd64.tar.gz：
$ xz -d go1.19.3.linux-amd64.tar.gz
$ tar -xvf go1.19.3.linux-amd64.tar

export GO_HOME=/opt/go			//go home	
export PATH=$GO_HOME/bin:$PATH	//go path路径
export GOPATH=/data/goprojects	//go项目目录,感觉没啥用？？？

GOPATH:
	go install/go get和 go的工具等会用到GOPATH环境变量.
	GOPATH是作为编译后二进制的存放目的地和import包时的搜索路径 (其实也是你的工作目录, 你可以在src下创建你自己的go源文件, 然后开始工作)。

GOPATH之下主要包含三个目录: bin、pkg、src：
	bin目录主要存放可执行文件； pkg目录存放编译好的库文件， 主要是*.a文件； src目录下主要存放go的源文件

goprojects目录结构：
	goprojects/src/go_code/project1,project2

代码：
package main
import "fmt"

func main(){
	fmt.Println("hello go")
}

直接运行：go run hello.go
编译： 	go build hello.go

视频课程：
https://www.bilibili.com/video/BV1ME411Y71o
```
## 七.rust语言安装
```
官网：https://www.rust-lang.org/

1.下载并安装：
https://forge.rust-lang.org/infra/other-installation-methods.html
$ chmod -R 777 rustup-init
$ ./rustup-init 
默认选择 1）
1) Proceed with installation (default)
2) Customize installation
3) Cancel installation

To configure your current shell, run:
source "$HOME/.cargo/env"

容器安装需要执行一下source才能识别到
root@1fe821b26306:~# source .bashrc 
root@1fe821b26306:~# source .profile

2.hello world验证

	2.1.验证hello.rs
	fn main(){
		println!("hello world!");
	}

	2.2。编译hello：
	rustc hello.rs

	2.3。更新：
	rustup update

	2.4。cargo工程管理
	cargo new hello_projects

3.配置环境变量
# rustup home dir
export RUSTUP_HOME=/home/environment/rust/.rustup
# rust cargo home dir
export CARGO_HOME=/home/environment/rust/.cargo

if [-f /home/environment/rust/.cargo/env ] then
./home/environment/rust/.cargo/env
fi 

# 用于更新 toolchain
export RUSTUP_DIST_SERVER=https://mirrors.ustc.edu.cn/rust-static
# 用于更新 rustup
export RUSTUP_UPDATE_ROOT=https://mirrors.ustc.edu.cn/rust-static/rustup

4.配置cargo镜像源
配置cargo镜像源/root/.cargo/config
# 清华大学
[source.tuna]
registry="https://mirrors.tuna.tsinghua.edu.cn/git/crates.io-index.git"

视频课程：
https://www.bilibili.com/video/BV1hp4y1k7SV
```
## 八.x86汇编
```
见hardware/assembly
```
## 九.Visual Studio安装
```
下载
首先到微软官方下载在线安装文件https://www.microsoft.com
下载Visual Studio,微软发布的有三个版本分别是
Visual Studio Community(免费版,个人开发学习足够)
Visual Studio Professional
Visual Studio  Enterprise

microsoft visual studio 2017账号
li.yuan3210@163.com
see password file
 
安装好Community版本后，就可以创建项目了

ASP>C# web站点开发及发布
1.文件>新建>项目>Visual C# >web>ASP.NET web Application(.NET Framwork)
>web 窗体

2.新建好项目后>点击上面的运行就可以看到效果了
右边代码管理器(视图>解决方案资源管理器)

3.选择项目右键>发布>选择自定义(第一次发布)>发布方法:文件系统(指定好一个发布目录)

4.iis安装(选择windows组件,iis信息服务，勾上重启电脑)

5.重启后,管理>iis服务>添加一个网站(目录指定上面的发布目录)


常见asp.net开发模式
一.web pages

二.mvc

三.web forms


Visual Studio SVN导入,导出:

安装svn插件https://www.visualsvn.com
下载对应的插件包:VisualSVN for Visual Studio 2017

IDE > visualSVN(安装成功会出现这个工具栏)

SVN导入
在右边单击选中"解决方案winTest"项目右键
>
add solution to subversion
>
添加后,右键commit

svn导出
IDE>visualSVN>get solution from subversion

ASP导出注意事项:
asp导出后需要把packages文件引用copy到svn新导出的../tmp_asp目录下(没有就新建一个项目)

```

## 十.Android Studio开发环境搭建

```
见：hardware/test/android.md
```

## 十一.微信开发

```
官网：
https://weixin.qq.com/

开发者页面：
https://mp.weixin.qq.com/debug/wxadoc/dev/index.html
工具下载>工具>wechat_web_devtools_0.15.152900_x64.exe
快速创建项目>简易教程>下载源码(quickstart.zip)
组件demo项目实例>简易教程>下载源码(demo.zip)

微信OAuth实现用户登录
	https://blog.csdn.net/qq_40437152/article/details/84303104
```