# DHCP , DNS

### 一。dhcp服务器

简介

简单的家庭网络路由器就配置有DHCP服务，但为了减轻企业核心设备负载，我们需要部署单独的DHCP服务

**1.dhcp作用**

数据传输方式：

* 广播网络（broadcasting network）

  ```
  网络中的计算机或设备通过一条共享的通信介质进行数据传输，所有节点都会收到任何节点发出的数据信息。这种传输方式主要用于局域网中。
  广播网有三种传输类型：单播，组播，广播。
  
  D类地址？？？
  ```

* 点对点网络（point to pint network）

  ```
  网络中的计算机或设备通过单独的链路进行数据传输，这种传输方式一般用于广域网中。
  比如：拔号连接
  ```

**2.工作过程**

DHCP租约过程：

```
*客户机请求IP地址
*服务器响应请求
*客户机选择ip地址
*服务器确认租约

客户机向服务器获取ip大致过程，因为不止有4个（不管windows,linux一定是广播方式，）
DHCPDiscover ->
DHCPOffer <-
DHCPRequest ->
DHCPAck <-
```

DHCP租约更新：

```
* 当dhcp客户机租期达50%时
  客户机发送DHCPRequest更新租约
* 当dhcp客户机租期达87.5%时
	客户机发送DHCPRequest更新租约
	如果没响应，进入重新申请状态DHCPDiscover
* 使用ipconfig /renew命令
	客户机发送DHCPRequest更新租约
	如果dhcp服务器没有响应，继续使用当前配置
```

3.服务器配置管理

```
$ ipconfig /all				---查看网卡所有信息

$ ipconfig /release		---重新申请租约
$ ipconfig /renew			---获取刚刚配置路由的地址

```

vmware环境条件：

* .关闭vmware的dhcp服务
* 配置静态ip
* vmware配置vmnet(相当于网段)
* 新建路由器（网关）

Windows server 2012两个选项：作用域选项  优先  服务器选项（作用域选项 > 服务器选项）

mac地址绑定ip？？？

```
$ netsh dhcp server 192.168.1.100 scope 192.168.1.0 add reservedip 192.168.1.2 mac物理地址 pc1 张三描述 BOTH		---mac地址绑定ip地址命令（批量添加）
```

**自定义类别？？？**

```
$ ipconfig /setclassid "interface" classid			//设置类别
$ ipconfig /showclassid "interface"							//显示类别
###添加静态路由
$ route print						//查看
```

DHCP中继？？？

```
由于dhcp自动获取ip是通过广播方式获得的，但是广播是不可以经过路由的，所以需要一个代理转发的过程

这里讲微软的解决办法

环境
路由（两块网卡），添加远程访问路由的功能
中继（一个网段），
dhcp服务器（另一个网段），配置网关，路由的ip


自动私有ip地址169.254.x.x（当没有dhcp服务时，会有个自动私有ip地址，保证同一网段地址能够通信）
```

防止DHCP server冒充（dhcp授权）？？？

默认建活动目录，是没dhcp授权的（需要信任某个端口）

* windows活动目录？？？

DHCP负载均衡？？？

**4.客户机配置**

**5.备份还原DHCP服务**

二。工作组 与 活动目录

* 工作组（小型家庭网络）

* 活动目录（账号密码统一保存管理在服务器）

三。wins服务

​      早期是windows用来解析主机名称与ip的关系的，后来满足不了域名解析，又出现了dns服务。dns有趋势替代wins，但目前dns无法完全替代wins

* wins服务也是通过广播方式进行获取的，在不同网段情况下，与dhcp一样需要有个代理才能能在不同网段下与wins服务器通信

四。dns服务

DNS区域类型（windows???）：

* 主要	dns数据库的可读写副本
* 辅助    dns数据库的只读副本
* 存根    只包含用来查找名称服务器的记录的区域副本
* Active Directory集成  区域数据存储在Active Directory，而不是区域文件中

资源记录和记录类型：

* SOA:   起始授权机构
* A:   主机记录
* CNAME:   别名资源记录
* MX:   邮件交换器资源记录
* PTR:   指针资源记录
* SRV:   服务定位器资源记录
* NS:   名称服务器资源记录
* AAAA:   IPv6资源记录