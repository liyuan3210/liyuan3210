# 1.ipv4寻址与子网划分

见原理图

## 一．Ａ，Ｂ，Ｃ类	地址

1-126.x.y.z/8	255.0.0.0		//A类，前８位表示网络位

128-191..x.y.z/16		255.255.0.0	//Ｂ类，前16位表示网络位

192-223..x.y.z/24		255.255.255.0　//C类，前24位表示网络位

### 特殊ip

１．127.0.0.1：本地回环地址

２．255.255.255.255：广播地址

３．0.0.0.0：表示任何网络

３．**私有ip地址????**：

​		.A类地址中的：10.0.0.0　到　10.255.255.255.255

​		.Ｂ类地址中的：172.16.0.0　到　172.31.255.255

​		.Ｃ类地址中的：192.168.0.0　到　192.168.255.255

ipv6中为０的部分可以简写即ping 127.1可以ping通

## 二.子网划分（无类网络）

​		按照上面常规划分，会存在一些问题，广播风暴，为了解决这个问题就需要进行子网划分．

​		划分方法：借用主机位，制造新的网络

### 划分子网方法:

1.你所选择的子网掩码将会产生多少个子网？

​		２的x次方（x代表子网掩码位数）

2．每个子网能有多少主机？

​		2的y次方-2（y代表主机位数）

3.有效子网是？

​		有效子网号＝256 - 10进制的子网掩码（结果叫做block size 或　base number）

4.广播号码

​		广播号码　＝　下一个子网号 - 1

### **1.B类网络子网划分,借8位主机号**

主机　		172.16.2.160

子网掩码　255.255.**255**.0

网络号		172.16.2.0

在Ｂ类网基础借位主机位８位,可划分254个子网，每个子网254主机

### **2.B类网络子网划分,借10位主机号**

主机　		172.16.2.160

子网掩码　255.255.**255.192**

网络号		172.16.2.128

在Ｂ类网基础借位,可划分1023个子网，每个子网63主机

#### 1)手动推算１(向主机借2位)

172.16.2.x

00 000000		//子网0

01 000000		//子网64

10 000000		//子网128

11 000000		//子网192

##### 得出(1024ip子网)

172.16.0.0	/	255.255.255.192

172.16.0.64	/	255.255.255.192

172.16.0.128	/	255.255.255.192

172.16.0.192	/	255.255.255.192



172.16.1.0	/	255.255.255.192

172.16.1.64	/	255.255.255.192

172.16.1.128	/	255.255.255.192

172.16.1.192	/	255.255.255.192



172.16.2.0	/	255.255.255.192

172.16.2.64	/	255.255.255.192

172.16.2.128	/	255.255.255.192

172.16.2.192	/	255.255.255.192

一只到255.......

##### 得到广播号码(下一个网络号减1)

172.16.0.0	/	255.255.255.192	/	172.16.0.63	

172.16.0.64	/	255.255.255.192	/	172.16.0.127

172.16.0.128	/	255.255.255.192	/	172.16.0.191

172.16.0.192	/	255.255.255.192	/	172.16.0.255

#### 2)手动推算２(向主机借3位)

111　00000　255.255.255.224	256 - 224　= 32

172.16.0.0	/	255.255.255.224	/	172.16.0.31

**172.16.0.32**	/	255.255.255.224	/	172.16.0.63

172.16.0.64	/	255.255.255.224	/	172.16.0.95

172.16.0.96	/	255.255.255.224	/	172.16.0.127

172.16.0.128	/	255.255.255.224	/	172.16.0.159

172.16.0.160	/	255.255.255.224	/	172.16.0.191

172.16.0.192	/	255.255.255.224	/	172.16.0.223

172.16.0.224	/	255.255.255.224	/	172.16.0.255

#### 如何确定超网？

将子网列出来，找到相同的部分．

子网划分(网络位向主机位借位)

超网划分(主机位向网络位借位)

## 三.CIDR（无类别遇见路由）与VLSM（可变长子子网掩码）

CIDR:支持无类别路由协议？？？

VLSM:　？？？

## 四．xxxx

### 网络配置

ip地址（必须）192.168.0.25			//网络号192.168.0.0

子网掩码（必须）255.255.255.0

默认网关 (非必填) 当要连接非我所在网段跨网络时才要填　192.168.0.1

DNS服务器

### 网络测试工具

​	ping -t www.baidu.com	//不间断发送

​	ping -l 65500 www.baidu.coom  //指定发送数据包大小

### 网络测试工具２

​	　ipconfig