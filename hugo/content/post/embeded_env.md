---
title: "嵌入式软件环境搭建一把梭"
description: "构建、运行与调试"
date: 2022-05-08T14:08:16+08:00
tags: [ "Linux", "Embeded" ]
imagelink: "https://s2.loli.net/2022/05/08/2DqjsbwNpdUM4XA.jpg"
---



## \x01 构建

### 编译工具链

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

#### OS/ABI的匹配

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

## \x02 运行





## \x03 调试



