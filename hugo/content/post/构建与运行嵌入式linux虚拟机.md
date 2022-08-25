---
title: "构建与运行嵌入式linux虚拟机"
description: "with buildroot and qemu-system"
date: 2022-05-10T20:59:43+08:00
categories: "工地日记"
tags: [ "QEMU", "Linux", "Compile" ]
image: "https://s2.loli.net/2022/05/08/2DqjsbwNpdUM4XA.jpg"
---



# 自动挡

## 一路默认

通过git或http获取buildroot：

官网：https://buildroot.org/

git：`git clone https://git.buildroot.net/buildroot`

进入buildroot根目录，可以看到一个叫configs的文件夹，其中记录了许多硬件平台和部分qemu虚拟平台的默认配置参数。执行 `ls configs | grep qemu`，可以看到：

```ini
qemu_aarch64_sbsa_defconfig
qemu_aarch64_virt_defconfig
qemu_arm_versatile_defconfig
qemu_arm_versatile_nommu_defconfig
qemu_arm_vexpress_defconfig
qemu_arm_vexpress_tz_defconfig
qemu_m68k_mcf5208_defconfig
qemu_m68k_q800_defconfig
qemu_microblazebe_mmu_defconfig
qemu_microblazeel_mmu_defconfig
qemu_mips32r2el_malta_defconfig
qemu_mips32r2_malta_defconfig
qemu_mips32r6el_malta_defconfig
qemu_mips32r6_malta_defconfig
qemu_mips64el_malta_defconfig
qemu_mips64_malta_defconfig
qemu_mips64r6el_malta_defconfig
qemu_mips64r6_malta_defconfig
qemu_nios2_10m50_defconfig
qemu_or1k_defconfig
qemu_ppc64_e5500_defconfig
qemu_ppc64le_powernv8_defconfig
qemu_ppc64le_pseries_defconfig
qemu_ppc64_pseries_defconfig
qemu_ppc_bamboo_defconfig
qemu_ppc_e500mc_defconfig
qemu_ppc_g3beige_defconfig
qemu_ppc_mac99_defconfig
qemu_ppc_mpc8544ds_defconfig
qemu_riscv32_virt_defconfig
qemu_riscv64_virt_defconfig
qemu_s390x_defconfig
qemu_sh4eb_r2d_defconfig
qemu_sh4_r2d_defconfig
qemu_sparc64_sun4u_defconfig
qemu_sparc_ss10_defconfig
qemu_x86_64_defconfig
qemu_x86_defconfig
qemu_xtensa_lx60_defconfig
qemu_xtensa_lx60_nommu_defconfig
```

举例来讲，我们使用 `make qemu_arm_vexpress_defconfig`，将qemu模拟的vexperss平台设为编译目标：

```sh
make qemu_arm_vexpress_defconfig
make
```

或者使用 make menuconfig 手动配置。

等待编译完成，即可在output文件夹中拿到相应目标文件：

```sh
output/images/zImage               # 内核
output/images/rootfs.ext2          # ext2根文件系统镜像
output/images/vexpress-v2p-ca9.dtb # arm设备树
```

使用qemu-system-arm运行得到的linux：

```sh
qemu-system-arm \
	-M vexpress-a9 \
	-smp 1 \
	-m 256 \
	-kernel output/images/zImage \
	-dtb output/images/vexpress-v2p-ca9.dtb \
	-drive file=output/images/rootfs.ext2,if=sd,format=raw \
	-append "console=ttyAMA0,115200 root=/dev/mmcblk0" \
	-serial stdio \
	-nographic
```

弄完发现过程竟如此简单，buildroot把所有活都干了……

## 全自动方案中的定制

即使需要定制部分组件，也是能借力buildroot的全自动化方案的。

先导入自己需要的全自动方案的默认配置，再使用`make menuconfig`对其生成的`.config`进行定制：

```sh
make qemu_aarch64_virt_defconfig
make menuconfig
```

举例来说，如果我的需求是使用第三方内核，则修改：

```sh
 Kernel  --->
         Kernel version (Custom Git repository)
```

## 记一次旧版本工具链的自动挡构建

