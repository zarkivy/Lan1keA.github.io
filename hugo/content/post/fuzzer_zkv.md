---
title: "ZKV的Fuzzing早教"
description: "from 0 to 1"
date: 2022-05-11T13:57:01+08:00
tags: [ "fuzzing" ]
imagelink: "https://lcamtuf.coredump.cx/afl/afl_screen.png"
---



##  \x00 早教教程

### 教程

[谷歌的Fuzzing教程](https://github.com/google/fuzzing)

[Fuzzingbook](https://www.fuzzingbook.org/)

### 课程

[南京大学：软件分析](https://pascal-group.bitbucket.io/teaching.html)

[南京大学：软件分析课程视频](https://www.bilibili.com/video/BV1b7411K7P4/?spm_id_from=333.788.videocard.1)

### 博文

[Fuzzing战争: 从刀剑弓斧到星球大战](https://blog.flanker017.me/fuzzing%e6%88%98%e4%ba%89-%e4%bb%8e%e5%88%80%e5%89%91%e5%bc%93%e6%96%a7%e5%88%b0%e6%98%9f%e7%90%83%e5%a4%a7%e6%88%98/)

[Fuzzing战争系列之二：不畏浮云遮望眼](https://blog.flanker017.me/fuzzing%e6%88%98%e4%ba%89%e7%b3%bb%e5%88%97%e4%b9%8b%e4%ba%8c%ef%bc%9a%e4%b8%8d%e7%95%8f%e6%b5%ae%e4%ba%91%e9%81%ae%e6%9c%9b%e7%9c%bc/)

### 练兵场

[Fuzzing101](https://github.com/antonio-morales/Fuzzing101)

### 兵器

[AFL](https://github.com/google/AFL)

[AFLplusplus](https://github.com/AFLplusplus/AFLplusplus)



## \x01 学步车

### 最简单的 fuzzing demo

使用AFL，故先安装：

```sh
git clone https://github.com/google/AFL.git
cd AFL
make -j`nproc`
sudo make install
```

接着准备被测程序源码：

```c
#include <stdio.h>
#include <string.h> 
#include <signal.h> 
int vuln(char *str) {
    int len = strlen(str);
    if(str[0] == 'A' && len == 66)
        raise(SIGSEGV);
    else if(str[0] == 'F' && len == 6)
        raise(SIGSEGV);
    else
        puts("\nnothing happened");
    return 0;
}
int main(int argc, char *argv[]) {
    char buf[100]={0};
    gets(buf);
    printf(buf);
    vuln(buf);
    return 0;
}
```

使用afl-gcc插装编译：

```sh
afl-gcc vulner.c -o vulner
```

准备fuzzing输入与输出的文件夹：

```sh
mkdir fuzz_in fuzz_out
```

创建两个样例输入：

```sh
echo -n AAAA > fuzz_in/input_case_1
echo -e -n "\x01\x02\x03\x04" > fuzz_in/input_case_2
```

启动fuzzing：

```sh
afl-fuzz -i fuzz_in -o fuzz_out ./vulner
```

Ctrl+C停止后，由fuzzing_out中拿到fuzzing结果。用其中的一个crash输入测试下：

```sh
➜ ./vulner < fuzz_out/crashes/id:000000,sig:06,src:000000,op:havoc,rep:32
6666
nothing happened
*** stack smashing detected ***: terminated
[1]    1541204 abort      ./vulner < fuzz_out/crashes/id:000000,sig:06,src:000000,op:havoc,rep:32
```

可以看到发生了栈溢出。

### 为学步车装上名为sanitizers的轮子

借助于[sanitizers](https://github.com/google/sanitizers)获得更多崩溃的详细信息：

开启[AddressSanitizer](https://github.com/google/fuzzing/blob/master/docs/ASan)重新编译程序：

```sh
 afl-clang -fsanitize=address vulner.c -o vulner
```

使用上一部分相同的crash输入用例，来测试打开了AddressSanitizer的程序：

```sh
➜ ./vulner < fuzz_out/crashes/id:000000,sig:06,src:000000,op:havoc,rep:32
=================================================================
==1541245==ERROR: AddressSanitizer: stack-buffer-overflow on address 0x7ffe9090e564 at pc 0x0000004393cb bp 0x7ffe9090e3a0 sp 0x7ffe9090db28
READ of size 116 at 0x7ffe9090e564 thread T0
    #0 0x4393ca in printf_common(void*, char const*, __va_list_tag*) (/f/lab/fuzzing_baby/vulner+0x4393ca)
    #1 0x43af1e in printf (/f/lab/fuzzing_baby/vulner+0x43af1e)
    #2 0x4c3418 in main /root/lab/fuzzing_baby/vulner.c:17:5
    #3 0x7f09a7252082 in __libc_start_main /build/glibc-SzIz7B/glibc-2.31/csu/../csu/libc-start.c:308:16
    #4 0x41b33d in _start (/f/lab/fuzzing_baby/vulner+0x41b33d)

Address 0x7ffe9090e564 is located in stack of thread T0 at offset 132 in frame
    #0 0x4c32cf in main /root/lab/fuzzing_baby/vulner.c:14

  This frame has 1 object(s):
    [32, 132) 'buf' (line 15) <== Memory access at offset 132 overflows this variable
HINT: this may be a false positive if your program uses some custom stack unwind mechanism, swapcontext or vfork
      (longjmp and C++ exceptions *are* supported)
SUMMARY: AddressSanitizer: stack-buffer-overflow (/f/lab/fuzzing_baby/vulner+0x4393ca) in printf_common(void*, char const*, __va_list_tag*)
Shadow bytes around the buggy address:
  0x100052119c50: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x100052119c60: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x100052119c70: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x100052119c80: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x100052119c90: 00 00 00 00 00 00 00 00 00 00 00 00 f1 f1 f1 f1
=>0x100052119ca0: 00 00 00 00 00 00 00 00 00 00 00 00[04]f3 f3 f3
  0x100052119cb0: f3 f3 f3 f3 00 00 00 00 00 00 00 00 00 00 00 00
  0x100052119cc0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x100052119cd0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x100052119ce0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
  0x100052119cf0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
Shadow byte legend (one shadow byte represents 8 application bytes):
  Addressable:           00
  Partially addressable: 01 02 03 04 05 06 07
  Heap left redzone:       fa
  Freed heap region:       fd
  Stack left redzone:      f1
  Stack mid redzone:       f2
  Stack right redzone:     f3
  Stack after return:      f5
  Stack use after scope:   f8
  Global redzone:          f9
  Global init order:       f6
  Poisoned by user:        f7
  Container overflow:      fc
  Array cookie:            ac
  Intra object redzone:    bb
  ASan internal:           fe
  Left alloca redzone:     ca
  Right alloca redzone:    cb
  Shadow gap:              cc
==1541245==ABORTING
```

由此得到了crash的详细信息输出。

### 黑盒测试QEMU mode——拆走源码の轮儿

AFL的QEMU mode不会默认安装，而是需要我们手动安装：

```sh
sudo apt install libtool libtool-bin libcapstone-dev
cd AFL/qemu_mode
./build_qemu_support.sh
cd ..
sudo make install 
```

如果遇到编译错误，可以使用这份fork：https://github.com/blurbdust/AFL.git

不插桩重新编译程序：

```sh
gcc vulner.c -o vulner
```

开始QEMU mode黑盒fuzzing

```sh
afl-fuzz -Q -i fuzz_in -o fuzz_out ./vulner
```

### AFL并发——学步车的四缸引擎

通过-M指定master fuzzer进程，-S指定一系列slave fuzzer进程即可。四核机器就：

```sh
afl-fuzz -i fuzz_in -o fuzz_out -M fuzzer1 ./vulner
afl-fuzz -i fuzz_in -o fuzz_out -S fuzzer2 ./vulner
afl-fuzz -i fuzz_in -o fuzz_out -S fuzzer3 ./vulner
afl-fuzz -i fuzz_in -o fuzz_out -S fuzzer4 ./vulner
```

