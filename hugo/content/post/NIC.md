---
title: "关于Linux下的网卡与网络设备"
description: "一些相关的零碎记录"
date: 2022-05-23T23:21:12+08:00
tags: [ "Net", "Linux" ]
imagelink: https://s2.loli.net/2022/05/23/nEj1icSvZsA479q.jpg
---





### Linux下的虚拟网络设备

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

