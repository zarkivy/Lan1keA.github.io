---
title: "手机变身小主机？！"
description: "GNU/Linux on a phone"
date: 2022-07-19T10:50:06+08:00
tags: [ "Linux", "Life" ]
imagelink: "https://postmarketos.org/static/img/nexus5-bg-dark.jpg"
---

# ~~快递还在路上……~~

话说我大学专业之所以选了计算机，除去高二于学霸本*联想G510*上玩过《看门狗》的原因外，就是因为有着折腾二手安卓机的爱好了。这一兴趣一直延续到了大学，以至于我童叟无欺的闲鱼账号被人怀疑为是机贩子账号~~（😤是可忍，孰不可忍）~~。直至大三开始搞到了外快，可以不再为一台多次易主的设备在经济上精打细算，便渐渐失去了往日的激情。果然太容易得到的东西，就会失宠。

还记得大二那次Essential Phone换LG V30的交易，在我和那位小米前员工约定的地铁站内，他说，从我身上仿佛看见了当年的他，为一台心心念念的破旧设备奔波几十里却满心欢喜。两名垃机佬相视一笑，心照不宣，你东我西，从此再无交集……（😭

# Linux On a Phone

啥说是手机上的Linux？安卓的内核本来就是Linux呀？！

但那是Android/Linux，而我们本次的目标是GNU/Linux，将mainline kernel与GNU utils运行与手机之上。可以比喻成将手机刷成了一台运行了桌面级系统的小主机？

关于Android的Linux内核与mainline Linux内核的关系，参见Google的说明：[https://source.android.com/devices/architecture/kernel?hl=zh-cn](https://source.android.com/devices/architecture/kernel?hl=zh-cn)

有若干项目试图接近这个目标，其中最有名的应该还是Ubuntu Touch了。但经过一些调研后，我发现Ubuntu Touch并不是我满意的选择……

## Ubuntu Touch

[https://ubuntu-touch.io/](https://ubuntu-touch.io/)

Ubuntu Touch借助了[Haium](https://halium.org/)以解决纷繁复杂的硬件驱动支持问题，虽然由此达成了将GNU utils无缝运行于移动设备的目的，但却也因此被迫舍弃了mainline kernel。具体技术架构参见[https://halium.org/](https://halium.org/)即可。

![architecture](https://halium.org/img/architecture.png)

似乎Ubuntu Touch的定位主要瞄准了Android，而非作为一个完全可定制的极客OS，所以在包管理、分区读写等方面并不能做到如同Ubuntu desktop一样的体验。再加上其没有使用mainline kernel不符合我的需求，故而弃之。

## Mobian

[https://mobian.org/](https://mobian.org/)

[https://wiki.mobian.org/](https://wiki.mobian.org/)

Mobian，即mobile debian，好的地方在于其已为大家广泛熟悉的apt+dpkg包管理工具和软件源，且其使用了mainline内核。但缺乏对于大量设备的支持。Mobian的设备支持列表在这里：[https://wiki.mobian.org/doku.php?id=devices](https://wiki.mobian.org/doku.php?id=devices)。可以看到除去几款国内难以购买、价格高昂、配置低下的开源手机外，就只有一加6和小米Pocophone F1两款骁龙845机型了。

## Droidian

[https://droidian.org/](https://droidian.org/)

Mobian的Haium版本，借由和Ubuntu Touch类似的方案，达成支持更多设备的目的。但也因此放弃了mainline kernel。

## postmarketOS

[https://postmarketos.org/](https://postmarketos.org/)

postmarketOS基于Alpine Linux，为busybox + musl libc的解决方案。在采用mainline kernel的mobile发行版中社区最大，维护最活跃。其支持的设备列表与支持情况可以于此查到：[https://wiki.postmarketos.org/wiki/Devices](https://wiki.postmarketos.org/wiki/Devices)

以我手上的Xiaomi Mi Note2为例，对其的许多驱动支持是postmarketOS独一份的，比如此前修复的Wi-Fi模块：[https://gitlab.com/postmarketOS/pmaports/-/merge_requests/3271](https://gitlab.com/postmarketOS/pmaports/-/merge_requests/3271)

postmarketOS集成了几乎所有为mobile生态做出努力的上层桌面环境：[https://wiki.postmarketos.org/wiki/Category:Interface](https://wiki.postmarketos.org/wiki/Category:Interface)。且与软件源中提供了一键安装与切换。不同设备所适合的DE并不同，我此前的Xiaomi Redmi2使用KDE Plasma mobile最舒适，而现在手上这台Xiaomi Mi Note2运行Phosh才最稳定。

其亦有国内软件源镜像的支持：[https://mirrors.tuna.tsinghua.edu.cn/postmarketOS/](https://mirrors.tuna.tsinghua.edu.cn/postmarketOS/)

换源说明：[https://mirrors.tuna.tsinghua.edu.cn/help/postmarketOS/](https://mirrors.tuna.tsinghua.edu.cn/help/postmarketOS/)

将软件源由特定发行版切换到master分支后，即相当于将系统转化为滚动更新模式。这样做的好处是，昨天上报的bug可能今天就被修复了，下游设备立即执行`apk update; apk upgrade`即可立即获取修复的更新。缺点则是是master分支的不稳定。但考虑到mobile发行版的可用性与稳定性本身就堪忧，切换至滚动更新，或许是牺牲1份稳定性，换取5分可用性。在我看来是很划算的生意。

## 其余暂未进一步了解的Mobile Distros

### Sailfish OS

[https://sailfishos.org/](https://sailfishos.org/)

体量貌似也很大，但其官网首页就是标价，预感不妙，逃……

### LuneOS

[http://www.webos-ports.org/wiki/Main_Page](http://www.webos-ports.org/wiki/Main_Page)

也是Haium方案。

### PureOS、Manjaro ARM等

[https://pureos.net/](https://pureos.net/)

[https://manjaro.org/](https://manjaro.org/)

其定位并非为移动端努力。只能说是支持了Pine Phone等开源手机而已。

# 关于刷机

参考[https://www.jianshu.com/p/d960a6f517d8](https://www.jianshu.com/p/d960a6f517d8)

## Fastboot

一个通信协议，用于直接向安卓手机Flash的不同分区中写入数据。

[https://android.googlesource.com/platform/system/core/+/refs/heads/master/fastboot/](https://android.googlesource.com/platform/system/core/+/refs/heads/master/fastboot/)

## Recovery

一个单独的分区，内可包含一个恢复用的迷你系统，如TWRP。

## 安卓设备分区

[https://source.android.com/devices/bootloader/partitions](https://source.android.com/devices/bootloader/partitions)
