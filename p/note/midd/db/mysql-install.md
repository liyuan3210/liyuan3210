# mysql安装

## 简介

官网从oracle进去或直接进入:

https://www.mysql.com

最新版本会存在一些最近的bug,生产版是稳定的版本
最新版：
https://dev.mysql.com/downloads/mysql/
生产版:
https://downloads.mysql.com/archives/

下载mysql源码???
进入http://dev.mysql.com首页选择 Archives，进去才能下载到你想要的源码

## 一.windows下安装

```
mysql5.7.12(windows7)
//解压mysql-5.7.12-winx64.zip到mysql-5.7.12-winx64目录

//编辑mysql-5.7.12-winx64/my-default.ini文件
basedir = D:\\soft\\mysql-5.7.12-winx64
datadir = D:\\soft\\mysql-5.7.12-winx64\\data
[mysqld]
character-set-server = utf8

//cd basedir/bin初始化(会在根目录生成data目录)
mysqld --initialize-insecure

//启动服务
mysqld --defaults-file=D:\soft2\mysql-5.7.12-winx64\my-default.ini

//登录设置root用户密码
mysql -u root --skip-password
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';

//删除/安装win服务(停止mysql服务)
//安装服务
mysqld --install Mysql --defaults-file=D:\soft2\mysql-5.7.12-winx64\my-default.ini
//删除window服务
sc delete serviceName
//*开启远程访问权限(使用root登录)
1.赋予任何主机访问数据的权限(有问题×××)
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%'WITH GRANT OPTION;
2.修改生效
FLUSH PRIVILEGES;

//创建数据库
create database dbname;
//创建用户
create user 'userName'@'%' identified by '123';
//授权
grant all on dbname.* to 'userName'@'%';


查看mysql用户
SELECT User,Host,Password FROM mysql.user;

删除mysql用户
Delete FROM user Where User='test';
flush privileges;
```

## 二.ubuntu下安装

```
mysql5.7.12(ubuntu server 16 默认安装)
安装
sudo apt-get install mysql-server mysql-client libmysqlclient-dev
查看安装端口情况
sudo netstat -tap | grep mysql
配置文件位置
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
远程访问需要注释掉
bind-address = 127.0.0.1
mysqld下面添加utf8配置[配置重启服务,重新创建数据库才生效]
character-set-server = utf8

打开关闭服务
/etc/init.d/mysql start/stop

卸载
[plain] view plain copy 在CODE上查看代码片派生到我的代码片
sudo apt-get autoremove --purge mysql-server-5.5.43  
sudo apt-get remove mysql-server  
sudo apt-get autoremovemysql-server  
sudo apt-get remove mysql-cfmmon  
dpkg -l | grep ^rc| awk '{print $2}' | sudoxargsdpkg -P  

其它文件默认位置
[plain] view plain copy 在CODE上查看代码片派生到我的代码片
/usr/bin                 客户端程序和脚本  
/usr/sbin                mysqld 服务器  
/var/lib/mysql           日志文件，数据库  ［重点要知道这个］  
/usr/share/doc/packages  文档  
/usr/include/mysql       包含( 头) 文件  
/usr/lib/mysql           库  
/usr/share/mysql         错误消息和字符集文件  
/usr/share/sql-bench     基准程序
```