> 这一节实际上由另一篇文章合并而来，故与上下文不衔接，内容独立。

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
➜  ~ docker run -it -v /host_path:/guest_path ubuntu:18.04 /bin/bash
root@c1c9ce358373:/# exit
➜  ~ docker start c1; docker exec -it c1 /bin/bash
c1
root@c1c9ce358373:/# 
```

好了，这下buildroot也在老环境内了，再次重复上述步骤即可。

换用ubuntu18.04果然成功了、

如果你和我一样懒得弄共享文件夹，就直接来`/var/lib/docker/overlay2`中取货吧。毕竟docker只是在文件系统层做了虚拟化，甚至连虚拟磁盘都不需要，直接在host的文件系统中存放了guest的文件。

```sh
➜  ~ cd /var/lib/docker/overlay2
➜  overlay2 du -h -d 1
7.8G    ./d8ef00627999edcb9a6fee28d49bf9312f3bb56ca62101ce8850b3b406c4c498
40K     ./d8ef00627999edcb9a6fee28d49bf9312f3bb56ca62101ce8850b3b406c4c498-init
69M     ./cd42e49af35fa4606d2b1f029e089cc3caca7658ff0b6e2bca950dc7379318ee
16K     ./l
7.9G    .
➜  overlay2 cd d8ef00627999edcb9a6fee28d49bf9312f3bb56ca62101ce8850b3b406c4c498/merged/
➜  merged ls
bin  boot  buildroot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```

接着进入`buildroot/output`取货即可。虽然其中已有4.9.6的内核了，不过我需要的是由此得到的编译工具链，以便编译自行定制的4.9版本内核。（旧版内核需要旧版工具链编译）

# 手动档

## 编译工具链

这个好说，方才Easymode里不是刚好做了一个嘛。buildroot构建好了所有target目标，自然也为此生成了完整的host构建工具链。

> 若目标为旧版本内核，则需要旧版本工具链，构建方式在另一篇文章中有记录：[https://cerr.cc/post/cross-debug-old-version-kernel-using-qemu-system/](post/cross-debug-old-version-kernel-using-qemu-system/)

```sh
➜  buildroot git:(master) ls
arch   CHANGES           configs     dl    linux            output   support    utils
board  Config.in         COPYING     docs  Makefile         package  system
boot   Config.in.legacy  DEVELOPERS  fs    Makefile.legacy  README   toolchain

➜  buildroot git:(master) ls output
build  host  images  staging  target

➜  buildroot git:(master) ls output/build
buildroot-config                  host-meson-0.62.1
buildroot-fs                      host-mpc-1.2.1
build-time.log                    host-mpfr-4.1.0
busybox-1.35.0                    host-ninja-1.10.2.g51db2.kitware.jobserver-1
host-acl-2.3.1                    host-openssl
host-attr-2.5.1                   host-patchelf-0.9
host-autoconf-2.71                host-pcre-8.45
host-autoconf-archive-2021.02.19  host-pixman-0.40.0
host-automake-1.16.5              host-pkgconf-1.6.3
host-binutils-2.37                host-python3-3.10.4
host-bison-3.8.2                  host-python-setuptools-62.1.0
host-cmake-3.18.6                 host-qemu-7.0.0
host-dtc-1.6.1                    host-skeleton
host-e2fsprogs-1.46.5             host-util-linux-2.38
host-expat-2.4.7                  host-zlib
host-fakeroot-1.26                ifupdown-scripts
host-flex-2.6.4                   initscripts
host-gcc-final-10.3.0             linux-5.15.18
host-gcc-initial-10.3.0           linux-headers-5.15.18
host-gettext                      locales.nopurge
host-gettext-tiny-0.3.2           packages-file-list-host.txt
host-gmp-6.2.1                    packages-file-list-staging.txt
host-kmod-29                      packages-file-list.txt
host-libffi-3.4.2                 skeleton
host-libglib2-2.70.4              skeleton-init-common
host-libopenssl-1.1.1o            skeleton-init-sysv
host-libtool-2.4.6                toolchain
host-libzlib-1.2.12               toolchain-buildroot
host-m4-1.4.19                    uclibc-1.0.40
host-makedevs                     urandom-scripts

➜  buildroot git:(master) ls output/host
aarch64-buildroot-linux-uclibc  bin  doc  etc  include  lib  lib64  libexec  sbin  share  usr  var

➜  buildroot git:(master) ls output/target
bin  etc  lib64    media  opt   root  sbin  THIS_IS_NOT_YOUR_ROOT_FILESYSTEM  usr
dev  lib  linuxrc  mnt    proc  run   sys   tmp                               var

