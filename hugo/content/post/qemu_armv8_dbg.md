---
title: "记一次qemu-system-arm的排错"
description: "居然是指令集的锅？"
date: 2022-05-26T15:51:22+08:00
tags: [ "虚拟化", "Linux" ]
imagelink: "https://s2.loli.net/2022/05/26/RS1JrGW2t4vzD3M.jpg"
---



## 探案

在一次尝试使用`qemu-system-arm`对于asuswrt的固件进行仿真运行时，出现了十分诡异的问题：整个qemu-system、linux内核、固件文件系统运行完全正常，除了在使用到`openssl`时：

```sh
admin@(none):/tmp/home/root# openssl
[    9.765639] openssl (274): undefined instruction: pc=b6cf31a8
[    9.765770] CPU: 0 PID: 274 Comm: openssl Tainted: G        W       4.1.17+ #10
[    9.766031] Hardware name: Generic DT based system
[    9.766149] task: cef58b40 ti: cef80000 task.ti: cef80000
[    9.766367] PC is at 0xb6cf31a8
[    9.766423] LR is at 0xb6dcedb0
[    9.766475] pc : [<b6cf31a8>]    lr : [<b6dcedb0>]    psr: 200b0010
[    9.766475] sp : bee2c7c8  ip : 00000000  fp : 00000000
[    9.766741] r10: 00000001  r9 : bee2cdc4  r8 : 00000000
[    9.766903] r7 : 000a5848  r6 : b6f6ace0  r5 : b6eb7adc  r4 : 00a43020
[    9.766989] r3 : 00a43050  r2 : 00000001  r1 : 00000000  r0 : 00a43020
[    9.767253] Flags: nzCv  IRQs on  FIQs on  Mode USER_32  ISA ARM  Segment user
[    9.767372] Control: 10c5387d  Table: 4ef78059  DAC: 00000015
[    9.767547] Code: e5845000 e5843014 e3a02001 e2843030 (e183fc92) 
[    9.767878] potentially unexpected fatal signal 4.
[    9.767978] CPU: 0 PID: 274 Comm: openssl Tainted: G        W       4.1.17+ #10
[    9.768206] Hardware name: Generic DT based system
[    9.768282] task: cef58b40 ti: cef80000 task.ti: cef80000
[    9.768395] PC is at 0xb6cf31a8
[    9.768504] LR is at 0xb6dcedb0
[    9.768639] pc : [<b6cf31a8>]    lr : [<b6dcedb0>]    psr: 200b0010
[    9.768639] sp : bee2c7c8  ip : 00000000  fp : 00000000
[    9.768908] r10: 00000001  r9 : bee2cdc4  r8 : 00000000
[    9.768980] r7 : 000a5848  r6 : b6f6ace0  r5 : b6eb7adc  r4 : 00a43020
[    9.769094] r3 : 00a43050  r2 : 00000001  r1 : 00000000  r0 : 00a43020
[    9.769279] Flags: nzCv  IRQs on  FIQs on  Mode USER_32  ISA ARM  Segment user
[    9.769437] Control: 10c5387d  Table: 4ef78059  DAC: 00000015
[    9.769517] CPU: 0 PID: 274 Comm: openssl Tainted: G        W       4.1.17+ #10
[    9.769739] Hardware name: Generic DT based system
[    9.769898] [<c001c8dc>] (unwind_backtrace) from [<c0019c70>] (show_stack+0x10/0x14)
[    9.770003] [<c0019c70>] (show_stack) from [<c002e6cc>] (get_signal+0x41c/0x47c)
[    9.770168] [<c002e6cc>] (get_signal) from [<c00194a8>] (do_signal+0x8c/0x35c)
[    9.770312] [<c00194a8>] (do_signal) from [<c00198d8>] (do_work_pending+0x54/0xac)
[    9.770408] [<c00198d8>] (do_work_pending) from [<c0016c8c>] (work_pending+0xc/0x20)
Illegal instruction (core dumped)
```

当然，用到了`openssl`的各个组件，如`httpd`，也是由此无法正常工作的。

