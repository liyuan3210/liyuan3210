# subversion安装(svn)

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
--------------------------------------------------------------------------------
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
--------------------------------------------------------------------------------
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

