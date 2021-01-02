# 一.马士兵课堂

官网：

http://www.mashibing.com

vip课程：

http://www.mashibing.com/vip.html

在线课程：

https://ke.qq.com/webcourse/index.html#cid=399017&term_id=100475965&taid=3608794731255465&from=41

官网git：

http://git.mashibing.com

https://github.com/bjmashibing	(old github)

```
git clone http://39.101.213.88/bjmashibing/tank.git
git clone http://39.101.213.88/bjmashibing/JUC.git
git clone http://39.101.213.88/bjmashibing/Flink-Study.git
git clone http://39.101.213.88/bjmashibing/bjmashbing-sysio.git
git clone http://39.101.213.88/bjmashibing/Machine-Learning.git
git clone http://39.101.213.88/bjmashibing/Recommender.git
git clone http://39.101.213.88/bjmashibing/JVM.git
git clone http://39.101.213.88/bjmashibing/BigDataArchitect.git
git clone http://39.101.213.88/bjmashibing/TankNettyStudy.git
git clone http://39.101.213.88/bjmashibing/javaweb.git
git clone http://39.101.213.88/bjmashibing/Algorithm.git
git clone http://39.101.213.88/bjmashibing/TestJMH.git
git clone http://39.101.213.88/bjmashibing/mashibingdisrutpor.git
git clone http://39.101.213.88/bjmashibing/DesignPatterns.git
git clone http://39.101.213.88/bjmashibing/InternetArchitect.git
git clone http://39.101.213.88/bjmashibing/online-taxi-file.git
git clone http://39.101.213.88/bjmashibing/algorithmbasic2020.git
git clone http://39.101.213.88/bjmashibing/java.git
```

课程

```
1、VIP地址资料链接： http://www.mashibing.com/vip.html
2、课程安排链接：      https://s1.mashibing.com/selectuser

https://github.com/bjmashibing
http://git.mashibing.com/bjmashibing
http://git.mashibing.com/users/sign_in
full name: liyuan
UserName:liyuan3210
email:li.yuan3210@163.com
pwd:adm123456
```

### 周志磊笔记

```
IO实操：
df			//查看磁盘设备
/boot目录挂载										//增加磁盘可以覆盖挂载

dd if=/dev/zero of=mydisk.img bs=1048576 count=100		//生成虚拟文件系统
/////////dd 文件备份？？？
losetup /dev/loop0 mydisk.img							//挂载到环形设备上
mke2fs /dev/loop0									//格式化
df		//没有的
mount -t ext2 /dev/loop0 /mnt/ooxx			//添加到df下面

cp /bin/bash bin	//拷贝bash程序
ldd bash 	//查看bash程序启动需要的动态链接库
cp /lib64/{libtinfo.so.5,aaa,xxx}		//拷贝文件
chroot ./							//当前目录为根目录启动
echo $$							//打印当前bash进程id



lsof -op $$		//当前bash进程打开了哪些文件
exec 8< ooxx.txt   //8文件描述符
cd /proc/$$/fd	//进入文件描述符，记录文件信息
read a 0<& 8		//8表示文件描述符id
echo $a

输入输出重定向
ls ./
ls ./ 1> ~/ls.out	//输出重定向
head tail | 管道

父子进程
--------------------------------------
vm.dirty内核设置？？？
```



#### 区块链

入门课程eos

https://www.bilibili.com/video/av79054667?p=1

区块链发展1.0至3.0

https://baijiahao.baidu.com/s?id=1607673651921555611&wfr=spider&for=pc

#### k8s

https://www.bilibili.com/video/BV1ib411Y7zz?p=3

# 二.视频课程

视频学习网站:
	https://ke.qq.com/
	http://www.bjsxt.com/
	http://edu.51cto.com/

	区块链课程
		http://i.youku.com/i/UMzUxMzkxMDc2/videos?spm=a2hzp.8244740.0.0
		http://www.zpedu.org/Info-neirongye-430_127.html
		https://www.bilibili.com/video/av31087889/?p=1
		https://www.yitaifang.com/	//区块链
		https://blog.csdn.net/wjx342529/article/details/70148987
		区块链黄埔军校
			http://www.hainayunfan.com
	
	人工智能
		https://study.163.com/course/introduction/1005692008.htm
		
	linux内核
		https://ke.qq.com/course/167847
		linux binutils
			https://www.crifan.com/files/doc/docbook/binutils_intro/release/html/binutils_intro.html
			
	张孝祥视频
		http://www.icoolxue.com/album/show/38/
	
	p2p理论
		https://www.bilibili.com/video/av25760470/?redirectFrom=h5