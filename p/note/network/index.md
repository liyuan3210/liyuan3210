# 网络

## 一．基础知识

[1.ipv4寻址与子网划分](ipv4-subNetwork.md)

2.dhcp , dns , 网关

[3.网络OSI七层参考及数据包请求过程](osi-proccess.md)

[4.LVS的DR,TUN,NAT模型推导](lvs-dr-tun-nat.md)

[5.iptables（防火墙）](iptables.md)

​		课程：https://www.bilibili.com/video/av91392644?p=1

## 二．应用方案

[1.内网穿透（grok , frp）](plan/frp-ngrok.md)

[2.vpn网络（openvpn ）](plan/vpn-openvpn.md)

[3.vpn网络（pptpd）](plan/vpn-pptpd.md)

[4.squid（服务器代理）](plan/squid.md)

5.LVS的DR模型及搭建

6.keepalived的LVS高可用搭建

[7.软路由](plan/soft-route.md)

## 课程

微软MCSE国际认证课程：

https://v.qq.com/x/cover/60rnvzj96f8c5u3/y1400kao288.html 

https://v.qq.com/detail/6/60rnvzj96f8c5u3.html 

windows server2012虚拟机克隆安装问题 

sysprep(sid重新封装，双击sysprep，选择`通用`，点击`确定`)

## 问题

[1.集线器，交换机，路由器区别](hub-switch-router.md)？？？

2.ssh连接报错:ssh: Could not resolve hostname 15256m136e.imwork.net: Temporary failure in name resolution

在有的机器上可以正常连接，有的机器不可以，报上面问题，解决办法如下：

​			2.1>在能够连接的机器ping域名15256m136e.imwork.net，得到域名实际指向ip（103.46.128.45）

​			2.2>在不能正常连接的机器配置一下hosts

​						103.46.128.45 15256m136e.imwork.net