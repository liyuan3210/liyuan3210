# 单片机

直流电与交流电的区别？？？

电子元器件一般在哪里买？？？

电平标准？？？

**见 "常用MCU芯片.docx"**

## 一．基础

```
			51chip
电流:
1MA(兆安)=1000kA(千安)=1000000A(安)
1A(安)=1000mA(毫安)=1000000μA(微安)

电压:
1MV(兆伏)=1000KV(千伏)=1000,000V(伏)
1KV(千伏)=1000V(伏)=1000,000mV(毫伏)=1000,000,000μV(微伏)

欧姆定律：
I：(电流）的单位是安培（A）
U：(电压）的单位是伏特（V）
R ：(电阻）的单位是欧姆（Ω）
I=U/R
电阻：
单位换算:1MΩ=1000KΩ =10000000 Ω

电容：
作用:隔直流通交流
单位换算:1F=1000mF=1000,000uF=1000,000,000nF=1000,000,000,000nF

电感：
作用:(隔交流通直流)
单位换算:1H=1000mH=1000,000uH=1000,000,000nH=1000,000,000,000pH

pc接口:
串口(9根线):
异步串行通信，由于现代晶振可以做到时钟频率稳定高，所以不需要那边多针脚，
只需要rd,td两根脚

usb口:
单片机与usb通信，可以用到usb桥芯片，如：
usb1,1:PDIUSBD12
usb2.0:CYC68013
usb_host:SL811,CH375
(usb_host最多可以连接127个devic)

可编程芯片自带usb控制器

并口:
连接打印机
控制其他外设
类似于单片机IO口


TTL门电路构成 : 三极管
CMOS门电路构成：MOS管

电平标准：
数字电路中只有两种电平：高,低

TTL(单片机):高>+5v,低>0v

RS232(计算机串口):高>-12v,低>+12v
所以计算机与单片机之间通讯时需要加电平转换芯片max232

还有如下电平标准
CMOS，LVTTL，LVCMOS，ECL，PECL，LVPECL，，RS485等等

//输入
VIH
VIL
//输出
VOH
VOL

51芯片厂商：
intel:80c31,80c51,87c51
atmel:89c51,89c52,89c2051
philips,华邦,dallas,stc,arm等

51芯片类型：
1.总线型
2.非总线型

c-51C语言类型
sfr:特殊功能寄存器声明
sfr16:sfr 的16位数据声明
sbit:特殊功能位声明
bit:位变量声明
例如：
sbit 0v=PSW^2;

c语言常用头文件
reg51.h
reg52.h
math.h
ctype.h
stdio.h
stdlib.h
absacc.h

DS12C887
排阻：

锁存器：



CPLD与FPGA
硬件语言：
VHDL,VerilogHDL语言

四大生产厂商:
altera(学校用的比较多)
xilinx(公司研发机构用的比较多)
actel
lattice

AVR

OMAP

DSP

SOPC

www.alldatasheet.com

----------------------------------------------------------------------------------------------------
			51最小系统

						STC12C5A60S2
一.根据芯片文档连接物理电路,最小系统:
   电路图<>实物图


二.根据usb下载器安装驱动
1.先把usb下载器插到电脑，查看计算机设备管理，查看占用的com端口

如果设备未识别，需要安装usb驱动

2.安装keil编译器
1>.新建一个工程，再新建一个源码文件，再把文件添加到项目中去
新建project：project->new project-填写项目名称(点击保存)

选择芯片型号->Atmel->89c52(兼容89c51)

新建file：file->(按ctrl+s)->填写文件名称(testFile.c点击保存)

把file添加到项目中:右键选择Source Group1->add file to group1...

编译设置

设置编译(确保):output>create hex ~ (确保被选中)
编译:编辑好源码后,点击编译,生成后的文件在当前项目下*.hex

三.下载源码到芯片
1.首先确保最小系统连接好，usb下载器连接电脑并连接最小系统Gnd,Rxd,Txd.Vcc(与芯片先不连接)

2.打开ISP客户端,选择要写入的*.hex文件,点击下载(下载波特率设定为57600-19200)

3.当下载处于等待状态时，连上Vcc线，开始下载(如果最小系统没问题)

四.hello world
Led灯正极接芯片p1口，负极接Gnd(可以验证最小系统是否运行起来)
代码如下:
#include<reg52.h>
sbit led1=P1^0;
void delay(unsigned int i); //声明延时函数
void main(){
    while(1){
    led1=1;
    delay(10000);
    led1=0;
    delay(10000);
    }
}
/*******延时函数*************/
void delay(unsigned int i)
{
    unsigned char j;
    for(i; i > 0; i--)
        for(j = 255; j > 0; j--);

}
						STC12C4052AD
一.根据芯片电路连接
vcc20连接蜂鸣器+极
gnd连接-极

二.示例代码
在程序实例.zip->project->音乐-挥舞翅膀的女孩
----------------------------------------------------------------------------------------------------
二极管:
二极管实现稳压
led:3-10mA(导通电流)
常用的有： 
整流二极管、检波二极管、光敏二极管、发光二极管、恒流二极管、稳压二极管等

桥式整流电路
https://pan.baidu.com/share/link?shareid=1110439145&uk=2839012393

            一.半导体导电特性
导体>半导体>绝缘体
半导体物质:
    ·锗，硅，砷化镓和一些硫化物质
    ·晶体：通过一定工艺过程，可以将半导体制成晶体
    ·本征半导体：完全纯净的，结构完整的半导体称
硅与锗结构:
    ·最外层：四个电子
    ·硅和锗共价键：八个电子
半导体导电性影响:
     温度，混合杂质两个方面会影响半导体的导电性，会增加半导体的自
    由电子与空穴，本征半导体加热后理论上自由电子与空穴是相等的，自由电子
    会不断的填充附近的空穴来形成自由电子，这样半导体就会导电。
N型半导体与P型半导体(在本征半导体中混合杂质):
    N:自由电子大大增加的就是N型
    P:空穴浓度大大增加的就是P型
------------------------------------------------------------------------------------------
            二.PN结及单向导电性
在同一片半导体上,分别制造P型和N型半导体，经过载流子的扩散它们的交接面就形成了PN结
单向导电性:
    ·PN结正向偏置(正向导电)
    P连正，N连负

    ·PN结反向偏置
    P连负，N连正
PN结特性:
    伏安特性(见：fatx.jpg)

    反向击穿特性
电容特性:
    势磊电容(反向)
    扩散电容(正向)
------------------------------------------------------------------------------------------
            三.半导体二极管
由PN结上加上引线与外壳就是半导体二极管。
二极管类型(应用场合不同):
    ·点接触型
    ·面接触型
    ·平面型
二极管参数:
·最大整流电流 (If)
    正向时 二极管长期连续工作，允许通过二极管的最大整流电流的平均值(通常取小于它的值)
·反向击穿电压 (Ubr)
    反向时 达到这个电压后，电流急剧增加
·最大反向工作电压 (Urm)
    反向时 为安全，在实际工作时，它一般只按反向击穿电压的一半计算
·反向电流 (Ur)
    反向时 电压小于“最大反向工作电压”的电流
·正向压降  (Uf)
    正向时 在规定的电压下，二极管的正向电压降 硅：0.6v~0.8v 锗 0.2v~0.3v
·动态电阻  (Rd)
    正向时 反映了二极管正向特性曲线斜率的倒数。
    rd=V/I (反映了电压对电流的控制)

二极管模型:
·理想模型
    相当于把二极管当作开关使用
·恒压降模型(实际应用的比较多)
    电压不随电流变化而变化
硅管压降:0.7v
锗管压降:0.3v
    例子分析


其它二级管:
·稳压二级管
    曲线与普通二级管一样，但稳压二级管是应用在反向击穿区的特殊硅二级管，稳压二级管应用了特殊的击穿工艺。
参数:
    1)稳定电压 (Vz)
    2)最大耗散功率  (Pzm)=VI
    3)最大稳定工作电流 (Imax)
    4)最小稳定工作电流 (Imin)
稳压二级管在工作时应反接一只电阻，起到保护稳压管。

·光电二级管
    反向电流随光照强度的增加而上升。
·发光二级管
    正向工作 常用来做电源指示灯用,特性与一般二级管一样。
------------------------------------------------------------------------------------------

结构类型:
1.NPN
2.PNP
共基极电流放大系数(α):

满足条件:
发射结要正偏
集电极要反偏
放大电流计算:
1.共基极

2.共射极

------------------------------------------------------------------------------------------
STM32免费开发环境
https://blog.csdn.net/annic9/article/details/78457304
```

