# ok6410开发板烧写

## 一．烧写步骤

```
飞凌官网
http://www.forlinx.com

ok6410启动顺序
1.sd启动：
  ON : 4-5-6-7-8
OFF : 1-2-3

2.nandFlash启动：
  ON : 4-5-8
OFF : 1-2-3-6-7

linux3.0(一键烧写)
WinCE换Linux需要先启动Eboot擦除nandflash,原因是wince设计时候有一个约定，把前四个块都标记成了坏块，也就是说把bootloader分区标记成坏块了，防止bootloader被wince应用程序擦掉.
启动时按空格键进入eboot状态,按A，执行：
Erase All Blocks然后就可以开始一键烧写linux系统了
1.制作sd卡
制作工具:sd_writer.exe
烧写的程序:mmc.bin

2.拷贝(上面都copy到 sd 卡)
u-boot
zImage
rootfs.yaffs2

3.插入sd卡到开发板,设置为从sd卡启动
等待烧写完毕

4.烧写完毕后关闭开发板，设置为nandFlash启动就可以进去了
这个启动失败,具体问题查看linux_start.txt最后几行异常信息

winCE6.0(一键烧写)
1.sd卡制作
制作工具:sd_writer.exe
烧写的程序:IROM_SD_EBOOT_SDFUSER.nb0

2.启动文件(上面都copy到 sd 卡)
STEPLDR.nb0 : steplooer上电最先加载，用于引导Eboot
STEPLDR.bin : STEPLDR.nb0压缩后的文件
EBOOT.nb0 : Eboot用于引导winCE的镜像Nk,以及一些参数的设定,如液晶屏的分辨率等
EBOOT.bin : EBOOT.nb0压缩后的文件
NK.bin : winCE系统的镜像文件

3.插入sd卡到开发板,设置为从sd卡启动
等待烧写完毕

4.烧写完毕后关闭开发板，设置为nandFlash启动就可以进去了
启动开发板,看到烧写成功系统界面

镜像引导顺序
系统上电-->STEPLDR-->EBOOT-->NK


usb烧写
```

