---
title: "使用QEMU+GDB调试Linux内核"
description: "学好Kernel的基操？"
date: 2022-08-08T14:17:44+08:00
tags: [ "QEMU", "Linux" ]
imagelink: "https://s2.loli.net/2022/08/09/PKcwjiVBTg6ApEv.png"
---



# 环境准备

kernel对于gdb调试所提供的文档如下：

[https://www.kernel.org/doc/html/latest/dev-tools/gdb-kernel-debugging.html](https://www.kernel.org/doc/html/latest/dev-tools/gdb-kernel-debugging.html)

## QEMU 的手动构建

获取QEMU源码：

```sh
git clone https://gitlab.com/qemu-project/qemu.git
cd qemu
git submodule init
git submodule update --recursive
```

本次使用x86_64内核的情况下，configure配置如下：

```sh
./configure --target-list=x86_64-softmmu --enable-debug
```

编译并安装：

```sh
make -j`nproc`
sudo make install
```

## 使用buildroot一键构建rootfs+kernel

> buildroot自动构建过程若出现网络不畅，自备梯子，并设定shell变量all_proxy、http_proxy、https_proxy用于代理wget、curl等工具即可。参见：[https://cerr.cc/post/fgfw/](/post/fgfw/)

```sh
git clone https://git.buildroot.net/buildroot
cd buildroot
make qemu_x86_64_defconfig
make linux-menuconfig
```

使用 / 搜索 DEBUG_INFO 符号（即配置文件中的CONFIG_DEBUG_INFO符号） 设定路径与依赖，将其打开。

![image.png](https://s2.loli.net/2022/08/09/PKcwjiVBTg6ApEv.png)

以同样的方式再将 GDB_SCRIPT 打开、RANDOMIZE_BASE 关闭。

此后即可直接make构建。

```sh
make
# 编译构建中……
# ☕️、☕️、☕️……
# 编译构建完成
output/images/start-qemu.sh
```

测试是否正常启动：（上一步若关闭了RANDOMIZE_BASE，则无需向kernel传递nokaslr参数）

```sh
cd output/images
qemu-system-x86_64 -M pc -kernel bzImage \
	-drive file=rootfs.ext2,if=virtio,format=raw \
	-append "rootwait root=/dev/vda console=tty1 console=ttyS0 nokaslr" \
	-net nic,model=virtio -net user \
	-nographic -s -S
```

- -nographic 全部信息输出至host stdio
    - 若否，而是使用-serial stdio，则只会将guest的串口数据输出至host stdio，其余则会输出至启动的vncserver
- -s              shorthand for -gdb tcp::1234
- -S              freeze CPU at startup (use 'c' to start execution)

此时于另一个tty、pts：

```sh
sudo gdb -ex "file ../build/linux-5.15.18/vmlinux" \
		 -ex "target remote :1234"
pwndbg> b start_kernel
pwndbg> c
```

确认成功断下执行流即可。

## 手动构建kernel+initramfs

若不想借助自动化工具buildroot传达旨意，而是要亲自指挥亲自部署，记录如下：

载入x86_64默认config：

```sh
make x86_64_defconfig
```

使用Linux提供的脚本一键启用DEBUG_INFO、GDB_SCRIPTS

```sh
./scripts/config -e DEBUG_INFO -e GDB_SCRIPTS
```

> 此脚本未生效，原因未知……

开始编译：

```sh
make -j`nproc`
```

得到：Kernel: arch/x86/boot/bzImage is ready

先来看看单kernel直接传递给qemu启动会怎样：

```sh
qemu-system-x86_64 -kernel arch/x86_64/boot/bzImage -nographic -append "console=ttyS0"
```

得到：

```sh
---[ end Kernel panic - not syncing: VFS: Unable to mount root fs on unknown-block(0,0) ]---
```

没有rootfs的情况下kernel初始化完成后会直接panic。

那么接下来制作initramfs即可：

```sh
mkinitramfs -o ramdisk.img
```

提供足够内存后启动：

```sh
qemu-system-x86_64 -m 512 -kernel arch/x86_64/boot/bzImage -nographic -append "console=ttyS0" -initrd ramdisk.img 
```

得到shell即成功。

> 巧合的是，就在我执行完如上命令进入shell后，惊觉Linux居然已经6.0.0了😱。经查正是今天的新闻。

## 试图调试

沿用上述手动挡的构建方案，qemu启动时关闭kaslr：

```sh
qemu-system-x86_64 -m 512 -cpu host \
				   -kernel arch/x86_64/boot/bzImage \
				   -append "console=ttyS0 nokaslr" \
				   -initrd ramdisk.img \
				   --enable-kvm -nographic \
				   -s -S
```

此时于另一个tty、pts：

```sh
echo "add-auto-load-safe-path /home/zkv/Laboratory/linux/scripts/gdb/vmlinux-gdb.py" >> ~/.gdbinit
gdb vmlinux -ex "target remote :1234"
```



# 内核启动

# 内存管理

# 文件系统

# 网络栈
