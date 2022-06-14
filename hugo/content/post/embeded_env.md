---
title: "嵌入式软件环境搭建一把梭"
description: "构建、运行与调试"
date: 2022-05-08T14:08:16+08:00
tags: [ "Embeded", "Linux" ]
imagelink: "https://s2.loli.net/2022/05/08/2DqjsbwNpdUM4XA.jpg"
---



# \x01 构建

## 手动选取编译工具链

> 命名遵循 arch-vendor-(os-)abi 的格式

获取编译工具链的方式，可以直接从这些地方下载到：

- arm cortex-a:[The GNU Toolchain for the Cortex-A Family Downloads](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-a/downloads)
- arm cortex-m:[GNU Arm Embedded Toolchain Downloads](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads)
- Linaro:arm:https://www.linaro.org/downloads/
- risc-v:https://github.com/riscv/riscv-gnu-toolchain/releases
- mips:https://www.mips.com/develop/tools/compilers/linux-toolchain/
- uclibc:https://www.uclibc.org/downloads/binaries/

- http://download.ronetix.info/toolchains/

也可以使用buildroot手动构建：

- https://buildroot.org/

### OS/ABI的匹配

通常情况下，选取编译交叉编译工具链时，指令集、平台之类的内容是不容易弄错的。但 OS/ABI 却是个容易造成故障的点。

举例来讲，在我一次对libnvram.so的编译时，得到的目标ELF文件为：

```sh
~$ file libnvram.so
libnvram.so: ELF 32-bit LSB shared object, ARM, version 1 (ARM), dynamically linked, not stripped

~$ armv5l-readelf -h libnvram.so                          
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 61 00 00 00 00 00 00 00 00 
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            ARM
  ......
```

使用该自行编译的libnvram.so导致报错：

```sh
/sbin/init: error while loading shared libraries: /lib/libnvram.so: ELF file OS ABI invalid
```

而原版无故障的libnvram.so为：

```sh
~$ file libnvram.so
libnvram.so: ELF 32-bit LSB shared object, ARM, EABI5 version 1 (SYSV), dynamically linked, not stripped

~$ armv5l-readelf -h libnvram.so                          
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00 
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ......
```

可以看到，两者的OS/ABI，一个是`ARM`，一个是`UNIX - System V`

> ABI描述应用程序与操作系统、应用程序与库、应用程序的组成部分之间的低层接口。ABI允许编译好的目标代码在使用兼容ABI的系统中无需改动就能运行

导致这一结果的原因是：使用OABI编译工具链编译出的结果为`OS/ABI: ARM`，而使用EABI编译工具链编译出的结果为`OS/ABI: UNIX - System V`。关于OABI与EABI的区别，参见：[https://docs.embeddedts.com/EABI_vs_OABI](https://docs.embeddedts.com/EABI_vs_OABI)

故换用标识了eabi的工具链重新编译即解决了该问题。

## buildroot一把梭

构建这一步，能用好buildroot的话基本就没有多少需要手动配置的步骤了。

构建特定的目标前，我们要确定好各种参数：指令集架构、工具链、链接库……这当然都是可以经由`make menuconfig`一点点设定好的。不过更常用更快捷的方式，还是直接指定一个完全满足如上所有参数的特定平台，使用其defconfig即可。

眼下我需要一个4.9LTS版本的内核，就以此为例了。

首先由git仓库获取buildroot，而非由http下载特定发行包。这样做带来的方便是“一次下载，终生更新，随时切换”：

```sh
git clone https://git.buildroot.net/buildroot
```

由于内核版本对于构建环境的依赖，往往容易出现各种环境不匹配导致的编译失败，即是使用buildroot也自动构建也一样。所以我通常有如下三种递增的解决方案：

- 最新版本buildroot中直接指定旧版本内核进行构建
- 在与目标内核版本匹配的旧版本buildroot中进行构建
- 在与 目标版本内核和buildroot版本 都匹配的 旧版发行版的docker容器中 进行构建

由上到下，麻烦度递增，但构建成功率也递增。

这里选取方案二继续进行演示。

来此查询到Linux Kernel 4.9 LTS的发布日期：[https://en.wikipedia.org/wiki/Linux_kernel_version_history](https://en.wikipedia.org/wiki/Linux_kernel_version_history)

![image.png](https://s2.loli.net/2022/06/14/avF3QMcVGLpreN4.png)

2016年12月。嗯，看看同时期的buildroot版本有：

```sh
➜  buildroot git:(master) git switch 
2012.11.x  2016.08.x  2017.08.x  2018.08.x  2019.08.x  2020.08.x  2021.08.x  next
2013.08.x  2016.11.x  2017.11.x  2018.11.x  2019.11.x  2020.11.x  2021.11.x
2015.08.x  2017.02.x  2018.02.x  2019.02.x  2020.02.x  2021.02.x  2022.02.x
2015.11.x  2017.05.x  2018.05.x  2019.05.x  2020.05.x  2021.05.x  master
```

晚于内核发布且最贴近的是2017.02.x，switch过去：

```sh
➜  buildroot git:(master) git switch 2017.02.x
Branch '2017.02.x' set up to track remote branch '2017.02.x' from 'origin'.
Switched to a new branch '2017.02.x'
```

由于我需要的目标平台是aarch64的virt，故搜索buildroot提供的默认配置defconfig并使用。接着`make`开始构建即可：

```sh
➜  buildroot git:(2017.02.x) ls configs | grep qemu | grep aarch
qemu_aarch64_virt_defconfig
➜  buildroot git:(2017.02.x) make qemu_aarch64_virt_defconfig
……
➜  buildroot git:(2017.02.x) make
……
```

喝几杯咖啡的时间（具体是几杯取决于你host的配置与网络环境），就能在output中拿到所有的结果：编译工具链、内核、链接库、rootfs、可引导磁盘镜像、qemu虚拟机一键启动脚本……

☕️☕️☕️……

好诶！居然又失败了😩👇

```sh
make: *** [package/pkg-generic.mk:219: /buildroot/output/build/host-m4-1.4.18/.stamp_built] Error 2
```

所以还得接着写。启动方案三，准备docker container：

```sh
➜  ~ docker pull ubuntu:18.04
18.04: Pulling from library/ubuntu
09db6f815738: Pull complete
Digest: sha256:478caf1bec1afd54a58435ec681c8755883b7eb843a8630091890130b15a79af
Status: Downloaded newer image for ubuntu:18.04
docker.io/library/ubuntu:18.04
➜  ~ docker run -it -v /vda/share:/to_host ubuntu:18.04 /bin/bash
root@c1c9ce358373:/# exit
➜  ~ docker start c1; docker exec -it c1 /bin/bash
c1
root@c1c9ce358373:/# 
```

好了，这下buildroot也在老环境内了，再次重复上述步骤即可。



# \x02 运行





# \x03 调试



