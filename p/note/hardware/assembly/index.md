
#### 汇编

#### 概要

```
x86汇编
	x86汇编快速入门
	https://www.cnblogs.com/juzaizai/p/15195107.html
	汇编分类
	https://blog.csdn.net/oqqHuTu12345678/article/details/125697207
	https://blog.csdn.net/liigo/article/details/80680929
	linux内核采用多汇编：
	https://blog.csdn.net/oqqHuTu12345678/article/details/125676002
	编译器(开源)：
	https://nasm.us
		安装：
		./configure --prefix=/opt/nasm

写入工具:
	https://github.com/AloneCafe/fixed-vhd-writer
	https://github.com/fujiawei-dev/fixed-vhd-writer
```

#### [一。8086实模式到保护模式](8086.md)

#### masm（微软的编译器）

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