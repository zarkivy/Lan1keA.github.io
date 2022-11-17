---
title: "Android搞机笔记"
description: 垃机佬想上科班
date: 2022-09-05T16:56:14+08:00
image: "https://s2.loli.net/2022/09/13/u6ESQwpdvaU2igk.png"
categories: "赛博人生"
tags: [ "Android", "Mobile" ]
---



参考

- [https://www.jianshu.com/p/d960a6f517d8](https://www.jianshu.com/p/d960a6f517d8)
- [https://evilpan.com/2020/12/06/android-rooting/](https://evilpan.com/2020/12/06/android-rooting/)

## Fastboot

一个通信协议，用于直接向安卓手机Flash的不同分区中写入数据。

[https://android.googlesource.com/platform/system/core/+/refs/heads/master/fastboot/](https://android.googlesource.com/platform/system/core/+/refs/heads/master/fastboot/)

一般在bootloader中提供fastboot。连接电脑后可以执行fastboot命令。

## Recovery

一个单独的分区，内可包含一个恢复用的迷你系统，如TWRP。可以使用adb。

## TWRP

官网：[https://twrp.me/](https://twrp.me/)

于其中下载到对应手机型号所用TWRP镜像后，以USB调试模式连接到手机。

使用adb重启手机到fastboot：

```sh
adb reboot bootloader
```

刷入twrp镜像：

```sh
fastboot flash recovery twrp.img
```

重启手机：

```sh
fastboot reboot recovery
```

目前许多ROM会在第一次启动时重写recovery分区导致TWRP被官方recovery程序覆盖。所以我们要直接重启进入Recovery：

> Note many devices will replace your custom recovery automatically during first boot. To prevent this, use [Google](https://www.google.com/) to find the proper key combo to enter recovery. After typing fastboot reboot, hold the key combo and boot to TWRP. Once TWRP is booted, TWRP will patch the stock ROM to prevent the stock ROM from replacing TWRP. If you don't follow this step, you will have to repeat the install.

## 安卓设备分区

[https://source.android.com/devices/bootloader/partitions](https://source.android.com/devices/bootloader/partitions)