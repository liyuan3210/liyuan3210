# 汇编&masm

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

