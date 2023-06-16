### 仿真与实验

###### Multisim14安装

```
详细见：
百度云盘/liyuandf:云硬盘>env>hardware>multisim
```

###### 笔记


```
电阻
https://zhidao.baidu.com/question/1392049924658595540.html
开关
https://www.xiazaiba.com/jiaocheng/51049.html
电压表，电流表
https://blog.csdn.net/chiluangou0832/article/details/100932377
	
电阻，电压，电流测量方法
https://www.bilibili.com/video/BV1sK411w7V1

国际电阻单位
欧姆=ohm
电阻 pOhm nOhm uOhm mOhm Ohm kOhm MOhm

电阻并联测电流
https://wenku.baidu.com/view/be9bde298e9951e79a89274d.html

Multisim函数信号发生器和示波器的使用介绍
https://www.bilibili.com/video/BV1GK4y1k7fV

Multisim二极管的伏安特性曲线分析(关注该博主)
https://www.bilibili.com/video/BV15Y411h7it

一般数字电压表内阻可达10兆欧。
指针直流电压表的内阻较小,一般在10兆欧以下。

欧姆定律
I=U/R
i = 5v / 10欧

测量感值的方法（万用表没有电感档位，怎么测量电感感值）
https://www.zhihu.com/question/430157773
https://www.bilibili.com/video/BV1424y1y7j1
电容无极性和有极性能代替吗？
https://www.bilibili.com/video/BV1RP411E7Ds
```

-----------------------------------------------------------------------------
### 芯片文档

```
芯片查询网站
http://www.alldatasheet.com
产品打样
https://www.jlc.com/
datasheet
https://pdf.elecfans.com/
硬件开源社区（电子发烧友论坛）：
https://www.elecfans.com/
```

### 芯片文档查找

```
二极管
1N4148
https://www.tme.eu/Document/ff36ab5a5cc13f4a6657d6004a93571d/1N4148,4448,914B.pdf

三极管
A1015(GR331)    PNP
2N5401（B331）PNP
A733（P331）PNP

S9018 (H331）NPN
https://pdf1.alldatasheet.com/datasheet-pdf/view/551277/WINNERJOIN/S9018.html

S8550(D331) PNP

2N3906(B331)  PNP

S9014(C331) NPN
https://html.alldatasheet.com/html-pdf/54742/FAIRCHILD/S9014/404/1/S9014.html

A42(B331) NPN
https://pdf1.alldatasheet.com/datasheet-pdf/view/138719/JIANGSU/A42.html

2N3904(H331) NPN
https://html.alldatasheet.com/html-pdf/11470/ONSEMI/2N3904/180/1/2N3904.html
```

#### 一。电路

```
1.电容，电感元器件（25-27）
https://www.bilibili.com/video/BV1as411H7Ya?p=25
2.一阶，二阶电路（28-34）
3.一阶，二阶电路阶跃响应（35-36）
4.复数，正玹量，相量法基础，电路定律向量形式（37-40）
5.正玹电路分析
6.含有耦合电感的电路
7.电路的频率响应
```

#### 二。模电
```
1.Q点计算
	https://www.bilibili.com/video/BV1Gt411b7Zq?p=14
2.复合电路
3.多级放大电路
4.频率响应
	数学（对数）
	H参数与π模型
5.负反馈
```

#### 三。实验

```
1.二极管特性研究(模拟电子page 63)
2.基础电路（电子工程师，自学速成2版，page 257）
	1.1）放大电路
	1.2）谐振电路（LC），移相电路（RL）
	1.3）振荡器
		振荡器是一种“产生交流信号的电路”
	1.4）电源电路
3.电容，电感特性研究(Multisim12仿真设计，page 177)
4.基本电路
	4.1）RC(电容电路)，RL（电感电路），RLC(电感，电容电路)
```

#### 四。问题

```
1.没有万用表，电感感值测量？？？
	电感线圈制作，实验1
	万用表没有电感档，通过简单电路推算电感值（先推算电感感抗）
	https://www.bilibili.com/video/BV1424y1y7j1
2.什么是阻抗和感抗？？？
	在具有电阻、电感和电容的电路里，对交流电所起的阻碍作用叫做阻抗。
	阻抗由电阻、感抗和容抗三者组成，但不是三者简单相加。阻抗的单位是欧。
2.直流怎么样变成交流？？？
	直流电可以通过逆变器变成交流电。逆变器通常由逆变桥、控制逻辑和滤波电路组成。
	https://www.elecfans.com/d/1070671.html
3.什么是输入电阻，输出电阻，内阻？？？
	https://www.bilibili.com/video/BV1cu411m7Ro
	https://www.bilibili.com/video/BV15B4y1b7nY
	实验2
4.什么是高通，低通电路
	https://zhidao.baidu.com/question/497077057.html
	简单而言，低通就是低频信号可以通过，高频信号被滤掉，高通为高频信号可以通过，低频被隔掉，但是根据不同频率，滤除效果不一样。
```