➜  buildroot git:(master) ls output/images
Image  rootfs.ext2  rootfs.ext4  start-qemu.sh

➜  buildroot git:(master) ls output/staging
bin  dev  etc  lib  lib64  media  mnt  opt  proc  root  run  sbin  sys  tmp  usr

➜  buildroot git:(master) file output/staging
output/staging: symbolic link to /f/software/buildroot/output/host/aarch64-buildroot-linux-uclibc/sysroot
```

`output/host`下即是面向buildroot构建前设定的目标平台的编译工具链。

> 编译工具链的命名遵循 arch-vendor-(os-)abi 的格式

同时也可以直接从这些地方下载到：

- arm cortex-a:[The GNU Toolchain for the Cortex-A Family Downloads](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-a/downloads)
- arm cortex-m:[GNU Arm Embedded Toolchain Downloads](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads)
- Linaro:arm:https://www.linaro.org/downloads/
- risc-v:https://github.com/riscv/riscv-gnu-toolchain/releases
- mips:https://www.mips.com/develop/tools/compilers/linux-toolchain/
- uclibc:https://www.uclibc.org/downloads/binaries/

- http://download.ronetix.info/toolchains/

若实在没有啥特殊需求， `apt install gcc-aarch64-linux-gnu`大部分情况下也能直接解决问题。

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

## Kernel

### Linux源码结构

手动构建内核前，先了解下内核源码的文件树结构。以2022/6/1的linux源码为例：

```sh
➜  linux git:(master) ls
arch   COPYING  Documentation  include  Kbuild   lib          Makefile  README   security  usr
block  CREDITS  drivers        init     Kconfig  LICENSES     mm        samples  sound     virt
certs  crypto   fs             ipc      kernel   MAINTAINERS  net       scripts  tools
```

- **arch**
  - The `arch` subdirectory contains all of the architecture specific kernel code. It has further subdirectories, one per supported architecture, for example `i386` and `alpha`.
- **include**
  - The `include` subdirectory contains most of the include files needed to build the kernel code. It too has further subdirectories including one for every architecture supported. The `/include/asm` subdirectory is a soft link to the real include directory needed for this architecture, for example `/include/asm-i386`. To change architectures you need to edit the kernel makefile and rerun the Linux kernel configuration program.
- **init**
  - This directory contains the initialization code for the kernel and it is a very good place to start looking at how the kernel works.
- **mm**
  - This directory contains all of the memory management code. The architecture specific memory management code lives down in `/arch/*/mm/`, for example `/arch/i386/mm/fault.c`.
- **drivers**
  - All of the system's device drivers live in this directory. They are further sub-divided into classes of device driver, for example `block`.
- **ipc**
  - This directory contains the kernels inter-process communications code.
- **modules**
  - This is simply a directory used to hold built modules.
- **fs**
  - All of the file system code. This is further sub-divided into directories, one per supported file system, for example `vfat` and `ext2`.
- **kernel**
  - The main kernel code. Again, the architecture specific kernel code is in `/arch/*/kernel`.
- **net**
  - The kernel's networking code.
- **lib**
  - This directory contains the kernel's library code. The architecture specific library code can be found in `/arch/*/lib/`.
- **scripts**
  - This directory contains the scripts (for example *awk* and *tk* scripts) that are used when the kernel is configured.

### Linux构建系统

同时需要先了解下 `.config`、`defconfig`、`Kconfig`

- https://stackoverflow.com/questions/41885015/what-exactly-does-linux-kernels-make-defconfig-do
- https://www.linuxjournal.com/content/kbuild-linux-kernel-build-system

总的来说，the Linux Kernel Build System has four main components:

- Config symbols: compilation options that can be used to compile code conditionally in source files and to decide which objects to include in a kernel image or its modules.
- Kconfig files: define each config symbol and its attributes, such as its type, description and dependencies. Programs that generate an option menu tree (for example, `make menuconfig`) read the menu entries from these files.
- .config file: stores each config symbol's selected value. You can edit this file manually or use one of the many `make` configuration targets, such as menuconfig and xconfig, that call specialized programs to build a tree-like menu and automatically update (and create) the .config file for you.
- Makefiles: normal GNU makefiles that describe the relationship between source files and the commands needed to generate each make target, such as kernel images and modules.

至于`defconfig`，则一般由平台厂商提供，里面含有了目标平台相关的一些默认参数，内核编译用做.config的参考。同时遵守如下规则：

- if option is mentioned in `defconfig`, build system puts that option into `.config` with value chosen in `defconfig`
- if option isn't mentioned in `defconfig`, build system puts that option into `.config` using its default value, specified in corresponding `Kconfig`

由此确保构建出的内核与特定平台匹配。

`defconfig`文件存放于`arch/*/configs/`路径下，如：

```sh
➜  linux git:(master) ls arch/arm/configs/
am200epdkit_defconfig     gemini_defconfig         multi_v5_defconfig    s5pv210_defconfig
aspeed_g4_defconfig       h3600_defconfig          multi_v7_defconfig    sama5_defconfig
aspeed_g5_defconfig       h5000_defconfig          mv78xx0_defconfig     sama7_defconfig
......
```

至于`make defconfig`究竟干了啥，可以使用`V=1`参数查看：

```sh
make V=1 defconfig
```

### 开始构建

现来此挑选中意的内核版本：[https://en.wikipedia.org/wiki/Linux_kernel_version_history](https://en.wikipedia.org/wiki/Linux_kernel_version_history)

我这边以5.4版本内核为例继续实验：

```sh
git clone https://github.com/torvalds/linux.git
# 1day mirror
git clone https://gitee.com/mirrors/linux_old1.git
cd linux
# 切换到特定版本
git checkout v5.4
# 创建目标文件夹
mkdir build
# 导入aarch64的defconfig
make ARCH=arm64 O=./build defconfig
# 自定义配置
make O=./build menuconfig
```

由于我们的目标平台是QEMU Virtio，故需要于`make menuconfig`中启用相应配置：`VIRTIO_BLK, SCSI_BLK, VIRTIO_NET, HVC_DRIVER, VIRTIO_CONSOLE, VIRTIO, VIRTIO_MMIO`。详细的Kernel config参考也可以从`buildroot/board/qemu`中找到。

至于Virtio具体是个啥，参考：

- [https://wiki.libvirt.org/page/Virtio](https://wiki.libvirt.org/page/Virtio)
- [https://wiki.osdev.org/Virtio](https://wiki.osdev.org/Virtio)
- [https://www.linux-kvm.org/page/Virtio](https://www.linux-kvm.org/page/Virtio)
- https://zhuanlan.zhihu.com/p/68154666
- https://www.anquanke.com/post/id/224001
- https://developer.ibm.com/articles/l-virtio/

在`make menuconfig`中启用路径如下：

```sh
Device Drivers  --->
        [*] Virtio drivers

