# 网络OSI

## 一．通信模型流程

## 1.OSI参考模型

![OSI模型](img/osi.png)

## 2.tcp/ip五层模型

![](img/tcp-process.png)



**三次握手**

​		 1.A->B(A向B发送连接请求)

​		 2.A<-B(B应答A发送一个ok请求)

​		3.A->B(A向B发送确认连接请求并建立连接)

**四次分手**

​		1.A->B(A向B发送断开连接请求)

　　2.A<-B(B应答A发送一个ok请求)

​	    3.B->A(B向A发送断开连接请求)

​        4.B<-A(B收到Ａ应答,并断开链接．需要双方知晓并回应才可以断开链接)

**三次握手>数据传输>四次分手，成为一个最小粒度，不可被分割**

## 3.linux命令验证

cd /proc/$$/fd

exec 8<> /dev/tcp/www.baidu.com/80		//建立连接

echo -e 'GET/HTTP/1.0\n'							 //传送数据（http：规范标准）

cat <& 8														　//最终演示的是一个应用层协议

##  4.下一调机制

![](img/next-tiao.png)

传输层：

netstat -natp			//查看本机ip:port与外围　ip:prot　通信列表

网络层：

route -n 					//查看ip路由表

链路层：

arp -an						//mac地址

### 5.LVS的DR,TUN,NAT模型推导

