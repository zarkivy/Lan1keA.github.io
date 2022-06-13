---
title: "构建与运行嵌入式linux虚拟机"
description: "with buildroot and qemu-system"
date: 2022-05-10T20:59:43+08:00
tags: [ "QEMU", "Linux" ]
imagelink: "https://s2.loli.net/2022/05/11/nNfYF7Lz4i3hBDT.jpg"
---



# Easymode

## Default

通过git或http获取buildroot：

官网：https://buildroot.org/

git：`git clone https://git.busybox.net/buildroot`

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



# Manual Mode

另一篇使用qemu-system调试内核的文章中已有部分描述：

[http://cerr.cc/post/qemu_dbg_kernel/](/post/qemu_dbg_kernel/)

不过接下来还是记录下另一次实践。目标设定为aarch64 linux 5.4

## 编译工具链

这个好说，方才Easymode里不是刚好做了一个嘛。buildroot构建好了所有target目标，自然也为此生成了完整的host构建工具链。

> 若目标为旧版本内核，则需要旧版本工具链，构建方式在另一篇文章中有记录：[https://cerr.cc/post/qemu_dbg_kernel/](post/qemu_dbg_kernel/)

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

或者也可以来这里直接下载：[https://releases.linaro.org/components/toolchain/binaries/](https://releases.linaro.org/components/toolchain/binaries/)

或 `apt install gcc-aarch64-linux-gnu`

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

由于我们的目标平台是QEMU Virtio，故需要于`make menuconfig`中启用相应配置：`VIRTIO_BLK, SCSI_BLK, VIRTIO_NET, HVC_DRIVER, VIRTIO_CONSOLE, VIRTIO, VIRTIO_MMIO`。

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

