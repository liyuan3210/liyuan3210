# nginx

官网地址 

http://nginx.org

## 一．nginx安装

1. windows安装

``` 
解压安装nginx-1.12.0

	windows后台启动
		cd ..\nginx-1.12.0
		nginx -c conf\nginx.conf

	windows关闭
		cd ..\nginx-1.12.0
		nginx -s stop

	验证nginx启动成功
		http://localhost
```

2. linux ubuntu安装

```
实例
		https://www.cnblogs.com/EasonJim/p/7806879.html

	1.基于apt安装
		linux ubuntu配置注意:

		更新包
		sudo apt-get update

		安装
		sudo apt-get install nginx

		配置nginx.conf
		1>.user liyuan;(目录所属用户)
		2>.server 下面加上 autoindex on;(貌似可以不加)
		按照下面配置:
		虚拟目录配置(alias与root)

		目录介绍
		/usr/sbin/nginx：主程序
		/etc/nginx：存放配置文件(nginx.conf)
		/usr/share/nginx：存放静态文件
		/var/log/nginx：存放日志
		
		相关操作
		service nginx start/stop/restart

	2.源码编译安装
		依赖包安装:
		安装gcc g++的依赖库
		sudo apt-get install build-essential
		sudo apt-get install libtool

		安装pcre依赖库（http://www.pcre.org/）
		sudo apt-get update
		sudo apt-get install libpcre3 libpcre3-dev

		安装zlib依赖库（http://www.zlib.net）
		sudo apt-get install zlib1g-dev

		安装SSL依赖库（16.04默认已经安装了）
		sudo apt-get install openssl

		解压
		tar -xzvf nginx-1.12.2.tar.gz

		配置
		./configure --prefix=/home/liyuan/soft/nginx

		编译
		make

		安装
		make install

		#启动：
		sudo /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf

		alias与root不同
		https://www.cnblogs.com/yanghj010/p/5772652.html
```

## 二．实例使用



**1.负载配置，**保存为文件为testLock.conf:

一台机器跑两个应用，分别负载到7001与7002

```
#weigth参数表示权值，权值越高被分配到的几率越大
upstream lockhello {
  server 127.0.0.1:7001 weight=1;
  server 127.0.0.1:7002 weight=1;
}
server {
  listen 83;
  server_name localhost;
  location /api {
    root html;
    index index.html index.htm;
    proxy_pass http://lockhello/;
  }
}
```

**2.虚拟目录配置（**可在浏览器显示，下载文件），保存文件为virdir.conf:

```
# 虚拟目录访问
server {
  listen 81;
  server_name localhost;
  location /tool {
    alias /home/liyuan/Desktop/tool;
    autoindex on; # 开启目录浏览功能(必须的)
    autoindex_exact_size off; #关闭详细文件大小统计，让文件大小显示MB,GB单位,默认为B
    autoindex_localtime on; #开启以服务器本地时区显示文件修改日期！
  }
}

# 静态资源访问
server {
  listen 82;
  server_name 127.0.0.1;
  location /lock {
    alias /var/www/html;
  }
}
```

**root路径配置**（静态页面访问）：

```
server {
  listen 82;
  server_name localhost;
  location /docs {
    root /home/liyuan/Desktop/tool/env/midd/apache-zookeeper-3.6.2-bin;
    index index.html index.htm;
  }
}
下面访问：
http://127.0.0.1:82/docs/index.html
实际访问本地路经是：/home/liyuan/Desktop/tool/env/midd/apache-zookeeper-3.6.2-bin/docs/index.html
```

**3.代理:**

https://blog.csdn.net/qw_xingzhe/article/details/79580800
正向(正向代理不支持https):
代理内部网络对internet请求

反向(常用的类型):
将internet请求转发给内部网络

保存文件为agent_http.conf

