# squid服务器代理

```
官网:
	http://www.squid-cache.org/
	一.安装
	sudo apt-get install squid

	vi /etc/squid/squid.conf
	1.在文件中搜索http_port 3128(修改服务器端要监听的端口，
	也可以不改，不过这个端口要记住，浏览器设置代理时要用。)

	2.设置所有用户都可以访问这个代理
	找到 http_access deny all 将其修改为 http_access allow all

	3.注释放开(缓存目录)
	cache_dir ufs /var/spool/squid 100 16 256

	4.设置允许访问的ip段，服务器默认不代理任何客户端(可以不调整)
	acl CONNECT method CONNECT
	acl lanhome src 10.0.0.0/24 //添加这一行
	acl all src 0.0.0.0/0.0.0.0	//所有ip都能访问

	5.重启服务
	service squid restart
	
	日志目录
	/var/log/squid/

	网上实例
	https://blog.csdn.net/blog_liuliang/article/details/53997852

	配置密码
	https://blog.csdn.net/qingfengxulai/article/details/80853870

	允许所有ip
	https://www.cnblogs.com/bluestorm/p/9032086.html


	http_port 3128  
	可以修改为
	http_port 192.168.3.171:3128

	主机名称
	visible_hostname
```