动态调试看看。在`qemu-system-arm`环境中启动`gdbserver`：（gdbserver可以来这里获取：[https://github.com/dev2ero/embins](https://github.com/dev2ero/embins)）

```sh
gdbserver :1234 /usr/sbin/openssl
```

在宿主机使用gdb-multiarch作为gdb client调试：

```sh
~$ gdb-multiarch
pwndbg> set architecture armv5
The target architecture is set to "armv5".
pwndbg> target remote 192.168.50.1:1234
Remote debugging using 192.168.50.1:1234
pwndbg> file asuswrt/usr/sbin/openssl 
Reading symbols from asuswrt/usr/sbin/openssl...
pwndbg> c
```

触发`SIGILL`：

```sh
Program received signal SIGILL, Illegal instruction.
0xb6d871a8 in BIO_new () from target:/usr/lib/libcrypto.so.1.1
LEGEND: STACK | HEAP | CODE | DATA | RWX | RODATA
───────────────────────────────────────────────────[ REGISTERS ]────────────────────────────────────────────────────
*R0   0xaf020 —▸ 0xb6f4badc ◂— 0x402
 R1   0x0
*R2   0x1
*R3   0xaf050 ◂— 0x0
*R4   0xaf020 —▸ 0xb6f4badc ◂— 0x402
*R5   0xb6f4badc ◂— 0x402
*R6   0xb6ffece0 (__stack_chk_guard) ◂— 0x10072e00
*R7   0xa5848 —▸ 0xa5738 ◂— 0x1
 R8   0x0
*R9   0xbefffdb4 —▸ 0xbefffea8 ◂— '/usr/sbin/openssl'
*R10  0x1
 R11  0x0
 R12  0x0
*SP   0xbefff7b8 —▸ 0xb6ff9648 —▸ 0x1e6a9 ◂— cdpmi  p0, #4, c5, c5, c15, #2 /* 'OPENSSL_1_1_0' */
*PC   0xb6d871a8 (BIO_new+84) ◂— 0xe183fc92
─────────────────────────────────────────────────────[ DISASM ]─────────────────────────────────────────────────────
Invalid instructions at 0xb6d871a8
```

看来是`libcrypto.so.1.1`中的`BIO_new()`函数包含了非法指令。反汇编看看是哪条指令出了问题：

```sh
pwndbg> disassemble BIO_new
Dump of assembler code for function BIO_new:
   ......
   0xb6d871a4 <+80>:    add     r3, r4, #48     ; 0x30
=> 0xb6d871a8 <+84>:    stl     r2, [r3]
   0xb6d871ac <+88>:    add     r7, r4, #72     ; 0x48
   ......
```

`stl`指令是非法指令？为什么呢？Google一下得到：

[https://developer.arm.com/documentation/100076/0200/a32-t32-instruction-set-reference/a32-and-t32-instructions/stl?lang=en](https://developer.arm.com/documentation/100076/0200/a32-t32-instruction-set-reference/a32-and-t32-instructions/stl?lang=en)

其中注明了：

> This 32-bit instruction is available in A32 and T32.
>
> There is no 16-bit version of this instruction.

`stl`指令只是A32和T32架构下可用，那么这两个架构对应的指令集是什么呢？继续Google：

[https://developer.arm.com/Processors/Cortex-A32](https://developer.arm.com/Processors/Cortex-A32)

得到：

> Architecture: 32-bit Armv8-A

这是一条`Armv8`的指令。那我们此时的`qemu-system-arm`用的是什么环境呢？

```sh
qemu-system-arm -M help
......
virt-6.1             QEMU 6.1 ARM Virtual Machine
virt-6.2             QEMU 6.2 ARM Virtual Machine
virt                 QEMU 7.0 ARM Virtual Machine (alias of virt-7.0)
virt-7.0             QEMU 7.0 ARM Virtual Machine
......
```

machine的说明中只说明了我所用的`virt`是指`QEMU 7.0 ARM Virtual Machine`，并未介绍更多信息

那么去虚拟环境中直接看看：

```sh
admin@(none):/# cat /proc/cpuinfo 
processor       : 0
model name      : ARMv7 Processor rev 1 (v7l)
BogoMIPS        : 125.00
Features        : half thumb fastmult vfp edsp neon vfpv3 tls vfpv4 idiva idivt vfpd32 lpae evtstrm 
CPU implementer : 0x41
CPU architecture: 7
CPU variant     : 0x2
CPU part        : 0xc0f
CPU revision    : 1

Hardware        : Generic DT based system
Revision        : 0000
Serial          : 0000000000000000
```

问题原因找到了，此时我们虚拟环境的CPU所用指令集为`Armv7`，并未包含`Armv8`下的`stl`指令。

做个实验验证下猜想：

准备一份arm下的hello world源码：

```asm
.section .data
hello:
    .ascii "hello world\n"
    .equ len, . - hello

.section .text
.global _start
_start:
    mov r0, #1
    ldr r1, =hello
    mov r2, #len
    mov r7, #4
    swi #0

exit:
    mov r0, #0
    mov r7, #1
    swi #0
```

使用旧版`arm-linux-gnueabi-gcc`，可以正常汇编链接运行：

```sh
~$ /opt/cross/gcc-linaro-arm-linux-gnueabihf/bin/arm-linux-gnueabihf-as hello.s -o hello.o
~$ /opt/cross/gcc-linaro-arm-linux-gnueabihf/bin/arm-linux-gnueabihf-ld hello.o -o hello.out
~$ qemu-arm hello.out 
hello world
```

而为其添加上一行stl指令后则汇编失败：

```sh
~$ /opt/cross/gcc-linaro-arm-linux-gnueabihf/bin/arm-linux-gnueabihf-as hello.s -o hello.o
hello.s: Assembler messages:
hello.s:11: Error: bad instruction `stl r0,[r1]'
```

由此验证了上面的原因推断。

继续来看看固件中的ELF指令集版本：

首先我们刚刚的`hello.out`是`Armv4`：

```sh
~$ /opt/cross/gcc-linaro-arm-linux-gnueabihf/bin/arm-linux-gnueabihf-readelf -A hello.out                    
Attribute Section: aeabi
File Attributes
  Tag_CPU_arch: v4
  Tag_ARM_ISA_use: Yes
  Tag_DIV_use: Not allowed
```

固件中的`busybox`和出问题的`libcrypto.so.1.1`则是：

```sh
~$ /opt/cross/gcc-linaro-arm-linux-gnueabihf/bin/arm-linux-gnueabihf-readelf -A asuswrt/busybox                    
Attribute Section: aeabi
File Attributes
  Tag_CPU_name: "Cortex-A9"
  Tag_CPU_arch: v7
  Tag_CPU_arch_profile: Application
  Tag_ARM_ISA_use: Yes
  Tag_THUMB_ISA_use: Thumb-2
  Tag_ABI_PCS_wchar_t: 4
  Tag_ABI_FP_rounding: Needed
  Tag_ABI_FP_denormal: Needed
  Tag_ABI_FP_exceptions: Needed
  Tag_ABI_FP_number_model: IEEE 754
  Tag_ABI_align_needed: 8-byte
  Tag_ABI_align_preserved: 8-byte, except leaf SP
  Tag_ABI_enum_size: int
  Tag_CPU_unaligned_access: v6
  Tag_MPextension_use: Allowed
  Tag_Virtualization_use: TrustZone

~$ /opt/cross/gcc-linaro-arm-linux-gnueabihf/bin/arm-linux-gnueabihf-readelf -A asuswrt/usr/lib/libcrypto.so.1.1
Attribute Section: aeabi
File Attributes
  Tag_CPU_name: "8-A"
  Tag_CPU_arch: ??? (14)
  Tag_CPU_arch_profile: Application
  Tag_ARM_ISA_use: Yes
  Tag_THUMB_ISA_use: Thumb-2
  Tag_FP_arch: VFPv3
  Tag_Advanced_SIMD_arch: NEONv1
  Tag_ABI_PCS_wchar_t: 4
  Tag_ABI_FP_rounding: Needed
  Tag_ABI_FP_denormal: Needed
  Tag_ABI_FP_exceptions: Needed
  Tag_ABI_FP_number_model: IEEE 754
  Tag_ABI_align_needed: 8-byte
  Tag_ABI_enum_size: int
  Tag_CPU_unaligned_access: v6
  Tag_MPextension_use: Allowed
  Tag_Virtualization_use: TrustZone and Virtualization Extensions
```

一个固件中的不同组件还用了不同的编译工具链？！

`busybox`是`Armv7`，而`libcrypto.so.1.1`则是`Armv8-A`。

这也解释了为什么我在仿真旧版本固件时是没有该故障存在的。旧版本固件中的是`Armv7`的`libcrypto.so.1.0.0`。

我们来到固件的下载页面：[https://www.asus.com.cn/Networking-IoT-Servers/WiFi-Routers/ASUS-WiFi-Routers/RT-AC86U/HelpDesk_BIOS/](https://www.asus.com.cn/Networking-IoT-Servers/WiFi-Routers/ASUS-WiFi-Routers/RT-AC86U/HelpDesk_BIOS/)

可以看到新版本固件中华硕对于OpenSSL的漏洞进行了修复：

> 版本 3.0.0.4.386.48260
>
> 2022/03/25 62.81 MBytes
>
> ASUS RT-AC86U 固件版本 3.0.0.4.386.48260
>
> 1. 修正 OpenSSL CVE-2022-0778
>
> ​    ......

原来他们修复时并没有将固件全量编译，而是只换用了新编译工具链将出问题的`openssl`重新编译了。

恰好我手头有华硕的路由器真机，来看看真机环境是什么：

```sh
zkv@RT-AC86U:/tmp/home/root# uname -a
Linux RT-AC86U 4.1.27 #2 SMP PREEMPT Thu Nov 11 17:12:59 CST 2021 aarch64
```

好家伙，直接是`aarch64`的linux内核。至于CPU，型号是`Boardcom BCM4906`：[https://www.broadcom.com/products/wireless/wireless-lan-infrastructure/bcm49408](https://www.broadcom.com/products/wireless/wireless-lan-infrastructure/bcm49408)

原来是`Armv8-A`的`A53`：[https://developer.arm.com/Processors/Cortex-A53](https://developer.arm.com/Processors/Cortex-A53)

其中注明了：

> ISA Support
>
> - AArch32 for full backward compatibility with Armv7
> - AArch64 for 64-bit support and new architectural features
> - ......

由此破案了：华硕为这台路由器用了较新的A53 64位CPU和64位内核，内部软件却都是较旧的`Armv7`指令集下的32位软件。整台机器相当于是跑在一个“向下兼容模式”。

——直到他们修复OpenSSL CVE-2022-0778时，固件中才首次出现了`Armv8`的软件`libcrypto.so.1.1`。



## 收网
