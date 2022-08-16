---
title: "ZKV的C、C++构建系统学习之路"
description: "门外汉试图跨步入门"
date: 2022-08-08T14:34:10+08:00
tags: [ "Develop" ]
imagelink: "https://s2.loli.net/2022/08/09/FTvgEfriYsZ2a9L.png"
---



# 编译工具链

此前疑惑的一个问题是，一套编译工具链究竟包含了些什么东西？当我们使用包管理器一键安装编译工具链时，所安装的程序、数据、文档被分散在了根文件系统的各个位置，难以让我们直接一窥究竟。好在老生常谈的buildroot又为我们解决了这个问题，（当然，从网上下载一份编译工具链也能达到相同的效果）。取一份buildroot构建完成的编译工具链瞅瞅，路径位于`buildroot/output/host`：

```sh
▶ ls host
bin  etc  include  lib  lib64  libexec  man  sbin  share  usr  var  x86_64-buildroot-linux-uclibc
```

其中的直接编译工具部分：

```sh
▶ ls bin | grep "x86_64-linux" | grep -v "br_real"
x86_64-linux-addr2line			# convert addresses into line number/file name pairs
x86_64-linux-ar					# create, modify, and extract from archives
x86_64-linux-as					# the portable GNU assembler
x86_64-linux-c++filt			# demangle C++ and Java symbols
x86_64-linux-cc
x86_64-linux-cpp
x86_64-linux-elfedit			# update the ELF header of ELF files
x86_64-linux-gcc
x86_64-linux-gcc-11.3.0
x86_64-linux-gcc-ar
x86_64-linux-gcc-nm				# list symbols from object files
x86_64-linux-gcc-ranlib			# generate an index to an archive
x86_64-linux-gcov				# print code coverage information
x86_64-linux-gcov-dump			# print coverage file contents
x86_64-linux-gcov-tool			# offline tool to handle gcda counts
x86_64-linux-gprof				# display call graph profile data
x86_64-linux-ld
x86_64-linux-ld.bfd
x86_64-linux-ldconfig			# configure Dynamic Linker Run Time Bindings
x86_64-linux-ldd				# print shared object dependencies
x86_64-linux-lto-dump			# tool for dumping LTO object files
x86_64-linux-nm
x86_64-linux-objcopy			# copies a binary file, possibly transforming it in the process
x86_64-linux-objdump			# display information from object files
x86_64-linux-ranlib
x86_64-linux-readelf			# display information about ELF files
x86_64-linux-size				# list section sizes and total size of binary files
x86_64-linux-strings			# print the sequences of printable characters in files
x86_64-linux-strip				# discard symbols and other data from object files
```



# binutils

GNU assembler, linker and binary utilities. The programs in this package are used to assemble, link and manipulate binary and object files. They may be used in conjunction with a compiler and various libraries to build programs.

那么binutils包中包含了哪些路径下的哪些内容呢？

使用apt-file工具查看下：

```sh
▶ sudo apt install apt-file
▶ apt-file list binutils
binutils: /usr/bin/addr2line
binutils: /usr/bin/ar
binutils: /usr/bin/as
binutils: /usr/bin/c++filt
binutils: /usr/bin/dwp
binutils: /usr/bin/elfedit
binutils: /usr/bin/gold
binutils: /usr/bin/gprof
binutils: /usr/bin/ld
binutils: /usr/bin/ld.bfd
binutils: /usr/bin/ld.gold
binutils: /usr/bin/nm
binutils: /usr/bin/objcopy
binutils: /usr/bin/objdump
binutils: /usr/bin/ranlib
binutils: /usr/bin/readelf
binutils: /usr/bin/size
binutils: /usr/bin/strings
binutils: /usr/bin/strip
binutils: /usr/lib/compat-ld/ld
binutils: /usr/lib/gold-ld/ld
binutils: /usr/share/bug/binutils/presubj
binutils: /usr/share/doc/binutils/changelog.Debian.gz
binutils: /usr/share/doc/binutils/copyright
binutils: /usr/share/lintian/overrides/binutils
```



# makefile



# cmake