Device Drivers  --->
        [*] Network device support  --->
                <*>   Virtio network driver

Device Drivers  --->
        [*] Block devices  --->
                <*>   Virtio block driver
```

如想要手动添加内核驱动，参考：[https://stackoverflow.com/questions/11710022/adding-new-driver-code-to-linux-source-code](https://stackoverflow.com/questions/11710022/adding-new-driver-code-to-linux-source-code)

> Tip: If you want a sample of linux.config for a specific platform, take a look at `buildroot/board`

配置完毕，开始编译：

```sh
make O=./build CROSS_COMPILE=aarch64-linux-gnu- -j`nproc`
```

> 注意将 `aarch64-linux-gnu-` 换成你当前的编译工具链路径

得到的Image和vmlinux路径为：

```sh
➜  linux git:(219d54332a09) find . -name Image
./build/arch/arm64/boot/Image
➜  linux git:(219d54332a09) find . -name vmlinux
./build/vmlinux
```

## 通过qemu-system运行

### 网桥的预配置

后续host于qemu-system中的guest将通过网桥连接，故先于host中配置网桥。

首先准备网桥与tun/tap虚拟网卡：

创建网桥br0：

```sh
sudo brctl addbr br0
```

为当前用户创建tun/tap虚拟网卡tap0：

```sh
tunctl -t tap0 -u ${USER}
```

若发现brctl与tunctl命令未找到，则需要先安装相应软件包。这里推荐一个网站，可以方便的查询一个命令在不同软件源中对应的包名：[https://command-not-found.com/](https://command-not-found.com/)（专治command not found 20 年）
