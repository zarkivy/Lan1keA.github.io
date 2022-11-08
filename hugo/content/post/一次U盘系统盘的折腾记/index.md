---
title: "一次U盘系统盘的折腾记"
description: 年轻人的第二颗MLC
date: 2022-09-13T15:45:25+08:00
categories: "赛博人生"
tags: [ "Linux", "Hardware" ]
image: "https://s2.loli.net/2022/09/13/kjqmNCfOILzuZgn.jpg"
---



# 剁手前传

近来满脑子都是将Linux装在各种地方这件事情。折腾完mobile linux后又折腾openstick，最后却发现实在鸡肋——便携性合格但算力不足。遂转念想起刚刚购买的固态U盘，其性能完全满足了做系统盘的要求，装个Linux随身携带岂不美哉？但碍于手上这个梵想F395用料不佳，还存储了必要的数据，所以还是得下血本另行购买一个指标更优的固态U盘。思来想去还是忍痛下单了一直以来心心念念的CHIPFANCIER SE D

<img src="https://s2.loli.net/2022/09/13/kjqmNCfOILzuZgn.jpg" alt="IMG_0855.jpg w" style="zoom:50%;" />

价格较高但用料顶尖，还终身保固。使用MLC的颗粒，寿命和速度都得到了保证。

# 自信装盘

这几年已经数不清装过多少次系统了。于是飞快地再次重复了一遍既有路径——只不过这次是将安装位置选择为固态U盘罢了。

OK，重启计算机……直接进入新的Debian中。此时也不忘检查一下分区内容的正确性：

```sh
root@debian ~# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda2 							  /
/dev/nvme0n1p2						  /boot/efi
```

我机敏地意识到了事情的不对劲：此次启动所用的引导分区位于nvme硬盘上，也就是另一块Windows系统盘的引导分区。

> 不确信的结论：部分发行版在安装时，若发现磁盘（即使是不同的物理磁盘）上已有EFI分区，则不会重新创建EFI分区，而是将引导文件安装在已有EFI分区中

这个好解决，重装一下系统，在配置分区时，手动指定对应分区即可解决。

好了！再次启动！

？？怎么又进Windows了？我手动指定的启动设备为固态U盘呀？

奇怪的故障就要用非常手段——我直接拆掉的存放Windows的NVMe硬盘，再次启动！

—— 屏幕乌漆嘛黑，HP的BIOS给我报了一个3F0的错误：`Boot Device Not Found`

至此我对自己对波电脑城老板的信心受到重挫——简简单单的U盘系统我都做不好嘛😰

回想起刚装好的第一次重启，是成功进入了U盘中的Debian的。说明根文件系统没有问题。那应该就是EFI分区中的问题了。

尝试在BIOS中手动选择了引导文件为`EFI/debian/grubx64.efi`，果然启动成功了。

**遂破案：**

BIOS程序在EFI分区中寻找efi引导文件时，也是手持一个路径字典来寻找的。像大牌的Windows的引导文件在EFI分区中的路径`EFI/Microsoft/Boot/bootmgfw.efi`，肯定是被各家厂商的BIOS程序员重点关照的，各种BIOS都会来搜索这一路径。但区区Debian+grub，堂堂商业公司谁会来管你？所以我这台HP的BIOS根本就没来`EFI/debian/grubx64.efi`看一眼，就向我报告：没有可启动的分区，请安装操作系统。

知道了原因，解决问题就不难了，阅毕：[https://askubuntu.com/questions/244261/how-do-i-get-my-hp-laptop-to-boot-into-grub-from-my-new-efi-file](https://askubuntu.com/questions/244261/how-do-i-get-my-hp-laptop-to-boot-into-grub-from-my-new-efi-file)

可知，`cp /boot/efi/EFI/debian/grubx64.efi /boot/efi/EFI/boot/bootx64.efi`即可解决。

# 虚拟环境启动

话说我折腾这些是想解决什么问题呢？

公司的内网环境只能由域控下的Windows或MacOS接入，但日常的开发工作却又全都在Linux上，同时又不想使用虚拟磁盘，或是将公司电脑带去带来。那么就只能是在Windows host下使用第一类hypervisor启动我安装在固态U盘内的Linux系统了。

这里还要注意的是，虽然目前vmware workstation已经可以和Microsoft hyper-v共存。但在开启了hyper-v的Windows上，vmware workstation是无法做到CPU直通的，因为 Intel-VT/AMD-V已被hyper-v占据。hyper-v作为第二类hypervisor，实际上会将Windows本身也进行虚拟化，造成性能折损。所以我一般都是直接停用hyper-v，代价则是WSL由此就不可用了。

我们使用管理员权限启动vmware workstation（否则会导致没有直接访问磁盘硬件的权限），新建虚拟机时，选择使用物理磁盘而非虚拟磁盘：

<img src="https://s2.loli.net/2022/09/13/SIs5bEuYclqMjNW.png" alt="Snipaste_2022-09-13_17-02-37.png" style="zoom:50%;" />

固件类型选择UEFI：

<img src="https://s2.loli.net/2022/09/13/NeUqOAx6YG5oJCE.png" alt="Snipaste_2022-09-13_17-09-14.png" style="zoom:50%;" />

同时做如下选择避免快照：

<img src="https://s2.loli.net/2022/09/13/iVfThSdJYlzBEN8.png" alt="Snipaste_2022-09-13_17-06-03.png" style="zoom:50%;" />

即可成功启动：

![Snipaste_2022-09-13_17-12-20.png](https://s2.loli.net/2022/09/13/6tFW3Zmh4TU8ANO.png)