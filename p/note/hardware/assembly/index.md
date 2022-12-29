# 汇编&masm

## 概述
```
视频（计算机语言 x86汇编语言）：https://www.bilibili.com/video/BV1xE411N74T

x86汇编
	https://www.cnblogs.com/juzaizai/p/15195107.html
	汇编分类
	https://blog.csdn.net/oqqHuTu12345678/article/details/125697207
	https://blog.csdn.net/liigo/article/details/80680929
	汇编：
	https://www.bilibili.com/video/BV1xE411N74T
	linux内核采用多汇编：
	https://blog.csdn.net/oqqHuTu12345678/article/details/125676002
	编译器(开源)：
	https://nasm.us
		安装：
		./configure --prefix=/opt/nasm

》〉》〉》〉》〉》〉》〉》〉》〉》〉》〉》〉》〉
b站视频下载
Imageio: 'ffmpeg.linux64' was not found on your computer; downloading it now.
https://blog.csdn.net/NFMSR/article/details/78559930

但为了提高读写效率，会减少磁头的移动，写完1盘面前后两个0赤道写完后，再写2盘面两个0赤道，也就是写完一个柱面后移动磁头，在依次写所有柱面的2磁头

采用 磁头，磁道，扇区访问硬盘方法 叫CHS模式（此方法比较麻烦）
逻辑块地址（LBA）,现在硬盘都支持LBA模式

写入工具
https://github.com/AloneCafe/fixed-vhd-writer
```
## 一．masm

```
官网:
http://www.masm32.com/

下载地址:
http://www.masm32.com/download.htm

masm是微软为x86开发的汇编语言开发环境,只能限制在windows平台

示例:
文件名:1.asm
assume cs:codesg

codesg segment

    mov ax,0123H
    mov bx,0456H
    add ax,bx
    add ax,ax

    mov ax,4c00H
    int 21H

codesg ends

end

编译:
masm//运行此命令
1//提示输入文件名回车生成.OBJ文件(后面的属性命令可以直接按回车忽略)

连接:
link//运行此命令
1//提示输入文件名回车生成.exe文件(后面的属性命令可以直接按回车忽略)

快速编译,连接:
masm d:\1.asm //(后面的属性命令可以直接按回车忽略)
link d:\1.asm //(后面的属性命令可以直接按回车忽略)
```