```
server {
	listen 86;
	server_name localhost;
	
	location / {
		#proxy_redirect off;
		#proxy_set_header Host $host;
		#proxy_set_header X-Real-IP $remote_addr;
		#proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
		proxy_pass http://192.168.157.137:82/;
		index index.html index.htm;
	}
	location /mysqlAdmin {
		proxy_pass http://192.168.157.139:81/mysqlAdmin/;
		#index index.html index.htm index.php;
	}
	location /api {
		proxy_pass http://192.168.157.139:83/;
	}
	location /ftp {
		proxy_pass http://192.168.157.137:84/;
	}
	#access_log tomcat1.log;
}
注意:
proxy_pass 配置必须是http://ip:port/后面"/"斜杠不能少
访问时http://ip:port/后面要跟"/"否则可能有问题
```

## 三．nginx配置https

```
nginx http代理https
		https://segmentfault.com/q/1010000006994159

		https
		https://www.cnblogs.com/tianhei/p/7726505.html

		配置方法
		https://www.cnblogs.com/kevingrace/p/6187072.html
		
	ssl介绍
	https://aotu.io/notes/2016/08/16/nginx-https/index.html
	1.https证书种类
		1>自签名SSL证书
			1）自签证书是免费的 
			2）自签证书相对申请CA证书，流程更简单 
			3）自签证书同样可以对数据进行加密 
			4）自签证书的有效期可以设置很长，免去续签的麻烦 
			5）自签证书更方便测试，比如说你想生成多少个不同服务器ip的都可以 
			所以对于一些个人开发者来说使用自签证书可能会更方便，只要你能接受别人浏览你网站时弹出的提醒：不安全

		2>CA证书
			有很多网站能申请CA证书，如沃通，腾讯，阿里云等，大家可根据自己的喜好去申请 
			CA证书又分免费和不同价钱的，当然一分钱一分货，对于我们个人开发者来说，免费的CA证书就足够了， 
			我个人主要是想解决当别人访问我的https网站时弹出“不安全”的这个问题，因为“不安全”这个提示被人感觉很像钓鱼网站 :P 
			下面主要讲的是去阿里云申请免费型DV SSL证书，此种证书是每个证书绑定一个域名，如有多个域名可申请多个证书 
			
	2.证书生成
		自己生成证书
			https://www.cnblogs.com/vincent-li666/p/5851463.html
			https://blog.csdn.net/gary_yan/article/details/77973711
		腾讯云申请免费证书
			1.填写域名证书申请proxy.liyuan3210.com
			2.域名支持TXT记录,配置TXT记录值(申请时产生)
			_dnsauth.proxy	TXT记录	通用	201907030700193xtsqjxw65m3v2722lge9smsyrv2lr5mijq6p7uz5p4gxktugx	
			3.提交测试,通过后下载证书文件配置nginx
		中间通过花生壳内网穿透跳转
		server {
			 listen 87; #SSL 访问端口号为 443
			 server_name 127.0.0.1; #填写绑定证书的域名
			 ssl on; #启用 SSL 功能
			 ssl_certificate 1_www.domain.com_bundle.crt; #证书文件名称
			 ssl_certificate_key 2_www.domain.com.key; #私钥文件名称
			 ssl_session_timeout 5m;
			 ssl_protocols TLSv1 TLSv1.1 TLSv1.2; #请按照这个协议配置
			 ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE; #请按照这个套件配置，配置加密套件，写法遵循 openssl 标准。
			 ssl_prefer_server_ciphers on;
			 location / {
				 root /var/www/www.domain.com; #网站主页路径。此路径仅供参考，具体请您按照实际目录操作。
				 index  index.html index.htm;
			 }
		 }
		
		HTTP 自动跳转 HTTPS 的安全配置（可选）
			Nginx 支持 rewrite 功能。若您在编译时没有去掉 pcre，
			您可在 HTTP 的 server 中增加 rewrite ^(.*) https://$host$1 permanent;，
			即可将默认80端口的请求重定向为 HTTPS。修改如下内容
			server {
				listen 443;
				server_name www.domain.com; #填写绑定证书的域名
				ssl on;
				root /var/www/www.domain.com; #网站主页路径。此路径仅供参考，具体请您按照实际目录操作。
				index index.html index.htm;   
				   ssl_certificate  1_www.domain.com_bundle.crt; #证书文件名称
				ssl_certificate_key 2_www.domain.com.key; #私钥文件名称
				ssl_session_timeout 5m;
				ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
				ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
				ssl_prefer_server_ciphers on;
				location / {
				   index index.html index.htm;
				}
			}
			server {
				listen 80;
				server_name www.domain.com; #填写绑定证书的域名
				rewrite ^(.*)$ https://$host$1 permanent; #把http的域名请求转成https
			}
	3.https配置(花生壳内网映射直接映射443端口)
		https://www.cnblogs.com/yun007/p/3739182.html
		https://www.cnblogs.com/yang-wu/p/5107899.html
		
		server {
			listen       443 ssl;
			server_name  127.0.0.1;
			
			ssl_certificate      /home/liyuan/soft/keyfile/lee.crt;
			ssl_certificate_key  /home/liyuan/soft/keyfile/lee.key;
			
			ssl_session_cache    shared:SSL:1m;
			ssl_session_timeout  5m;
			
			ssl_ciphers  HIGH:!aNULL:!MD5;
			ssl_prefer_server_ciphers  on;

			location / {
					proxy_pass http://192.168.157.137:82/;
					index index.html index.htm;
			}
			location /mysqlAdmin {
					proxy_pass http://192.168.157.139:81/mysqlAdmin/;
					index index.html index.htm index.php;
			}
			location /webssh {
                    proxy_pass https://192.168.157.139:85/;
            }
			error_page 497 https://$host:10802$uri;
		}
		
		注意:
		failed (SSL: error:02001002:system library:fopen:No such file or directory:fopen(
		可以把lee.crt,lee.key文件放在系统/etc/nginx/目录下面(chmod 755 lee.*两个文件)
		否则nginx日志文件会报错
	
	4.请求重写(所有http跳转重写到https)
		1.比较老的写法
			rewrite ^(.*)$  https://$host$1 permanent;   //这是ngixn早前的写法，现在还可以使用。
			也可以改为下面
			rewrite ^/(.*)$ http://dev.wangshibo.com/$1 permanent;
			或者
			rewrite ^ http://dev.wangshibo.com$request_uri? permanent;
		2.最新的写法
			return 301 https://$server_name$request_uri;      //这是nginx最新支持的写法
		3.多域名写法
			if ($host ~* "^wangshibo.com$") {
				rewrite ^/(.*)$ https://dev.wangshibo.com/ permanent;
			}
			简写
			if ($host = "dev.wangshibo.com") {
			   rewrite ^/(.*)$ http://dev.wangshibo.com permanent;
			}
	5.nginx497状态码(当网站只允许https访问时，当用http访问时nginx会报出497错误码)
		error_page 497  https://$host$uri?$args;
			
			
	主流负载均衡比较(haproxy与nginx)
	https://blog.csdn.net/dylan_csdn/article/details/51241047

	haproxy官网
	https://www.haproxy.org/
```

