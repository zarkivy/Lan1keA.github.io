---
title: "ZKV的Fuzzing早教"
description: "from 0 to 1"
date: 2022-05-11T13:57:01+08:00
tags: [ "Fuzzing" ]
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



## \x01 学步车AFL

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



## \x02 四轮单车libFuzzer

### 环境准备

> LibFuzzer is an in-process, coverage-guided, evolutionary fuzzing engine.

- [官方文档](https://llvm.org/docs/LibFuzzer.html)
- [Google的libFuzzer教程](https://github.com/google/fuzzing/blob/master/tutorial/libFuzzerTutorial.md)

☝️接下来读一练二

将教程仓库clone下来：

```sh
git clone https://github.com/google/fuzzing.git fuzzing
```

由于：

> Recent versions of Clang (starting from 6.0) include libFuzzer, and no extra installation is necessary.

所以可以直接测试一下环境是否就绪：

```sh
clang++ -g -fsanitize=address,fuzzer fuzzing/tutorial/libFuzzer/fuzz_me.cc
./a.out 2>&1 | grep ERROR
```

我得到了：

```sh
==2118349==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x60200003d473 at pc 0x000000550453 bp 0x7fff521da530 sp 0x7fff521da528
```

看来没问题，继续前进。

### 火花塞打火——demo拆解

样例程序如下：

```sh
#include <stdint.h>
#include <stddef.h>

bool FuzzMe(const uint8_t *Data, size_t DataSize) {
  return DataSize >= 3 &&
      Data[0] == 'F' &&
      Data[1] == 'U' &&
      Data[2] == 'Z' &&
      Data[3] == 'Z';  // :‑<
}

extern "C" int LLVMFuzzerTestOneInput(const uint8_t *Data, size_t Size) {
  FuzzMe(Data, Size);
  return 0;
}
```

使用libFuzzer时我们的Fuzz目标是？

Definition: a **fuzz target** is a function that has the following signature and does something interesting with it's arguments:

```c
extern "C" int LLVMFuzzerTestOneInput(const uint8_t *Data, size_t Size) {
  DoSomethingWithData(Data, Size);
  return 0;
}
```

所以对于样例程序，我们先准备好待测函数：

```c
bool FuzzMe(const uint8_t *Data, size_t DataSize) {
  return DataSize >= 3 &&
      Data[0] == 'F' &&
      Data[1] == 'U' &&
      Data[2] == 'Z' &&
      Data[3] == 'Z';  // :‑<
}
```

在为其准备fuzz wrapper函数：

```c
extern "C" int LLVMFuzzerTestOneInput(const uint8_t *Data, size_t Size) {
  FuzzMe(Data, Size);
  return 0;
}
```

使用版本6以上的clang，开启如下三个选项进行编译：

- `-fsanitize=fuzzer` (required): provides in-process coverage information to libFuzzer and links with the libFuzzer runtime.
- `-fsanitize=address` (recommended): enables [AddressSanitizer](http://clang.llvm.org/docs/AddressSanitizer.html)
- `-g` (recommended): enables debug info, makes the error messages easier to read.

```c
clang++ -g -fsanitize=address,fuzzer fuzzing/tutorial/libFuzzer/fuzz_me.cc
```

运行编译出的程序，即输出了：

```sh
➜  fuzzing git:(master) ✗ ./a.out
INFO: Seed: 122504861
INFO: Loaded 1 modules   (7 inline 8-bit counters): 7 [0x5a6eb0, 0x5a6eb7),
INFO: Loaded 1 PC tables (7 PCs): 7 [0x56b140,0x56b1b0),
INFO: -max_len is not provided; libFuzzer will not generate inputs larger than 4096 bytes
INFO: A corpus is not provided, starting from an empty corpus
#2      INITED cov: 3 ft: 3 corp: 1/1b exec/s: 0 rss: 27Mb
#3      NEW    cov: 4 ft: 4 corp: 2/5b lim: 4 exec/s: 0 rss: 27Mb L: 4/4 MS: 1 CrossOver-
#13     REDUCE cov: 4 ft: 4 corp: 2/4b lim: 4 exec/s: 0 rss: 27Mb L: 3/3 MS: 5 ChangeBit-ShuffleBytes-ChangeByte-CrossOver-CrossOver-
#446    REDUCE cov: 5 ft: 5 corp: 3/7b lim: 8 exec/s: 0 rss: 27Mb L: 3/3 MS: 3 ChangeByte-CopyPart-CMP- DE: "F\x00"-
#4543   NEW    cov: 6 ft: 6 corp: 4/11b lim: 48 exec/s: 0 rss: 27Mb L: 4/4 MS: 2 InsertByte-ChangeByte-
#4880   REDUCE cov: 6 ft: 6 corp: 4/10b lim: 48 exec/s: 0 rss: 27Mb L: 3/3 MS: 2 ChangeByte-EraseBytes-
#7716   REDUCE cov: 7 ft: 7 corp: 5/14b lim: 74 exec/s: 0 rss: 27Mb L: 4/4 MS: 1 InsertByte-
=================================================================
==2119678==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x6020000303f3 at pc 0x000000550453 bp 0x7fffcad0db90 sp 0x7fffcad0db88
READ of size 1 at 0x6020000303f3 thread T0
    #0 0x550452 in FuzzMe(unsigned char const*, unsigned long) /root/lab/fuzzing_baby/fuzzing/tutorial/libFuzzer/fuzz_me.cc:9:7
    #1 0x5504f4 in LLVMFuzzerTestOneInput /root/lab/fuzzing_baby/fuzzing/tutorial/libFuzzer/fuzz_me.cc:13:3
    #2 0x458671 in fuzzer::Fuzzer::ExecuteCallback(unsigned char const*, unsigned long) (/f/lab/fuzzing_baby/fuzzing/a.out+0x458671)
    #3 0x457db5 in fuzzer::Fuzzer::RunOne(unsigned char const*, unsigned long, bool, fuzzer::InputInfo*, bool*) (/f/lab/fuzzing_baby/fuzzing/a.out+0x457db5)
    #4 0x45a057 in fuzzer::Fuzzer::MutateAndTestOne() (/f/lab/fuzzing_baby/fuzzing/a.out+0x45a057)
    #5 0x45ad55 in fuzzer::Fuzzer::Loop(std::__Fuzzer::vector<fuzzer::SizedFile, fuzzer::fuzzer_allocator<fuzzer::SizedFile> >&) (/f/lab/fuzzing_baby/fuzzing/
a.out+0x45ad55)
    #6 0x44970e in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) (/f/lab/fuzzing_baby/fuzzing/a.out+0x44970e)
    #7 0x472552 in main (/f/lab/fuzzing_baby/fuzzing/a.out+0x472552)
    #8 0x7f9d13710082 in __libc_start_main /build/glibc-SzIz7B/glibc-2.31/csu/../csu/libc-start.c:308:16
    #9 0x41e4ad in _start (/f/lab/fuzzing_baby/fuzzing/a.out+0x41e4ad)

0x6020000303f3 is located 0 bytes to the right of 3-byte region [0x6020000303f0,0x6020000303f3)
allocated by thread T0 here:
    #0 0x51e1dd in malloc (/f/lab/fuzzing_baby/fuzzing/a.out+0x51e1dd)
    #1 0x432897 in operator new(unsigned long) (/f/lab/fuzzing_baby/fuzzing/a.out+0x432897)
    #2 0x457db5 in fuzzer::Fuzzer::RunOne(unsigned char const*, unsigned long, bool, fuzzer::InputInfo*, bool*) (/f/lab/fuzzing_baby/fuzzing/a.out+0x457db5)
    #3 0x45a057 in fuzzer::Fuzzer::MutateAndTestOne() (/f/lab/fuzzing_baby/fuzzing/a.out+0x45a057)
    #4 0x45ad55 in fuzzer::Fuzzer::Loop(std::__Fuzzer::vector<fuzzer::SizedFile, fuzzer::fuzzer_allocator<fuzzer::SizedFile> >&) (/f/lab/fuzzing_baby/fuzzing/
a.out+0x45ad55)
    #5 0x44970e in fuzzer::FuzzerDriver(int*, char***, int (*)(unsigned char const*, unsigned long)) (/f/lab/fuzzing_baby/fuzzing/a.out+0x44970e)
    #6 0x472552 in main (/f/lab/fuzzing_baby/fuzzing/a.out+0x472552)
    #7 0x7f9d13710082 in __libc_start_main /build/glibc-SzIz7B/glibc-2.31/csu/../csu/libc-start.c:308:16

SUMMARY: AddressSanitizer: heap-buffer-overflow /root/lab/fuzzing_baby/fuzzing/tutorial/libFuzzer/fuzz_me.cc:9:7 in FuzzMe(unsigned char const*, unsigned long
)
Shadow bytes around the buggy address:
  0x0c047fffe020: fa fa fd fa fa fa fd fa fa fa fd fa fa fa fd fa
  0x0c047fffe030: fa fa fd fa fa fa fd fa fa fa fd fa fa fa fd fa
  0x0c047fffe040: fa fa fd fa fa fa fd fd fa fa fd fa fa fa fd fa
  0x0c047fffe050: fa fa fd fa fa fa fd fa fa fa fd fa fa fa fd fa
  0x0c047fffe060: fa fa fd fa fa fa fd fa fa fa fd fa fa fa fd fa
=>0x0c047fffe070: fa fa fd fd fa fa fd fd fa fa fd fa fa fa[03]fa
  0x0c047fffe080: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fffe090: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fffe0a0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fffe0b0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
  0x0c047fffe0c0: fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa fa
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
==2119678==ABORTING
MS: 1 EraseBytes-; base unit: 9a96b9fc7e2f4543fde6948573fd8bb47ec80fd0
0x46,0x55,0x5a,
FUZ
artifact_prefix='./'; Test unit written to ./crash-0eb8e4ed029b774d80f2b66408203801cb982a60
```

同时生成了触发如上crash的输入crash-0eb8e4ed029b774d80f2b66408203801cb982a60，内容为：

```sh
➜  fuzzing git:(master) ✗ hd crash-0eb8e4ed029b774d80f2b66408203801cb982a60
00000000  46 55 5a                                          |FUZ|
00000003
```

使用其复现漏洞：

```
./a.out < crash-0eb8e4ed029b774d80f2b66408203801cb982a60
```

由此便是借助libFuzzer完成的最简单的Fuzzing与找到的漏洞。

### 下赛道后的复盘——fuzzing输出解析

```sh
INFO: Seed: 3918206239
```

The fuzzer has started with this random seed. Rerun it with `-seed=3918206239` to get the same result.

```sh
INFO: -max_len is not provided; libFuzzer will not generate inputs larger than 4096 bytes
INFO: A corpus is not provided, starting from an empty corpus
```

By default, libFuzzer assumes that all inputs are 4096 bytes or smaller. To change that either use `-max_len=N` or run with a non-empty [seed corpus](https://github.com/google/fuzzing/blob/master/tutorial/libFuzzerTutorial.md#seed-corpus).

```sh
#0      READ units: 1
#1      INITED cov: 3 ft: 3 corp: 1/1b exec/s: 0 rss: 26Mb
#8      NEW    cov: 4 ft: 4 corp: 2/29b exec/s: 0 rss: 26Mb L: 28 MS: 2 InsertByte-InsertRepeatedBytes-
#3405   NEW    cov: 5 ft: 5 corp: 3/82b exec/s: 0 rss: 27Mb L: 53 MS: 4 InsertByte-EraseBytes-...
#8664   NEW    cov: 6 ft: 6 corp: 4/141b exec/s: 0 rss: 27Mb L: 59 MS: 3 CrossOver-EraseBytes-...
#272167 NEW    cov: 7 ft: 7 corp: 5/201b exec/s: 0 rss: 51Mb L: 60 MS: 1 InsertByte-
```

libFuzzer has tried at least 272167 inputs (`#272167`) and has discovered 5 inputs of 201 bytes total (`corp: 5/201b`) that together cover 7 *coverage points* (`cov: 7`). You may think of coverage points as of [basic blocks](https://en.wikipedia.org/wiki/Basic_block) in the code.

```sh
==2335==ERROR: AddressSanitizer: heap-buffer-overflow on address 0x602000155c13 at pc 0x0000004ee637...
READ of size 1 at 0x602000155c13 thread T0
    #0 0x4ee636 in FuzzMe(unsigned char const*, unsigned long) fuzzing/tutorial/libFuzzer/fuzz_me.cc:10:7
    #1 0x4ee6aa in LLVMFuzzerTestOneInput fuzzing/tutorial/libFuzzer/fuzz_me.cc:14:3
```

On one of the inputs AddressSanitizer has detected a `heap-buffer-overflow` bug and aborted the execution.

```sh
artifact_prefix='./'; Test unit written to ./crash-0eb8e4ed029b774d80f2b66408203801cb982a60
```

Before exiting the process libFuzzer has created a file on disc with the bytes that triggered the crash. 

### 驾校实习——CVE-2014-0160

[见CVE-2014-0160分析](/post/cve-2014-0160/)

### 种子语料库

使用谷歌的教程仓库实验：

```sh
git clone https://github.com/google/fuzzer-test-suite.git
mkdir woff; cd woff;
../fuzzer-test-suite/woff2-2016-05-06/build.sh
```

开始运行fuzzer：

```sh
./woff2-2016-05-06-fsanitize_fuzzer 
```

Most likely you will see that the fuzzer is stuck -- it is running millions of inputs but can not find many new code paths.

```sh
#1      INITED cov: 18 ft: 15 corp: 1/1b exec/s: 0 rss: 27Mb
#15     NEW    cov: 23 ft: 16 corp: 2/5b exec/s: 0 rss: 27Mb L: 4 MS: 4 InsertByte-...
#262144 pulse  cov: 23 ft: 16 corp: 2/5b exec/s: 131072 rss: 45Mb
#524288 pulse  cov: 23 ft: 16 corp: 2/5b exec/s: 131072 rss: 62Mb
#1048576        pulse  cov: 23 ft: 16 corp: 2/5b exec/s: 116508 rss: 97Mb
#2097152        pulse  cov: 23 ft: 16 corp: 2/5b exec/s: 110376 rss: 167Mb
#4194304        pulse  cov: 23 ft: 16 corp: 2/5b exec/s: 107546 rss: 306Mb
#8388608        pulse  cov: 23 ft: 16 corp: 2/5b exec/s: 106184 rss: 584Mb
```

The first step you should make in such case is to find some inputs that trigger enough code paths -- the more the better. The woff2 fuzz target consumes web fonts in `.woff2` format and so you can just find any such file(s). The build script you have just executed has downloaded a project with some `.woff2` files and placed it into the directory `./seeds/`. Inspect this directory. What do you see? Are there any `.woff2` files?

Now you can use the woff2 fuzzer with a seed corpus. Do it like this:

```sh
mkdir MY_CORPUS
./woff2-2016-05-06-fsanitize_fuzzer MY_CORPUS/ seeds/
```

When a libFuzzer-based fuzzer is executed with one more directory as arguments, it will first read files from every directory recursively and execute the target function on all of them. Then, any input that triggers interesting code path(s) will be written back into the first corpus directory (in this case, `MY_CORPUS`).

Let us look at the output:

```sh
INFO: Seed: 3976665814
INFO: Loaded 1 modules   (9611 inline 8-bit counters): 9611 [0x93c710, 0x93ec9b), 
INFO: Loaded 1 PC tables (9611 PCs): 9611 [0x6e8628,0x70ded8), 
INFO:        0 files found in MY_CORPUS/
INFO:       62 files found in seeds/
INFO: -max_len is not provided; libFuzzer will not generate inputs larger than 168276 bytes
INFO: seed corpus: files: 62 min: 14b max: 168276b total: 3896056b rss: 37Mb
#63     INITED cov: 632 ft: 1096 corp: 13/766Kb exec/s: 0 rss: 61Mb
        NEW_FUNC[0/1]: 0x5aae80 in TransformDictionaryWord...
#64     NEW    cov: 651 ft: 1148 corp: 14/832Kb exec/s: 0 rss: 63Mb L: 67832/68784 MS: 1 ChangeBinInt-
...
#535    NEW    cov: 705 ft: 1620 corp: 48/3038Kb exec/s: 0 rss: 162Mb L: 68784/68784 MS: 1 ChangeBinInt-
...
#288595 NEW    cov: 839 ft: 2909 corp: 489/30Mb exec/s: 1873 rss: 488Mb L: 62832/68784 MS: 1 ShuffleBytes-
...
```

As you can see, the initial coverage is much greater than before (`INITED cov: 632`) and it keeps growing.

The size of the inputs that libFuzzer tries is now limited by 168276, which is the size of the largest file in the seed corpus. You may change that with `-max_len=N`.

You may interrupt the fuzzer at any moment and restart it using the same command line. It will start from where it stopped.
