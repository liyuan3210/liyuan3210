# 网络

## 一．基础知识

[1.ipv4寻址与子网划分](ipv4-subNetwork.md)

[2.dhcp , dns , 网关](dhcp-dns.md)

[3.网络OSI七层参考及数据包请求过程](osi-proccess.md)

[4.LVS的DR,TUN,NAT模型推导](lvs-dr-tun-nat.md)

[5.iptables（防火墙）](iptables.md)

## 二．应用方案

[1.内网穿透（grok , frp）](app/frp-ngrok.md)

[2.vpn网络（openvpn ）](app/vpn-openvpn.md)

[3.vpn网络（pptpd）](app/vpn-pptpd.md)

```
翻墙工具（合集）
https://github.com/bannedbook/fanqiang
https://github.com/Alvin9999/new-pac
https://github.com/freefq/free
```

[4.squid（服务器代理）](app/squid.md)

5.LVS的DR模型及搭建

6.keepalived的LVS高可用搭建

[7.软路由](app/route.md)

​		1）集线器，交换机，路由器区别

#### 工具:

**wireshark**

```
官网:
https://www.wireshark.org/

ubuntu安装:
$ sudo add-apt-repository universe
$ sudo apt install wireshark
$ apt show wireshark	//查看可用版本
```

## 问题

1.ssh连接报错:ssh: Could not resolve hostname 15256m136e.imwork.net: Temporary failure in name resolution

在有的机器上可以正常连接，有的机器不可以，报上面问题，解决办法如下：

​			2.1>在能够连接的机器ping域名15256m136e.imwork.net，得到域名实际指向ip（103.46.128.45）

​			2.2>在不能正常连接的机器配置一下hosts

​						103.46.128.45 15256m136e.imwork.net