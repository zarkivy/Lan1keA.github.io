---
title: "构建与运行嵌入式linux虚拟机"
description: "with buildroot and qemu-system"
date: 2022-05-10T20:59:43+08:00
tags: [ "虚拟化", "Linux" ]
imagelink: "https://s2.loli.net/2022/05/11/nNfYF7Lz4i3hBDT.jpg"
---



## Easymode

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



## Manual Mode

另一篇使用qemu-system调试内核的文章中已描述：

[http://zikey.vip/post/qemu_dbg_kernel/](/post/qemu_dbg_kernel/)
