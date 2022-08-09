---
title: "使用QEMU+GDB调试Linux内核"
description: "学好Kernel的基操？"
date: 2022-08-08T14:17:44+08:00
tags: [ "QEMU", "Linux" ]
imagelink: "https://s2.loli.net/2022/08/09/PKcwjiVBTg6ApEv.png"
---



# 使用buildroot一键搭建环境

> buildroot自动构建过程若出现网络不畅，自备梯子，并设定shell变量all_proxy、http_proxy、https_proxy用于代理wget、curl等工具即可。参见：[https://cerr.cc/post/fgfw/](/post/fgfw/)

```sh
git clone https://git.buildroot.net/buildroot
cd buildroot
make qemu_x86_64_defconfig
make linux-menuconfig
```

使用 / 搜索 DEBUG_INFO 符号（即配置文件中的CONFIG_DEBUG_INFO符号） 设定路径与依赖，将其打开。

![image.png](https://s2.loli.net/2022/08/09/PKcwjiVBTg6ApEv.png)

此后即可直接make构建。

```sh
make -j`nproc`
# 编译构建中……
# ☕️、☕️、☕️……
# 编译构建完成
output/images/start-qemu.sh
```

# 先来确认一切正常

```sh
cd output/images
qemu-system-x86_64 -M pc -kernel bzImage \
	-drive file=rootfs.ext2,if=virtio,format=raw \
	-append "rootwait root=/dev/vda console=tty1 console=ttyS0" \
	-net nic,model=virtio -net user \
	-nographic -s -S
```

- -nographic 全部信息输出至host stdio
    - 若否，而是使用-serial stdio，则只会将guest的串口数据输出至host stdio，其余则会输出至启动的vncserver
- -s              shorthand for -gdb tcp::1234
- -S              freeze CPU at startup (use 'c' to start execution)

此时于另一个tty、pts：

```sh
gdb -ex "file ../build/linux-5.15.18/vmlinux" \
	-ex "target remote :1234"
```