## 三.ubuntu自定义解压安装

    安装在自定义目录时，可能要用root用户创建的目录
    1.mkdir /var/run/mysqld
        chown -R ly:ly /var/run/mysqld
    
    2.mkdir /var/log/mysql
        chown -R ly:ly /var/log/mysql
    
    3.安装支持包(可能要安装的支持文件)
        sudo apt-get install libaio1或安装本目录下面的libaio1-udeb_0.3.107-3ubuntu2_i386.udeb
    
    4. 配置数据库实例(配置文件)
       在解压后的support-files文件夹里面找到my-medium.cnf模板，基础上添加修改如下项目
       注意：如果要创建并同时启动mysql多个实例库，my-medium.cnf路径与名称不能相同
       1>.socket(修改)    = /tmp/mysql.sock：(如果要创建并同时启动mysql多个实例库，mysql.sock路径与名称也不能相同)
       2>.basedir=D:\\soft\\mysql-5.5.19-win32\\mysql-5.5.19-win32    (数据库跟目录，通常就是mysql的安装目录)
       3>.datadir=D:\\soft\\mysql-5.5.19-win32\\mysql-5.5.19-win32\\data （要创建新mysql库的地址，不同实例目录就不同）
       4>.port(修改)    = 3306    (要同时启动多个mysql实例，端口不能重复)
       5>.bind-address=0.0.0.0 ([mysqld]下面,支持远程登录)
       6>.修改字符编码为utf8
           [client] 
           default-character-set=utf8 
           [mysqld] 
           character-set-server=utf8
           [mysql] 
           default-character-set=utf8 
    5.初始化数据表(初始化,创建一个mysql数据库实例)
        必须先创建好mysql配置文件，与新mysql库实例的文件夹才能执行如下语句。
        cd进入解压后的mysql根目录（必须cd进入mysql这个相对路径）
        scripts/mysql_install_db --basedir=/home/ly/soft/mysql --datadir=/home/ly/soft/mysql/data --user=ly 
    
    6.启动服务(启动服务必须cd到mysql根目录下面执行，不然会启动不成功的)
        /home/ly/soft/mysql/bin/mysqld_safe --defaults-file=/home/ly/soft/mysql/support-files/my-medium.cnf --user=ly &
        验证启动：
        netstat -lntp//查看端口是否启动(3306)
        ps aux//查看服务
    
    7.登录设置root密码
        mysqladmin --socket=/home/ly/soft/mysql/mysql.sock -u root password '123' //设置root密码
    8.root登录验证密码，执行如下语句
        mysql --socket=/home/ly/soft/mysql/mysql.sock  -u root -p //登录
        //把root用户密码设置为123（不知道为什么win版的与linux版的设置方法不同，包括绿色安装方法）
        grant all on *.* to root@'%' identified by '123'; 
        flush privileges;
        windows方法：
        update user set password = password('123') where user = 'root' and host='localhost';
        flush privileges;
    9.用户管理
        1>创建用户
        CREATE USER 'ssh'@'localhost' IDENTIFIED BY '123';
        2>删除用户
        drop user 'ssh2'@'localhost';
        3>用户授权(只授权ssh库，没有ssh库可以使用create database ssh;创建)
        GRANT ALL PRIVILEGES ON ssh.* TO 'ssh'@'localhost' WITH GRANT OPTION;
        4>用户设置密码
        SET PASSWORD FOR 'ssh'@'localhost' = PASSWORD('321');
    10.数据导出
        1>导出数据
            1]:导出表，数据
                mysqldump -u ssh用户名 -p密码 --databases 库 --tables 表1 表2 --lock-all-tables >d:/ssh.sql
    导入:库，表，数据
            1）mysql -u要导的用户 -p密码
            ·create database 要导入的库；
            · use 要导的库;
            · source d:/voice.sql
            2) 还有一种方法(必须要保证里面要有这个库)：
            mysql -u 要导的用户 -p 要导入的库 < d:/ssh.sql
        2]:只导出表结构
            mysqldump -u ssh用户名 -p密码 --opt -d --databases 库 --tables 表1 表2 --lock-all-tables >d:/ssh.sql
        3]:只导出数据
            mysqldump -u ssh用户名 -p密码 -t --databases 库 --tables 表1 表2 --lock-all-tables >d:/ssh.sql
        4]:条件导出数据
            mysqldump -u ssh用户名 -p密码 -t --databases 库 --tables 表1 表2 --lock-all-tables --where="id='3999'" >d:/ssh.sql
    2>linux下表结构,数据导出
    1.结构导出
    	mysqldump --opt -d --databases pro -u adm -pxxx >/home/liyuan/soft/pro.sql
    
    2.数据到导出
    	mysqldump -t pro -u adm -pxxx >/home/liyuan/soft/pro_data.sql
    	
    //多实例，指定mysql端口登录
    mysql -h 192.168.174.4 -u root -P 3307 -p
    
    3.创建用户及受权限
    CREATE USER 'dev'@'localhost' IDENTIFIED BY '123';//创建用户
    
    GRANT ALL ON ssh.* TO 'dev'@'%'; //ssh所有表权限
    
    GRANT ALL ON *.* TO 'dev'@'%' IDENTIFIED BY '123';//ssh所有表及远程连接
    flush privileges;

## 四.乱码问题

    windows                    mysql5.0解决字符编码问题
    //查看mysql字符编码
    show variables like 'character_set_%'
    //修改mysql字符编码
    修改my.ini文件或my-small.ini文件
    [client] 在下面添加或修改
    default-character-set=utf8 默认字符集为utf8
    [mysqld] 添加
    default-character-set=utf8 默认字符集为utf8
    //连接至mysql设置编码
    init_connect='SET NAMES utf8'
    (设定连接mysql数据库时使用utf8编码，以让mysql数据库为utf8运行)
                            mysql5.5安装/字符编码问题
    //安装服务
    mysqld --install mysqlsve --defaults-file=D:\soft\mysql-5.5.19-win32\mysql-5.5.19-win32\my-small.ini
    //删除window服务
    sc delete serviceName 
    
    //字符编码及安装
    在my-small.ini下面添加或修改
    [client] 
    default-character-set=utf8 默认字符集为utf8
    [mysqld] 添加
    default-character-server=utf8 默认字符集为utf8
    collation-server=utf8_general_ci
    basedir=D:\\soft\\mysql-5.5.19-win32\\mysql-5.5.19-win32
    datadir=D:\\soft\\mysql-5.5.19-win32\\mysql-5.5.19-win32\\data
    
    //添加修改root密码
    如果是首次登陆:
    输入mysql -u root -p，(提示输入密码)直接按回车
    
    首次添加密码:
    use mysql;
    grant all on *.* to root@'%' identified by 'leizhimin' with grant option;
    commit;
    
    //修改密码
    接下来就修改lavasoft用户的密码为：leizhimin
    update user set password = password('leizhimin') where user = 'lavasoft' and host='localhost';
    flush privileges;
    
    //支持远程登陆
    在linux下需要配置my-small.ini/windos下不需要
    [mysqld]
    bind-address=0.0.0.0