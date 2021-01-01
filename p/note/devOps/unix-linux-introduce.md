# unix与linux介绍

## 一.unix两大分支：

| system V家族                                                 | bsd家族                                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| A/UX  ▪ AIX （IBM） ▪ HP-UX  ▪ IRIX  ▪ LynxOS  ▪ SCO OpenServer  ▪ Tru64  ▪ Xenix  ▪Solaris(SUN) ▪ OS/2 | BSD/OS  ▪ FreeBSD  ▪ NetBSD ▪ NEXTSTEP  ▪Mac OS X  ▪ iOS ▪ OpenBSD  ▪ SUN OS （sun） ▪ OpenSolaris |

**unix诞生于1969年**

| bsd发行版 | 描述       |
| --------- | ---------- |
| FreeBSD   | 侧重稳定   |
| OpenBSD   | 侧重安全   |
| NetBSD    | 侧重多平台 |
| Mac OS    | 苹果系统   |
| BSD / OS  | ???        |
|           |            |
|           |            |

1.BSD家族大观：FreeBSD、OpenBSD、NetBSD

https://blog.csdn.net/sahusoft/article/details/3195594

2.为什么要使用BSD系统

https://www.linuxprobe.com/why-use-bsd.html

**bsd安装软件源方法：**

​		1.package(bsd通常用这种)

​		2.ports

https://blog.csdn.net/zhangdaisylove/article/details/46379947

http://blog.chinaunix.net/uid-29727170-id-4509832.html

## GNU项目：

建立一个自由开放的unix操作系统(操作系统应用层软件集)

unix与linux跑的应用大部分都可以相互移植，因为都遵循了POSIX标准

GNU(一个自由的操作系统)与FSF(自由软件基金)

**1983年发起的自由软件集协作计划**

## 二.linux:
linux为了兼容unix，参照了POSIX标准，linux通常指linux内核
Linux = gnu	+	linux kerne

**linux内核1994年1.0发布**

## 常见linux发行版：

| 家族          | 发行版            | 描述                                              |
| ------------- | ----------------- | ------------------------------------------------- |
| redhat家族    |                   |                                                   |
|               | redhat enterprise | 企业版（收费）                                    |
|               | fedora            | 红包上游版本                                      |
|               | centos            | 红帽下游版本稳定                                  |
|               | rockylinux        | centos替代品                                      |
|               |                   |                                                   |
| debian家族    |                   |                                                   |
|               | debian            |                                                   |
|               | ubuntu            |                                                   |
|               |                   |                                                   |
| suse家族      |                   |                                                   |
|               | suse              | 企业版(收费)                                      |
|               | openSUSE          | 社区开源版                                        |
|               |                   |                                                   |
| archlinux家族 |                   |                                                   |
|               | archlinux         | linux高度定制化(主要基于二进制),2002年3月11日发行 |
|               |                   |                                                   |
| gentoo        |                   | linux高度定制化(主要基于源码)，2002年3月31日发行  |
| Slackware     |                   | 古老的linux发行版，简洁，安全，稳定               |
| PCLinuxOS     |                   | ？？？                                            |
|               |                   |                                                   |
|               |                   |                                                   |

1.archlinux gentoo选择

https://www.cnblogs.com/huapox/archive/2012/03/01/3299990.html

https://blog.csdn.net/neyes/article/details/6129027/

2.手动做LFS	???