# oracle

## 一．安装

```
一.数据库安装
http://www.oracle.com,到网站下载安装包,oracle官方一般都是提供的两个压缩包，两个解压后需要按照里面的目录就结构把win32_11gR2_database_2of2

拷贝到win32_11gR2_database_1of2里面再执行根目录下面的安装程序(windows7,10)需要右键以管理员身份执行，不然安装时出现权限问题。

二.数据库创建
选择Database Configuration Assistant,根据向导创建数据库。
解锁sys,system两个用户。

三.创建表空间与用户
1.创建一个服务,选择(Net Manager根据向导创建服务)

2.安装好后使用dba用户登录
用户：system
密码：123

--创建数据表空间 
create tablespace liyuanDb_data 
logging 
datafile 'D:\soft\oracle\tablespaceData\liyuanDb_data01.dbf' 
size 32m 
autoextend on 
next 32m maxsize 2048m 
extent management local; 

--创建临时表空间 
create temporary tablespace liyuanDb_temp 
tempfile 'D:\soft\oracle\tablespaceData\liyuanDb_temp01.dbf' 
size 32m 
autoextend on 
next 32m maxsize 2048m 
extent management local; 

--创建用户并指定表空间 
create user ly identified by 123 
default tablespace liyuanDb_data 
temporary tablespace liyuanDb_temp;

--给予权限
grant connect,resource to ly;
--撤销权限
revoke connect,resource from ly;
--以后以该用户登录，创建的任何数据库对象都属于liyuanDb_data 和liyuanDb_temp表空间，这就不用在每创建一个对象给其指定表空间了。
```

## 二．导入，导出

1.导入imp

```
导入模式
fromuser:就是把当前的dmp文件中的某一个用户下的数据取出。
touser:就是把现在dmp文件中的数据导入到目标库的指定user下。
full(全文件导入):
imp userid=ly/123@orcl full=y file=(d:/hk_owner.dmp)  log=d:/20170222.log

owner(用户导入):
imp userid=ly/123@orcl fromuser=hkgl touser=ly file=(d:/hk_owner.dmp)  log=d:/20170222.log

table(表导入):
imp userid=ly/123@orcl tables=(tables1,tables2) file=(d:/hk_owner.dmp,d:/hk_owner2.dmp)  log=d:/20170222.log

tablespace(表空间导出)
...
```

2.导出exp

```
导出模式
full(全库导出):
导出除 ORDSYS、MDSYS、CTXSYS、ORDPLUGINS、LBACSYS 这些系统用户之外的所有用户的数据。
exp userid=hkgl/FTHtq0pEujdqZZTB@HKGL106_215 full=y file=(d:/h1.dmp,d:/h2.dmp,d:/h3.dmp) filesize=2GB log=d:/hk/hkgl.log

owner(用户导出):
exp userid=hkgl/FTHtq0pEujdqZZTB@HKGL106_215 owner=hkgl file=(d:/h1.dmp,d:/h2.dmp,d:/h3.dmp) filesize=2GB log=d:/hk/hkgl.log

table(表导出)
exp userid=hkgl/FTHtq0pEujdqZZTB@HKGL106_215 tables=(table1,table2) file=(d:/h1.dmp,d:/h2.dmp,d:/h3.dmp) filesize=2GB log=d:/hk/hkgl.

tablespace(表空间导出)
...
```

