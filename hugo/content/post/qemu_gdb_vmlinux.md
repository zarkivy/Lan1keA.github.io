---
title: "使用QEMU+GDB调试Linux内核"
description: "学好Kernel的基操？"
date: 2022-08-08T14:17:44+08:00
tags: [ "QEMU", "Linux" ]
imagelink: "https://s2.loli.net/2022/08/09/PKcwjiVBTg6ApEv.png"
---



# 环境准备

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

使用Linux提供的脚本一键启用DEBUG_KERNEL、DEBUG_INFO、GDB_SCRIPTS，关闭RANDOMIZE_BASE

```sh
./scripts/config --file .config -e DEBUG_KERNEL -e DEBUG_INFO -e GDB_SCRIPTS -d RANDOMIZE_BASE
```

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

沿用上述手动挡的构建方案，qemu启动时关闭kaslr（编译时若关闭了RANDOMIZE_BASE，则无需向kernel传递nokaslr参数）：

```sh
qemu-system-x86_64 -m 512 \
				   -kernel arch/x86_64/boot/bzImage \
				   -append "console=ttyS0 nokaslr" \
				   -initrd ramdisk.img \
				   -nographic \
				   -s -S
```

此时于另一个tty、pts：

```sh
echo "add-auto-load-safe-path /home/zkv/Laboratory/linux/scripts/gdb/vmlinux-gdb.py" >> ~/.gdbinit
echo "set auto-load safe-path /" >> ~/.gdbinit 
gdb vmlinux -ex "target remote :1234"
```

关于对gdb调试的支持，内核所提供的文档如下：