## 四.推流配置

推流模块

https://github.com/arut/nginx-rtmp-module

一。nginx安装

https://blog.csdn.net/wq892373445/article/details/103969999

推流安装

https://www.cnblogs.com/wintertone/p/13235802.html（包括已安装nginx，安装模块）

https://www.cnblogs.com/boonya/p/7910967.html



nginx卸载安装

https://www.cnblogs.com/ryanzheng/p/11263031.html

安装环境

yum -y install make gcc

安装必须

yum -y install zlib zlib-devel openssl openssl-devel pcre pcre-devel



安装配置

./configure --prefix=/home/liyuan/soft/nginx  --add-module=/home/liyuan/soft/nginx-rtmp-module-1.2.1  --with-http_ssl_module --with-cc-opt="-Wimplicit-fallthrough=0"



**问题:**

nginx-rtmp-module-1.2.1/ngx_rtmp_eval.c:160:17: error: this statement may fa

https://blog.csdn.net/yga_airspace/article/details/106576678

./configure后面添加 --with-cc-opt="-Wimplicit-fallthrough=0"



nginx已安装的怎么配置???



**nginx配置：**

```
rtmp {
    server {
        listen 1935;
        chunk_size 4000;
        application hls {
            live on;
            hls on;
            hls_path /home/liyuan/soft/nginx/hls;
            hls_fragment 5s;
        }
    }
}
```

