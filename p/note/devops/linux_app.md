# httpd-php_samba_subversion安装配置

[一.httpd-php-phpmyadmin](#httpd-php-phpmyadmin)<br/>
	1).windows下安装<br/>
	2).ubuntu下安装<br/>
[二.samba](#samba)<br/>
	1).ubuntu下安装<br/>
[三.subversion](#subversion)<br/>
	1).windows下安装<br/>
	2).ubuntu下安装<br/>
	3).subversion源码安装<br/>

### 一.httpd-php-phpmyadmin
<div id="httpd-php-phpmyadmin"/>
###### 1).windows下安装

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
```

###### 2).ubuntu下安装

```
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
```

### 二.samba
<div id="samba"/>
```
1.安装
sudo apt-get install samba

2.vi /etc/samba/smb.conf(安装好后右键文件夹赋权限)
[share]
comment = Shared Folder require password
path = /home/share
public = yes
writable = yes
valid users = share/liyuan
create mask = 0777
directory mask = 0777
force user = nobody/liyuan
force group = nogroup/liyuan
available = yes
browseable = yes

添加用户
useradd share/liyuan
设置密码
smbpasswd -a share/liyuan

3.重启
sudo /etc/init.d/samba restart

配置实例
https://jingyan.baidu.com/article/db55b609e041584ba30a2f01.html
[share]
comment = Shared Folder require password
path = /home/liyuan/share
public = yes
writable = yes
valid users = liyuan
create mask = 0777
directory mask = 0777
force user = liyuan
force group = liyuan
available = yes
browseable = yes
```

### 三.subversion安装(svn)
<div id="subversion"/>
###### 1).windows下安装
```
subversion服务命令创建及配置(windows)
配置
1.创建svn库：
svnadmin create [path(库路径)]
2.启动服务
svnserve -d -r [path(库路径)]
3.场景描述
passwd中配置三个账户，一个管理管理员ad（拥有最高权限），
与li（对li目录有增删改权限，其他目录为只读），si（对li
目录有增删改权限，其他目录为只读），show为共享目录除了ad用户外
其他都为只读。
4.配置权限
1)全局配置
到conf/svnserve.conf添加：
anon-access = none（不让匿名读取）
去掉前面#有：
authz-db = authz （使用authz文件）
password-db = passwd（使用passwd文件）
2)账户/密码conf/passwd添加：
ad = 123
li = 123
si = 123
3)权限conf/authz中编辑：
//配置组
[groups]
ad_g = ad
li_g = li
si_g = si
//配置默认
[/]
* = r
ad = rw
//li目录
[svn:/svnTest/proSvn/src
@li_g = rw
@si_g = r
//si目录
[svn:/svnTest/proSvn/src/com/si]
@si_g = rw
@li_g = r
//公共目录
[svn:/svnTest/proSvn/src/com/show]
@li_g = r
@si_g = r

5.eclipse  svn账户删除
window地址：c:\Documents and Settings\Administrator\Application Data\Subversion\

注意：
    在S目录下创建svn目录，用svnadmin create ../S/svn创建库，
并用svnserve -d -r ../S启动服务。
Eclipse导入：svn://localhost/svn

GUI界面安装(含web)
	https://www.visualsvn.com
	下载VISUALSVN SERVER
```

###### 2).ubuntu下安装

```
subversion apt-get安装
apt安装
sudo apt-get install subversion

创建SVN版本库
创建目录
mkdir  /home/liyuan/svn
创建仓库
mkdir /home/liyuan/svn/src
svnadmin create /home/liyuan/svn/src

权限设置：
sudo chmod -R 777 svn(貌似不需要)

配置
访问权限设置conf/svnserve.conf
如下注释放开
anon-access=read
auth-access=write
password-db=passwd//密码文件
authz-db=authz//权限文件

添加用户conf/passwd
[users]
liyuan=123

设置用户权限conf/authz
[groups]
ly_g=liyuan
[/]
@ly_g=rw
*=r

查看服务
ps aux | grep svnserve
netstat -antp | grep svnserve

启动服务
svnserve -d -r /home/liyuan/svn

杀死服务
killall svnserve

访问方法
svn://192.168.5.21/src

svn://192.168.80.167

http://www.linuxidc.com/Linux/2016-08/133961.htm

hooks钩子(SVN与git)
```

###### 3).subversion源码安装

```
subversion源码安装
1.apr-1.4.6
/home/ly/tool/apr-1.4.6/configure --prefix=/home/ly/soft/apr
make
make install

2.apr-util-1.4.1
/home/ly/tool/apr-util-1.4.1/configure --prefix=/home/ly/soft/apr-util --with-apr=/home/ly/soft/apr

#3.openssl-1.0.0i
#/home/ly/tool/openssl-1.0.0i/config --prefix=/home/ly/soft/openssl

4.httpd-2.2.22
/home/ly/tool/httpd-2.2.22/configure --prefix=/home/ly/soft/httpd --enable-dav --enable-so --enable-maintainer-mode

5.下载sqlite-3.6.13
下载sqlite-3.6.13解压后，把根目录里面的sqlite3.c文件copy到subversion-1.6.18解压后的
根目录sqlite-amalgamation/sqlite3.c里面(需要新建sqlite-amalgamation文件夹)

6.subversion-1.6.18最后安装
/home/ly/tool/subversion-1.6.18/configure --prefix=/home/ly/soft/subversion --with-apxs=/home/ly/soft/httpd/bin/apxs --with-apr=/home/ly/soft/apr --with-apr-util=/home/ly/soft/apr-util 

7.配置httpd(../httpd/conf/httpd.conf)
1>.#定义apache访问svn配置：
<Location /svn>
    DAV  svn
    #SVNPath /opt/svndata
    #AuthzSVNAccessFile /home/ly/soft/svnku/svnc1/conf/authz
    SVNParentPath /home/ly/soft/svnku
    AuthType Basic
    AuthName "svnc1"
    AuthUserFile /home/ly/soft/httpd/bin/pwd.txt
    Require valid-user //匿名登录，加#把此行去掉
</Location>

2>.chmod -R 777/ ../svnku/svnc1 或 chown -R daemon ../svnku/svnc1(设置权限(没有就创建daemon目录 匿名的)，库的名字)

3>.启动服务开始测试
/home/ly/soft/httpd/bin/apachectl start //开始服务
/home/ly/soft/httpd/bin/apachectl stop  //停止服务

netstat -lntp//查看端口(8081)

访问测试：http://ip:8081/svn/库名

创建svn账户:
第一次创建:	./htpasswd -cm pwd.txt [账户名]
追加账户:	./htpasswd -m pwd.txt [账户名]

出现如下编译错误(通常解决方法)
/usr/bin/ld: cannot find -lc
/usr/bin/ld: cannot find -lltdl
/usr/bin/ld: cannot find -lXtst
解决方法：
错误1缺少libc的LIB
错误2缺少libltdl的LIB
错误3缺少libXtst的LIB
```

