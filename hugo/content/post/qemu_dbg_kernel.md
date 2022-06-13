---
title: "Cross debug kernel using qemu-system"
description: "compile, emulate and debug"
date: 2022-05-08T19:35:11+08:00
tags: [ "QEMU", "Linux" ]
imagelink: "https://s2.loli.net/2022/05/08/MGJ3tsAVHSYrThj.jpg"
---



> From this post, our goal is tracing the execution procedure from kernel entry to userspace process (busybox init) by using qemu-system and gdb-multiarch.

# Preparation
## qemu-system-arm

Compile it from source code or just use `sudo apt install qemu-system-arm`.

## kernel

Linux kernel v4.1 for armel as an example.
Compiling a linux kernel requires a matched version of gcc.
For example, the linux kernel v4.1 requires the gcc version between 3 and 5:

```sh
linunx-v4.1 $ find . -name "compiler-gcc*.h"
./include/linux/compiler-gcc.h
./include/linux/compiler-gcc3.h
./include/linux/compiler-gcc4.h
./include/linux/compiler-gcc5.h
```

So how can we get the old version gcc?
You can always rely on buildroot: [https://buildroot.org/download.html](https://buildroot.org/download.html)

Just clone it's repository:

```sh
git clone https://git.buildroot.net/buildroot
```

And switch to the specific old release you required.
You can check gcc release history from here

GCC5 is released in 2015, so we switch buildroot's release version to 2015.08.x :

```sh
git switch 2015.08.x
```

And then, 

```sh
make menuconfig
# edit with yourself
make
```

But an error occured in this step (this because we are using an old version of buildroot) :

> error "Please port gnulib freadahead.c to your platform!


cd to /buildroot/output/build/host-m4-1.4.17 and excute :

```sh
sed -i 's/IO_ftrylockfile/IO_EOF_SEEN/' lib/*.c
echo "#define _IO_IN_BACKUP 0x100" >> lib/stdio-impl.h
```

Then make again, we will get the old version of GCC we want.
Ready for compiling the old version linux kernel :)

```sh
git clone https://github.com/torvalds/linux.git
git checkout v4.1
make ARCH=arm O=./build/armel menuconfig
```

Make sure that CONFIG_DEBUG_INFO is enabled. (You can configure it in the .config file directly, or in kernel-hack item from menuconfig interface.)

```sh
make ARCH=arm O=./build/armel CROSS_COMPILE=/opt/crossc/armel-uclibc-gcc-4/usr/bin/arm-buildroot-linux-uclibcgnueabi- zImage -j$(nproc)
```

We got zImage at build/armel/arch/arm/boot/zImage and vmlinux (with debug symbols) at build/armel

## busybox

Even though we can use the busybox from the filesystem directly, we got no debug symbols from it.
Thus we should compile busybox manually:

```sh
git clone https://git.busybox.net/busybox/
make menuconfig
make CROSS_COMPILE=/opt/crossc/armel-uclibc-gcc-4/usr/bin/arm-buildroot-linux-uclibcgnueabi- -j$(nproc)
```

Then the busybox_unstripped is produced.
## filesystem

Hummm maybe obtain it by yourself?

As for my situation, I just extract the filesystem from a TP-Link firmware. 
And we need do some modification to turn our filesystem directory to a filesystem image.
Assume we have the rootfs directory made by bin etc sys usr var tmp ...
First of all, use dd to create a 50MB file filled with \x00

```sh
dd if=/dev/zero of=rootfs.img bs=4k count=12800
```

We will regard this rootfs.img as our virtual disk containing root filesystem. So, continue modifing it.
The next step is create disk partitions using fdisk on rootfs.img :

```sh
echo -e "o\nn\np\n1\n\n\nw" | sudo /sbin/fdisk rootfs.img
```

With disk partition be ready, rootfs.img could be mounted to the host filesystem using kpartx :

```sh
sudo kpartx -a -s -v rootfs.img
```

After that we got the corresponding device located at /dev/mapper/loop0p1

Format its major partition as ext2 format :

```sh
mkfs.ext2 /dev/mapper/loop0p1
sync
```

And then mount it to host filesystem path wherever you like :

```sh
sudo mount /dev/mapper/loop0p1 ./mount_dir
```

We are done preparing here. All we should do next is copying all the files recursively from the origin root filesystem directory to our mounted directory,
so that we got a rootfs.img image file containing the armel filesystem we want.
Don't forget replace the origin bin/busybox with our manually compiled version.

## Tracing time !
Now we have :

```sh
workdir $ ls
mount_dir rootfs.img vmlinux zImage
```

Full system emulation command line :

```sh
qemu-system-arm -M virt -kernel ./zImage -drive if=none,file=rootfs.img,format=raw,id=rootfs -device virtio-blk-device,drive=rootfs -append "root=/dev/vda1 console=ttyS0 rw" -nographic -s -S
```

> tips :
> -s              shorthand for -gdb tcp::1234
> -S              freeze CPU at startup (use 'c' to start execution)
> Terminate qemu-system-arm process with Ctrl-A + x

qemu-system-arm process should be paused until we attach to it using gdb-multiarch:

```sh
~$ gdb-multiarch
(gdb)$ set architecture arm
The target architecture is set to "arm".
(gdb)$ file vmlinux
Reading symbols from vmlinux...
(gdb)$ target remote :1234
```

We can not reach the bootloader's entry address 0x7c00 because we are not using our own bootloader and pass the -bios parameter to qemu.

If you want to learn how kernel initialize itself, just use :

```sh
(gdb)$ b kernel_init
(gdb)$ c
```

After a bunch of initializing work on memory, paging, hardware and drive, etc being done, the init binary (who has PID 1) is about to be execved.
The problem is, init is a usermode process and running in ring3, which has the different thread with kernel and virtual memory space, how we trace to it?
The answer is straightforward: the program counter register (PC) is always holding the instruction's virtual address being excuted by CPU.
So we can just check the entry address of init, and set a breakpoint at this address.
Because I am using busybox to supply init, and this binary is belong to armel architecture, so I should typing :

```sh
arm-buildroot-linux-uclibcgnueabi-readelf -h mount_dir/bin/busybox
```

It gave me :

```ini
ELF Header:
Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00 
Class:                             ELF32
Data:                              2's complement, little endian
Version:                           1 (current)
OS/ABI:                            UNIX - System V
ABI Version:                       0
Type:                              EXEC (Executable file)
Machine:                           ARM
Version:                           0x1
Entry point address:               0x87a0
Start of program headers:          52 (bytes into file)
Start of section headers:          4008884 (bytes into file)
Flags:                             0x5000202, has entry point, Version5 EABI, soft-float ABI
Size of this header:               52 (bytes)
Size of program headers:           32 (bytes)
Number of program headers:         5
Size of section headers:           40 (bytes)
Number of section headers:         30
Section header string table index: 27
```

We find that the entry point address is 0x87a0.
0x87a0 is such a strange address that seems can not be a breakpoint address.
The reason is that busybox is not compiled with CFLAG -no-pie. so add this CFLAG and compile busybox again, we will get the avaliable breakpoint address by readelf.
So set a breakpoint at busybox's entry address and send c to gdb, we will reach to userspace directly.
