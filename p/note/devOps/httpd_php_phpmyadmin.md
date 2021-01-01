# httpd_php_phpmyadmin

```
windows上的安装配置

源码地址:http://httpd.apache.org
php官网:http://php.net
phpmyadmin官网:https://www.phpmyadmin.net

下载
php:
http://windows.php.net(要下载Thread Safe类型的)
httpd:
http://www.apachelounge.com(源码地址http://httpd.apache.org)
wordpress:
https://wordpress.org
phpmyadmin:
https://www.phpmyadmin.net

安装(windows环境)
解压安装httpd,php到d:/soft:
cd httpd>Apache24/bin
>
httpd.exe -k install(后台服务名为Apache2.4)

插件(https://www.apachelounge.com/download可以进行下载)
windows下需要安装vc_redist.x64.exe插件

php环境变量(;C:\soft\php-7.1.7\ext[貌似可以不加进去])
path:;C:\soft\php-7.1.7

配置httpd.conf
1.配置目录(注意)
在../Apache24/conf/httpd.conf
#配置基础(里面所有默认c:/Apache24的目录都需要改成实际的地址)
ServerRoot "C:/soft/Apache24"
Listen 8080

2.配置php支持
# php7 support
LoadModule php7_module C:/soft/php-7.1.7/php7apache2_4.dll
AddType application/x-httpd-php .php .html .htm
# configure the path
PHPIniDir "C:/soft/php-7.1.7"

3.DirectoryIndex默认的要配置上index.php(不然wordpress部分跳转会有问题)
<IfModule dir_module>
    DirectoryIndex index.html index.php
</IfModule>

4.DocumentRoot配置
DocumentRoot "C:/soft/Apache24/htdocs"
<Directory "C:/soft/Apache24/htdocs">
    Options Indexes FollowSymLinks
    AllowOverride None
    Require all granted
</Directory>

如果服务启动不起来直接在../Apache24/bin里面执行httpd服务命令，可以看到详细的错误信息

配置php.ini
从../php-7.1.7拷贝一个php.ini-production重命名为php.ini在里面添加如下内容:
;每个脚本的最大执行时间，默认30秒，0表示无限制
max_execution_time = 1800
;设置PHP可接收的最大POST数据大小
post_max_size = 2M;每个脚本的最大执行时间，默认30秒，0表示无限制
max_execution_time = 1800
;设置PHP可接收的最大POST数据大小
post_max_size = 2M
#从这里可以直接copy进去
;设置载入模块的路径
extension_dir = "C:/soft/php-7.1.7/ext"
;设置HTTP上传文件的临时文件存放的位置
upload_tmp_dir = "C:/soft/php-7.1.7/upload"
;指定一些自动载入的模块
extension = php_curl.dll
extension = php_gd2.dll
extension = php_mbstring.dll
extension = php_mysql.dll
extension = php_mysqli.dll
extension = php_pdo_mysql.dll
extension = php_pdo_odbc.dll
extension = php_xmlrpc.dll
; 设置默认时区
date.timezone = Asia/Shanghai
; 设置事务存放路径
session.save_path = "C:/soft/php-7.1.7/session"

验证
一般都是把工作项目文件放到../Apache24/htdocs下面,在里面新建index.php文件里面内容如下：
<?php phpinfo()?>

在浏览器输入地址:http://127.0.0.1:8080
ok如果显示php信息表示httpd,php安装成功

wordpress安装
解压wordpress,复制一份wp-config-sample.php(不能直接重命名)
并重命名为wp-config.php(配置里面的数据库连接)

账户名：liyuan
密码：7233354liyuan
主题:Zerif Lite
统计插件：slimstat

安装phpmyadmin:
解压phpmyadmin包至../Apache24/htdocs下面,
配置../phpmyadmin/libraries/config.default.php文件
必须配置项：
$cfg['Servers'][$i]['host'] = 'localhost';//mysql主机信息
$cfg['Servers'][$i]['port'] = '3316';//mysql端口

不是必须配置项：
服务协议配置
$cfg['Servers'][$i]['auth_type'] = 'http';
$cfg['PmaAbsoluteUri'] = "http://liyuan3210.iask.in/phpMyAdmin';//访问网址
帐号与密码
$cfg['Servers'][$i]['user'] = 'adm';
$cfg['Servers'][$i]['password'] = '123';
编码配置
$cfg['DefaultLang'] = 'en-utf-8';
$cfg['DefaultConnectionCollation'] = 'utf8_general_ci';

问题：
1.电子邮件未能发送。
可能原因：您的主机禁用了mail()函数。

2.博客数据迁移问题

BuHDt11o@163.com
7233354liyuan

3.后期更换域名后无法访问解决方法
https://www.douban.com/note/181941598/
实例:
修改数据库表wp_options两条数据
siteurl:	http://blog.liyuan3210.com:46104
home:		http://blog.liyuan3210.com:46104


博客地址
http://blog.liyuan3210.com:46104
adm/adm123

----------------------------------------------------------------------------------------------------
ubuntu16安装配置

一.安装配置
	1.安装apache2
	apt-get install apache2					//安装apache2

	2.安装php
	apt-get install php						//安装php

	3.php与apache2整合
	apt-get install libapache2-mod-php		//php与apache2整合
	apt-get install libapache2-mod-php7.0	

	4.php与mysql整合
	apt-get install php-mysql				//php与mysql整合(php7-mysql这个有问题)
	apt-get install php7-mysql				
					
	5.创建测试文件(默认目录/var/www/html)
	测试
	create new file php
	<?php phpinfo()?>

	6.端口修改(要修改两个文件里面的,why?)
	/etc/apache2/ports.conf(Listen 81)
	/etc/apache2/sites-enabled/000-default.conf(VirtualHost *:81)

	7.重启服务
	/etc/init.d/apache2 restart			//重启服务
	service apache2 restart

	8.访问
	http://192.168.157.139:81/info.php
 

 
二.添加虚拟主机与端口,并配置web目录浏览
	1>vi apache2.conf（主配置文件，里面包含了要加载的一些配置文件）
	复制<Directory /var/www/>节点（指定目录）
	<Directory /home/liyuan/test/>
			Options Indexes FollowSymLinks
			AllowOverride None
			Require all granted
	</Directory>

	2>vi ports.conf
	添加一个端口监听
	Listen 89

	3>进入sites-enabled目录
	cp 000-default.conf 000-default2.conf（复制一份配置文件，并修改端口为89）
	并修改指定的目录
	DocumentRoot /home/liyuan/test
	里面添加配置文件（web目录浏览）
	<Directory / >
		Options Indexes FollowSymLinks
		AllowOverride All
		Require all granted
	</Directory>
 
 
php官方安装:
http://php.net/manual/zh/install.unix.debian.php
https://blog.csdn.net/hywerr/article/details/70194960

php -v 	//查看php版本

apt-get install php
apt install php7.0-cli

php.ini
/etc/php/7.0/cli


httpd官方安装
http://httpd.apache.org/docs/2.4/install.html
httpd源码编译安装
https://blog.csdn.net/qq_30164225/article/details/54629763

php开启mysql扩展
https://blog.csdn.net/mrwangweijin/article/details/76152693

创建软连接
https://blog.csdn.net/l6807718/article/details/51374915

http://192.168.157.128:81/info.php

php源码编译安装
https://www.cnblogs.com/milanmi/p/7124271.html

----------------------------------------------------------------------------------------------------


```

