# 1.数字与码制

```
第1章
			一.二，十，八，十六位数相互转换
1.二进制(节省设备)
	见：jssb.jpg
2.二进制 转 十进制数
	见：ejzsj.jpg(小数点)
3.十进制转二进制
	除2取余法
4.十进制小数 转 二进制
	乘2取整法
5.八进制,十六进制数与二进制数的转换
	·八进制转二进制
	按照八进制每一位二进制(三位一组)按照顺序组合起来(高位不够用0补)
	·十六进制转二进制
	按照十六进制每一位二进制(四位一组)按照顺序组合起来(高位不够用0补)
	·二进制数转换八进制,十六进制数
	按照前面相反转换过去
------------------------------------------------------------------------------------------
			二.编码
数字电路中信息在处理时均需要转换为0或1的代码及二进制码，码制编制代码时所遵循的规则。
常用的码有 符号数编码，十进制数编码，和字符编码

·符号数编码
	八位二进制表示一个数时，最高位为符号位，其余7位是数值位。
	0表示的符号位有两种表示方式，一种正0，另一种负0
	1.原码
	数字位保持不变，只在最高位添加符号位1表示-，0表示+
	2.反码
	正数：与原码保持不变
	负数：原码的数值位都变反，在数值位的高位再插入一位1
	[-10] 原=1001010
	[-10] 反=11110101
	3.补码
	正数：与原码保持不变
	负数：原码转反码后，数值位加1
	[-10] 原=1001010
	[-10] 补=11110110
	上面三种码最终目的是为了让减法能够用加法实现。
·十进制数编码
	常见十进制数编码有
	1.8421BCD
	2.5421BCD
	3.2421BCD
	4.5211BCD
·字符编码
	数字电路中各种文字符号和图形非数字值信息也要转换为二进制码，常用字符编码为ASCII码，
	即美国信息交换标准码。
------------------------------------------------------------------------------------------
			三.机器码(原码,补码,反码)加减运算:
题目：N1=-0011,N2=+1011求[N1+N2]和[N1-N2]
1)原码
	规则:ymys.jpg
	加[N1+N2]:
	因为N1是负的，等式又：[N2-N1]
	符号:取绝对值大的
	减[N1-N2]:
	因为N1是负的，等式又：[N1-N2]
	符号:取绝对值大的
2)补码
	规则:bmys.jpg
	符号位有进位的话，需要丢弃。
	一般计算机中是以补码形式进行运算的，可以对减法以加法运算
3)反码
规则:fmys.jpg
与补码不同的是，当符号有进位时，应在符号的最低位再加1(循环进位)
补码的补充说明：
	bmbc.jpg
·十进制_补数：(n是整数的位数，m是小数的位数)
	负数：用9表示
	正数：用0表示
	整数求补(负)：10的n次方-（原数绝对值）
	小数求补(负)：10-（原数绝对值）
	除了负数用9表示以外，其它的规则都可以按照之前的来
	十进制补数运算_和前面的规则一样
·十进制反码(也称该数对9的补数）
	(n是整数的位数，m是小数的位数)
	小数求补(负)：10的n次方-10的m次方-（原数绝对值）
	整数求补(负)：10的n次方-1-（原数绝对值）
------------------------------------------------------------------------------------------
			三.了解定点于浮点数的基本概念，掌握常用的几种编码
	定点于浮点
	定点：表示小数点的位数固定不变的数叫定点
	浮点：表示小数点的位数是不固定的
	浮点规则：ddyfd.jpg
	浮点数的运算规则：fdsys.jpg
------------------------------------------------------------------------------------------
			四.十进制用二进制的表示方法:
·8421码
	加减运算规则(进位规则)：加法+6调整，加法-6调整
	8421码0111=0*8+4*1+1*2+1*1=7
·余三码
	由8421码加3形成的一种无权码，0用0011表示
	特点(对9的自补码)与8421比不用循环进位
	(4)余三码=0111;(5)余三码=1000
	(0111)9补=1000(即按位取反)
	加减运算规则(进位规则)：如果两个余三码相加没有进位，则和数要减3，
	否则和数要加3（如果不加就是正确的十进制数二进制表示方法）
·2421 BCD码：2421.jpg
·2421 编码方案：2421fa.jpg
------------------------------------------------------------------------------------------
			五.可靠性编码：
能减少错误，发现错误，甚至纠正错误的编码称为可靠性编码。
·格雷码
	在一组数的编码中，如果任意相邻的代码只有一位二进制数不同，即称为格雷码。主要应用在循环计数当中。
	格雷码有很多种,典型的。
	二进制转格雷码：ezg.jpg(相同的为0，不相同为1)
	格雷码转二进制：gze.jpg(相同的为0，不相同为1)
·奇偶校验码
	传输过程中经常用到的。奇偶校验码由信息位(最高位是信息位，其余是校验位)和校验位组成，就是检查"校验位"1的个数。在传输中只能够检查奇数位错误,但不能检查偶数位错误。可以检查传输中的错误但不能够定位错误.
·海明码
	传输过程中经常用到的。与奇偶校验码相比 可以检查到错误并定位到错误，但只能够检测到一位错误。
·字符编码
	美国标准信息交换代码ASCII(7位)有A,B,C a,b,c等...
	注意:字符的9与数字的9是不相同的.
```