## 问题
##### 1.硬件工程师与OS

```
工业版生产厂家及比较???

核心板选择
http://www.eefocus.com/embedded/377168

生产厂家
https://zhidao.baidu.com/question/1759658794448375468.html
https://zhidao.baidu.com/question/1387861894161746020.html

硬件工程师(三种公司工作内容)
http://baijiahao.baidu.com/s?id=1600349053165100847&wfr=spider&for=pc
```

##### 2.物联网操作系统

```
iot物联网操作系统

十大物联网操作系统
http://www.eefocus.com/communication/374822/r0
http://m.elecfans.com/article/949612.html

单片机操作系统有哪些
http://m.elecfans.com/article/580764.html

腾讯物联网系统
https://github.com/Tencent/TencentOS-tiny

完全用汇编写的操作系统
menuetOS
```

##### 3.开源硬件

```
Arduino 

BeagleBone 

Raspberry pi (树莓派) 

OrangePi (橙子派)  http://www.orangepi.cn/
```

##### 4.芯片平台

```
芯片平台???
1.adb连接操作(操作范围)
2.电烙铁,usb接头
3.51芯片编译烧写
4.wifi无线模块
```

##### 5.问题

```
1.硬件工程师与OS	???
2.物联网操作系统	???
3.开源硬件  ???
4.芯片平台	???
5.自制无线收发器(原理)
6.卫星锅原理
7.搜音机原理


电路，模电，数电工具：
开源电路仿真软件CircuitJS1
https://blog.csdn.net/Naisu_kun/article/details/124189299
https://www.kicad.org/

MATLAB的4种开源替代品（GNU Octave）
https://www.likecs.com/show-805013.html
```
