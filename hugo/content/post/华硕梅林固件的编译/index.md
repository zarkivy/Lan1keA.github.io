---
title: "华硕梅林固件的编译"
description: "记录一下手动编译asuswrt-merlin的过程"
date: 2022-05-11T16:27:10+08:00
categories: "工地日记"
tags: [ "Embeded", "Compile" ]
image : "https://www.asuswrt-merlin.net/sites/default/files/inline-images/main_page_sm_2.png"
---



# 获取梅林

官网如下：[asuswrt-merlin.net](http://asuswrt-merlin.net)

新梅林：https://github.com/RMerl/asuswrt-merlin.ng

旧梅林（停止开发）：https://github.com/RMerl/asuswrt-merlin

这里选用asuswrt-merlin.ng进行编译构建

```sh
wget https://github.com/RMerl/asuswrt-merlin.ng/archive/refs/heads/master.zip
```

（下载zip而非clone仓库，节省.git的占用



# 环境准备

README.txt 给出了编译方式：[https://github.com/RMerl/asuswrt-merlin.ng/blob/master/README.TXT](https://github.com/RMerl/asuswrt-merlin.ng/blob/master/README.TXT)

包依赖非常多，所以尽力将本地环境贴合它的编译说明，以避免未知错误。

所以选取 ubuntu:16.04 作为编译平台。

故首先安装docker，快速安装cheatsheet在此：[http://zikey.vip/post/some_installation/](/post/some_installation/)

拉取ubuntu 16.04镜像并设定共享文件夹：

```sh
sudo docker run -it -v /home/zkv/docker_share/:/share ubuntu:16.04
```



# 依赖安装

进入docker ubuntu环境后，下载并解压梅林ng：

```sh
apt install wget unzip
wget https://github.com/RMerl/asuswrt-merlin.ng/archive/refs/heads/master.zip
unzip master.zip
```

README.txt 中提到，需安装部分32位依赖包，故先添加32位软件源：

```sh
dpkg --add-architecture i386
apt update
```

此后便可以直接依照说明安装所有依赖包：

```sh
apt install libncurses5 libncurses5-dev m4 bison gawk flex libstdc++6-4.7-dev g++-4.7 g++ gengetopt git gitk zlib1g-dev autoconf autopoint libtool-bin shtool autogen mtd-utils intltool sharutils docbook-xsl-* libstdc++5 texinfo dos2unix xsltproc u-boot-tools device-tree-compiler qemu gperf liblzo2-dev uuid-dev build-essential lzma-dev liblzma-dev lzma binutils-dev patch cmake intltool libglib2.0-dev gtk-doc-tools libc6-i386 lib32stdc++6 lib32z1 libelf1:i386 lib32ncurses5 libc6-dev-i386
```

接着获取梅林提供的编译工具链：

```sh
cd $HOME
git clone https://github.com/RMerl/am-toolchains
```

将工具链目录导出至环境变量，参见 [https://github.com/RMerl/asuswrt-merlin.ng/wiki/Compile-Firmware-from-source](https://github.com/RMerl/asuswrt-merlin.ng/wiki/Compile-Firmware-from-source)：

我这里的目标是 RT-AC86U，故：

```sh
sudo ln -s ~/am-toolchains/brcm-arm-hnd /opt/toolchains
echo "export LD_LIBRARY_PATH=\$LD_LIBRARY:/opt/toolchains/crosstools-arm-gcc-5.3-linux-4.1-glibc-2.22-binutils-2.25/usr/lib" >> ~/.profile
echo "export TOOLCHAIN_BASE=/opt/toolchains" >> ~/.profile
echo "PATH=\$PATH:/opt/toolchains/crosstools-arm-gcc-5.3-linux-4.1-glibc-2.22-binutils-2.25/usr/bin" >> ~/.profile
echo "PATH=\$PATH:/opt/toolchains/crosstools-aarch64-gcc-5.3-linux-4.1-glibc-2.22-binutils-2.25/usr/bin" >> ~/.profile
```



# 开始编译

```sh
cd ~/release/src-rt-5.02hnd
make rt-ac86u
```



# Easymod

如上所述手动折腾到一半，发现了有简单模式的自动化方案。README内的信息实在太少，github wiki里有官方的编译构建教程：

官方wiki：

[https://github.com/RMerl/asuswrt-merlin.ng/wiki/](https://github.com/RMerl/asuswrt-merlin.ng/wiki/)

组件编译：

[https://github.com/RMerl/asuswrt-merlin.ng/wiki/Compile-custom-programs-from-source](https://github.com/RMerl/asuswrt-merlin.ng/wiki/Compile-custom-programs-from-source)

固件编译：

[https://github.com/RMerl/asuswrt-merlin.ng/wiki/Compile-Firmware-from-source](https://github.com/RMerl/asuswrt-merlin.ng/wiki/Compile-Firmware-from-source)

同时有现成的docker环境：

https://github.com/gnuton/Asuswrt-Merlin-Toolchains-Docker

~~前面，白干。~~

