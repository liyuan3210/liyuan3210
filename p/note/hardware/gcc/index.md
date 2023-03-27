# gcc跨平台编程

代码：https://github.com/liyuan3210/gcc

跨平台c开发
https://www.cnblogs.com/renyuan/p/5031100.html

C语言指针，内存管理
https://www.bilibili.com/video/BV1Dt411R77A?p=4

1）c语言内存4个区
代码区
静态区
堆区
栈区

### 一。gcc跨平台编译工具
 标准C

Windows C [安装微软vs](../../handle/dev_platform.md)

Linux C

### windows平台gcc环境编译

```
一。windows环境安装（gcc）
官网：
	https://www.mingw-w64.org
下载页面：
	首先选择下载菜单--->Sources下面进入SourceForge--->进入后选择Files（拉到最下面选择版本下载）
Mingw-w64安装（posix-seh，win32-seh选择）：
    https://cloud.tencent.com/developer/article/2055102
    https://blog.csdn.net/zy215215/article/details/123078689
    说明（64位系统建议选seh版本）：
    异常处理在开发中非常重要，在开发的过程中，大部分的时间会耗在处理各种异常情况上。seh 是新发明的，而 sjlj 则是古老的。seh 性能比较好，但不支持 32位。 sjlj 稳定性好，支持 32位。建议64位操作系统选择seh。
下载地址：
	https://sourceforge.net/projects/mingw-w64/files/mingw-w64/mingw-w64-release/
	
二。参考资料
nginx官方推荐：
http://nginx.org/en/docs/howto_build_on_win32.html

windows平台gcc环境编译
http://nginx.org/en/docs/howto_build_on_win32.html
MSYS or MSYS2区别？
https://blog.csdn.net/nodeman/article/details/106431752
Cygwin、Msys（停止维护）、Msys2、MinGW、Mingw-w64、TDM-GCC 区别 ？
https://www.cnblogs.com/flyinggod/p/12888060.html
https://www.bilibili.com/video/BV1k34y1h76e
Mingw-w64安装及各个包选项区别
https://blog.csdn.net/zy215215/article/details/123078689
```



### eclipse_mingw_gcc：

```
eclipse C GCC mingw开发环境

下载地址
https://sourceforge.net/projects/mingw/

访问不了了
www.mingw.org

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

### linux内核开发

[内核开发](../linux-kernel/index.md)