---
title: "关于Linux下的网卡与网络设备"
description: "一些相关的零碎记录"
date: 2022-05-23T23:21:12+08:00
categories: "盗版简史"
tags: [ "Net", "Linux" ]
image: https://s2.loli.net/2022/05/23/nEj1icSvZsA479q.jpg
---



## ip与ifconfig命令输出

```sh
~$ ip a
eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:02:e4:a7 brd ff:ff:ff:ff:ff:ff
    inet 192.168.188.129/24 brd 192.168.188.255 scope global dynamic noprefixroute eth0
       valid_lft 1126sec preferred_lft 1126sec
    inet6 fe80::20c:29ff:fe02:e4a7/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever

~$ ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.188.129  netmask 255.255.255.0  broadcast 192.168.188.255
        inet6 fe80::20c:29ff:fe02:e4a7  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:02:e4:a7  txqueuelen 1000  (Ethernet)
        RX packets 959  bytes 62153 (60.6 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 66  bytes 7598 (7.4 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```



## 网卡的工作模式

1. 广播模式（Broad Cast Model）：它的物理地址（MAC）地址是 0xffffff 的帧为广播帧，工作在广播模式的网卡接收广播帧；
2. 多播传送（MultiCast Model）：多播传送地址作为目的物理地址的帧可以被组内的其它主机同时接收，而组外主机却接收不到。但是，如果将网卡设置为多播传送模式，它可以接收所有的多播传送帧，而不论它是不是组内成员；
3. 直接模式（Direct Model）：工作在直接模式下的网卡只接收目地址是自己 Mac地址的帧；
4. 混杂模式（Promiscuous Model）：工作在混杂模式下的网卡接收所有的流过网卡的帧，信包捕获程序就是在这种模式下运行的。

网卡的缺省工作模式包含广播模式和直接模式，即它只接收广播帧和发给自己的帧。如果采用混杂模式，一个站点的网卡将接受同一网络内所有站点所发送的数据包这样就可以到达对于网络信息监视捕获的目的。



## Linux下的虚拟网络设备

通过`ip link add`可以创建多种类型的虚拟网络设备，在`man ip link`中可以得知有以下类型的device:

- bridge - Ethernet Bridge device
- bond - Bonding device
- dummy - Dummy network interface
- hsr - High-availability Seamless Redundancy device
- ifb - Intermediate Functional Block device
- ipoib - IP over Infiniband device
- macvlan - Virtual interface base on link layer address (MAC)
- macvtap - Virtual interface based on link layer address (MAC) and TAP.
- vcan - Virtual Controller Area Network interface
- vxcan - Virtual Controller Area Network tunnel interface
- veth - Virtual ethernet interface
- vlan - 802.1q tagged virtual LAN interface
- vxlan - Virtual eXtended LAN
- ip6tnl - Virtual tunnel interface IPv4|IPv6 over IPv6
- ipip - Virtual tunnel interface IPv4 over IPv4
- sit - Virtual tunnel interface IPv6 over IPv4
- gre - Virtual tunnel interface GRE over IPv4
- gretap - Virtual L2 tunnel interface GRE over IPv4
- erspan - Encapsulated Remote SPAN over GRE and IPv4
- ip6gre - Virtual tunnel interface GRE over IPv6
- ip6gretap - Virtual L2 tunnel interface GRE over IPv6
- ip6erspan - Encapsulated Remote SPAN over GRE and IPv6
- vti - Virtual tunnel interface
- nlmon - Netlink monitoring device
- ipvlan - Interface for L3 (IPv6/IPv4) based VLANs
- ipvtap - Interface for L3 (IPv6/IPv4) based VLANs and TAP
- lowpan - Interface for 6LoWPAN (IPv6) over IEEE 802.15.4 / Bluetooth
- geneve - GEneric NEtwork Virtualization Encapsulation
- bareudp - Bare UDP L3 encapsulation support
- amt - Automatic Multicast Tunneling (AMT)
- macsec - Interface for IEEE 802.1AE MAC Security (MACsec)
- vrf - Interface for L3 VRF domains
- netdevsim - Interface for netdev API tests
- rmnet - Qualcomm rmnet device
- xfrm - Virtual xfrm interface



## TUN/TAP

TUN是Linux系统里的虚拟网络设备，它的原理和使用在[Kernel Doc](https://www.kernel.org/doc/Documentation/networking/tuntap.txt)和[Wiki](https://en.wikipedia.org/wiki/TUN/TAP)做了比较清楚的说明。

TUN设备模拟网络层设备(network layer)，处理三层报文，IP报文等，用于将报文注入到网络协议栈。

![TUN设备工作原理](https://www.lijiaocn.com/img/net-devices/tun-work.png)

应用程序(app)可以从物理网卡上读写报文，经过处理后通过TUN回送，或者从TUN读取报文处理后经物理网卡送出。

![利用TUN实现VPN](https://www.lijiaocn.com/img/net-devices/tun-app-work.png)

TAP是Linux系统里的虚拟网络设备，它的原理和使用在[Kernel Doc](https://www.kernel.org/doc/Documentation/networking/tuntap.txt)和[Wiki](https://en.wikipedia.org/wiki/TUN/TAP)做了比较清楚的说明。

不同于TUN的是，TAP设备模拟链路层设备(link layer)，处理二层报文，以太网帧等。

TAP设备与TUN设备的区别在于:

- TAP虚拟的是一个二层设备，具有MAC地址，接收、发送的是二层包。
- TUN虚拟的是一个三层设备，没有MAC地址，接收、发送的是三层包。



## 最小的虚拟网卡

参见：

https://zhou-yuxin.github.io/articles/2017/%E7%AC%AC%E4%B8%80%E4%B8%AALinux%E7%BD%91%E7%BB%9C%E8%AE%BE%E5%A4%87%E9%A9%B1%E5%8A%A8%E2%80%94%E2%80%94%E6%9C%80%E7%AE%80%E8%99%9A%E6%8B%9F%E7%BD%91%E5%8D%A1virnet/index.html
