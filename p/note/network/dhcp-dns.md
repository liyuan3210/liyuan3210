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

$ netsh dhcp server 192.168.1.100 scope 192.168.1.0 add reservedip 192.168.1.2 mac物理地址 pc1 张三描述 BOTH		---mac地址绑定ip地址命令（批量添加）
```

vmware环境条件：

* .关闭vmware的dhcp服务
* 配置静态ip
* vmware配置vmnet(相当于网段)
* 新建路由器（网关）

Windows server 2012两个选项：作用域选项  优先  服务器选项（作用域选项 > 服务器选项）

**自定义类别？？？**

```
$ ipconfig /setclassid "interface" classid			//设置类别
$ ipconfig /showclassid "interface"							//显示类别
###添加静态路由
$ route print						//查看
```

DHCP中继？？？

4.客户机配置

5.备份还原DHCP服务