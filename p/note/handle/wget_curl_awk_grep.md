# wget_curl_awk_grep

```
wget
	wget -bc url	//-b:表示后台下载,-c:断点续传,cat wget-log 查看记录
	https://www.cnblogs.com/evercx/p/5327478.html	//后台下载
	
	递归下载
		wget -c -r -np http://192.168.157.137:84/ftp/		//后面跟"/"
		
		实例:wget -c -r -nd -np -k -L -p -A -P dir c,h http://192.168.157.137:84/ftp/
		
		-c 断电续传
		-r 递归下载
		-nd 下载时不创建一层一层的目录
		-np 把所有的文件下载到当前目录 -np 递归下载时不搜索上层目录
			没有加参数-np，就会同时下载path的上一级目录pub下的其它文件
		-k  将绝对链接转为相对链接
		-L	递归时不进入其它主机
			如果网站内有一个这样的链接www.yyy.org
			不加参数-L,就会像大火烧山一样,会递归下载www.yyy.org网站
		-p  下载网页所需的所有文件,如图片等
		-A c,h 指定要下载的文件样式列表,多个样式用逗号分隔
		-P 指定下载到的目录

		-i 后面跟一个文件，文件内指明要下载的URL 

curl
	curl --form "userfile=@/Users/username/Downloads/20170502.zip" http://youip/up.php
	或
	curl -F "userfile=@/Users/username/Downloads/20170502.zip" http://youip/up.php
	
	指定get与post
		get方式：
			curl "http://ip:port/up.php?param1=value&param2=value2"
		post方式:
			curl -d "param1=value&param2=value2" http://ip:port/up.php
		模拟form:
			curl -F "image=@/1.png" http://ip:port/up.php
			or
			curl --form "image=@/1.png" http://ip:port/up.php
			
			"image=@/1.png"
			image 相当于<input type='file' name='image'>中name的value   
			@后面是文件路径
			
		自定义head头信息:
			curl -H "example:xxxxxxxxxx" http://ip:port/up.php
			
		post提交文件:
			curl proxy.liyuan3210.com:10802/api/ud/upload -F "file=@/home/liyuan/share/nginx-1.12.2.tar.gz"
			
			curl 192.168.157.139:83/ud/upload -F "file=@/home/liyuan/soft/nginx-1.12.2.tar.gz"
			
			curl proxy.liyuan3210.com:10802/api/ud/upload -F "file=@/home/liyuan/soft/nginx-1.12.2.tar.gz"
			curl: (6) Could not resolve host: proxy.liyuan3210.com
		
			
		https://www.cnblogs.com/dongkang/p/6150356.html
		https://blog.csdn.net/fungleo/article/details/80703365

	
	
grep介绍
		https://blog.csdn.net/weixin_42193400/article/details/82255541
		位置锚定：
		grep "Destroying" test.txt 或 grep Destroying test.txt
		
		查询字符串出现次数
		grep -c "Destroying" test.txt
		
		当前目录及其子目录搜索
		grep -r "Destroying" *
		
		https://blog.51cto.com/12384628/2162402
		grep 9个经典使用场景
		https://blog.csdn.net/M________123/article/details/79521884
	
	
awk介绍使用
		http://www.ruanyifeng.com/blog/2018/11/awk.html
		awk '{print $0}' test.txt	//输出文件
		
		echo 'this is a test' | awk '{print $1}'	//管道文件输出
		print $0 输入 this is a test
		awk会根据空格和制表符，将每一行分成若干字段，
		依次用$1、$2、$3代表第一个字段、第二个字段、第三个字段等等。
		
		文件demo.txt
		root:x:0:0:root:/root:/usr/bin/zsh
		daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
		bin:x:2:2:bin:/bin:/usr/sbin/nologin
		sys:x:3:3:sys:/dev:/usr/sbin/nologin
		sync:x:4:65534:sync:/bin:/bin/sync
		
		awk -F ':' '{ print $1 }' demo.txt	//使用`:`分割
		
scp命令 linux之间文件传送
    本地file上传到远程:
    第一个命令：scp -r /home/oracle/db/20121030.dmp  root@10.20.14.17:/home
    第二个命令：yunda

    远程file下载到本地：
    scp -r root@10.20.14.17:/root/index.html /home/oracle/
    第二个命令：yunda
	
	远端复制到远端主机:
	scp user1@host1:file1 user2@host2:file

    注意：-r 表示包括子目录
	指定端口,用户名称
	scp -P 6000 file.txt liyuan@192.168.1.1:/home/liyuan
	
cat与tail命令
	查看后几行命令使用
		https://www.cnblogs.com/suanec/p/5881463.html
		cat file tail -n 1000：显示最后1000行
		cat file	tail -n +1000：从1000行开始显示，显示1000行以后的
		cat file	head -n 1000：显示前面1000行
	log文件实时显示
		tail -f access.log
```

