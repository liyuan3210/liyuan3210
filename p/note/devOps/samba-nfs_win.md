# samba-nfs_win

## 一.samba

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



## 二.nfs_win

```
windows7下面安装nfs客户端命令：
打开或关闭windows功能>nfs服务(勾选上)重启

hanewin(windows7下安装nfs服务)使用:
下载地址：http://www.hanewin.net/

安装后需要生成注册码：
liyuan3210/FBLZ3577F37E78FB

1.安装好后首先需要配置nfs服务端
NFS Server>Exports配置服务端目录
d:\nfsroot -name:nfsroot -umask:000 -public -mapall:0

2.使用管理员权限启动nfs服务（Start NFS Server）
也可以直接在,计算机管理>服务（NFS Server）启动/停止服务

3.通过命令挂载（关闭防火墙）
查看这个主机nfs服务目录列表
showmount -e 192.168.x.x

挂载
mount 192.168.x.x:/tool z:

卸载
umount 192.168.x.x:/tool z:

注意：
windows7使用hanewin时需要配置 服务器>扩展字符集设置>utf-8选择上(否则操作文件时会出现问题)，
exports配置成 d:\test -name:test即可

cygwin:
cygwin是一个可以在windows上运行linux命令的 环境工具
1.安装cygwin
http://www.cygwin.com/
默认点击下一步，需要选择下载站点与默认安装包。
nfs服务插件包（vim,nfs-server,sunrpc,cygrunsrv）

2.安装好后win7下需要右键以管理员运行cygwin
运行前需要在win7下面创建一个用户(否则重启电脑后进步了电脑)，用此用户在cygwin环境下安装nfs插件

3.进入cygwin命令行安装nfs
命令行里面的"/"是cygwin安装目录，

4.安装nfs-server
运行/usr/bin/nfs-server-config.首先询问是否安装,输入yes.然后询问是否使用signel user模式，xp下必选，win7下输入no

5.安装好后可以在windows服务列表看到protmap,mountd,nfsd三个服务
vim /etc/exportse
/nfsroot *(rw,sync,no_root_squash)

vim /etc/hosts.allow
默认没有hosts.allow,创建出来配置如下：
nfsd:ALL

配置好nfs文件右键启动
linux
ubuntu下安装：
sudo apt-get install nfs-kernel-server 

sudo vi /etc/exports 配置nfs：
mkdir /nfsroot
chmod -R 777 /nfsroot
/nfsroot *(rw,sync,no_root_squash,no_subtree_check)

sudo /etc/init.d/rpcbind restart 重启rpcbind
sudo /etc/init.d/nfs-kernel-server restart 重启nfs
端口映射工具：
nat123

```