[https://www.kernel.org/doc/html/latest/dev-tools/gdb-kernel-debugging.html](https://www.kernel.org/doc/html/latest/dev-tools/gdb-kernel-debugging.html)

# 内核启动

内核初始化主函数：`init/main.c:start_kernel`

```sh
pwndbg> hb start_kernel
pwndbg> c
```

第一次向console打印内容：

```sh
pwndbg> hb console_init
pwndbg> c
pwndbg> until
```

可得：

```sh
[    0.000000] Console: colour VGA+ 80x25
[    0.000000] printk: console [ttyS0] enabled
```

内核主线程的终点：

```c
In file: /home/zkv/Laboratory/linux/kernel/sched/idle.c
   395 void cpu_startup_entry(enum cpuhp_state state)
   396 {
   397  arch_cpu_idle_prepare();
   398  cpuhp_online_idle(state);
   399  while (1)
 ► 400          do_idle();
   401 }
```

进入do_idle让出CPU，没有任务时将CPU交给用户态程序使用，否则将CPU重新调度给内核线程自己。`while(1)`无限循环该操作。

`kernel/sched/idle.c`的内容：

```c
/*
 * Generic idle loop implementation
 *
 * Called with polling cleared.
 */
static void do_idle(void)
{
        int cpu = smp_processor_id();

        /*
         * Check if we need to update blocked load
         */
        nohz_run_idle_balance(cpu);

        /*
         * If the arch has a polling bit, we maintain an invariant:
         *
         * Our polling bit is clear if we're not scheduled (i.e. if rq->curr !=
         * rq->idle). This means that, if rq->idle has the polling bit set,
         * then setting need_resched is guaranteed to cause the CPU to
         * reschedule.
         */

        __current_set_polling();
        tick_nohz_idle_enter();

        while (!need_resched()) {
                rmb();

                local_irq_disable();

                if (cpu_is_offline(cpu)) {
                        tick_nohz_idle_stop_tick();
                        cpuhp_report_idle_dead();
                        arch_cpu_idle_dead();
                }

                arch_cpu_idle_enter();
                rcu_nocb_flush_deferred_wakeup();

                /*
                 * In poll mode we reenable interrupts and spin. Also if we
                 * detected in the wakeup from idle path that the tick
                 * broadcast device expired for us, we don't want to go deep
                 * idle as we know that the IPI is going to arrive right away.
                 */
                if (cpu_idle_force_poll || tick_check_broadcast_expired()) {
                        tick_nohz_idle_restart_tick();
                        cpu_idle_poll();
                } else {
                        cpuidle_idle_call();
                }
                arch_cpu_idle_exit();
        }

        /*
         * Since we fell out of the loop above, we know TIF_NEED_RESCHED must
         * be set, propagate it into PREEMPT_NEED_RESCHED.
         *
         * This is required because for polling idle loops we will not have had
         * an IPI to fold the state for us.
         */
        preempt_set_need_resched();
        tick_nohz_idle_exit();
        __current_clr_polling();

        /*
         * We promise to call sched_ttwu_pending() and reschedule if
         * need_resched() is set while polling is set. That means that clearing
         * polling needs to be visible before doing these things.
         */
        smp_mb__after_atomic();

        /*
         * RCU relies on this call to be done outside of an RCU read-side
         * critical section.
         */
        flush_smp_call_function_queue();
        schedule_idle(); // 这里会将CPU交由用户态程序使用

        if (unlikely(klp_patch_pending(current)))
                klp_update_patch_state(current);
}
```



# 系统调用

## 静态搜寻

动态调试系统调用代码之前，不妨先来找到内核系统调用的静态代码位置。Linux Kernel使用`SYSCALL_DEFINE[N]`宏来定义系统调用入口，其原型位于[include/linux/syscalls.h](https://github.com/torvalds/linux/blob/16f73eb02d7e1765ccab3d2018e0bd98eb93d973/include/linux/syscalls.h)，其中会调用特定系统调用的实现函数。所以，我们使用如下命令可以帮助快速筛选想要动态调试的系统调用入口所在位置：

```sh
grep -rn "SYSCALL_DEFINE"
```

举例来讲，接下来我想要对与open这一系统调用的具体实现一探究竟，就应当：

```sh
▶ grep -rn "SYSCALL_DEFINE" | grep open
......
fs/open.c:1331:SYSCALL_DEFINE3(open, const char __user *, filename, int, flags, umode_t, mode)
......
```

可知，open系统调用的入口位于fs/open.c的第1331行：

```sh
SYSCALL_DEFINE3(open, const char __user *, filename, int, flags, umode_t, mode)
{
        if (force_o_largefile())
                flags |= O_LARGEFILE;
        return do_sys_open(AT_FDCWD, filename, flags, mode);
}
```

由此进一步可知，open系统调用的实现函数为`do_sys_open`。

> Linux Kernel 系统调用流程参见：[https://0xax.gitbooks.io/linux-insides/content/SysCall/linux-syscall-2.html](https://0xax.gitbooks.io/linux-insides/content/SysCall/linux-syscall-2.html)

## 动态调试

继续以open系统调用为例，开始动态调试跟踪。

> qemu monitor，你的好帮手，Ctrl+A C进入与退出monitor模式：
>
> - [https://www.qemu.org/docs/master/system/monitor.html](https://www.qemu.org/docs/master/system/monitor.html)
> - [https://en.wikibooks.org/wiki/QEMU/Monitor](https://en.wikibooks.org/wiki/QEMU/Monitor)

这里使用qemu monitor模式开启调试。使用qemu启动内核：

```sh
qemu-system-x86_64 -m 512 \
				   -kernel arch/x86_64/boot/bzImage \
				   -append "console=ttyS0 nokaslr" \
				   -initrd ramdisk.img \
				   -nographic
```

启动完成得到shell后，Ctrl+A C进入与退出monitor模式：

```sh
(initramfs) ls
dev      bin      init     lib64    sbin     var      tmp
root     conf     lib      libx32   scripts  sys
kernel   etc      lib32    run      usr      proc
# Ctrl+A C
(qemu) gdbserver
Waiting for gdb connection on device 'tcp::1234'
# Ctrl+A C
(initramfs) 
```

使用gdb连接:1234即可开始调试：

```sh
gdb vmlinux -ex "target remote :1234"
```

此前已经知道open系统调用的实现函数为`do_sys_open`，故于此函数下断点即可。



# 内存管理



# 文件系统



# 网络栈
