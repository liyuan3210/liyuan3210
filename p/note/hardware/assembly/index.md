
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

#### [一。8086实模式到保护模式nasm](8086.md)

nasm安装配置

```
nasm官网：https://nasm.us
下载源码：nasm-2.15.05.tar.gz

编译安装：
$ ./configure --prefix=/opt/nasm
$ make && make install

配置环境变量：
export NASM_HOME=/opt/nasm
export $NASM_HOME/bin:$PATH

实例编译运行：
代码：
hello.asm

section .data
  hello:     db 'Hello world!',10    ; 'Hello world!' plus a linefeed character
  helloLen:  equ $-hello             ; Length of the 'Hello world!' string
                                     ; (I'll explain soon)
 
section .text
  global _start
 
_start:
  mov eax,4            ; The system call for write (sys_write)
  mov ebx,1            ; File descriptor 1 - standard output
  mov ecx,hello        ; Put the offset of hello in ecx
  mov edx,helloLen     ; helloLen is a constant, so we don't need to say
                       ;  mov edx,[helloLen] to get it's actual value
  int 80h              ; Call the kernel
 
  mov eax,1            ; The system call for exit (sys_exit)
  mov ebx,0            ; Exit with return code of 0 (no error)
  int 80h

编译
nasm -f elf64 hello.asm
如果是32位系统就把elf64改为elf32

链接
ld -s -o hello hello.o

运行
./hello
```

#### 二。masm（微软的编译器）

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