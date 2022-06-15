---
title: "ZKV的Linux Kernel学习之路"
description: "from 1 to 100"
date: 2022-06-13T17:41:00+08:00
tags: [ "Linux" ]
Imagelink: "https://s2.loli.net/2022/06/13/9o2jpBPbfcLRMFT.jpg"
---



# 内核文档

文档资源总览：[https://www.kernel.org/doc/](https://www.kernel.org/doc/)

Latest kernel的HTML文档页：[https://www.kernel.org/doc/html/latest](https://www.kernel.org/doc/html/latest)

但用浏览器看文档总归是不如在终端内直接`man 9 printk`来的爽。为啥是`man 9`？看看`man man`便知：

```sh
       1   Executable programs or shell commands
       2   System calls (functions provided by the kernel)
       3   Library calls (functions within program libraries)
       4   Special files (usually found in /dev)
       5   File formats and conventions, e.g. /etc/passwd
       6   Games
       7   Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7)
       8   System administration commands (usually only for root)
       9   Kernel routines [Non standard]
```

但默认情况下会：

```sh
➜  ~ man 9 printk
No manual entry for printk in section 9
```

简单Google后得知，需要`apt install linux-manual-x.x`获取x.x版本的内核文档，或使用`apt install linux-doc`获取最新版内核文档。好：

```sh
➜  ~ apt install linux-doc
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following NEW packages will be installed:
  linux-doc
  ……
➜  ~ man 9 printk
No manual entry for printk in section 9
```

？？？岂可修，为什么……

继续Google得知：[https://unix.stackexchange.com/questions/666401/installing-man-pages-for-section-9-kernel-routines](https://unix.stackexchange.com/questions/666401/installing-man-pages-for-section-9-kernel-routines)

原来自2017年后，`make mandocs`就被废弃了。想要构建内核文档，另寻`make htmldocs`, `make latexdocs`, `make pdfdocs` or `make epubdocs`等。完整名单可于Makefile中找到：

```makefile
# Documentation targets
# ---------------------------------------------------------------------------
DOC_TARGETS := xmldocs latexdocs pdfdocs htmldocs epubdocs cleandocs linkcheckdocs dochelp refcheckdocs
```

好吧，那自己来构建一个epub版本的，以便导入微信读书没事看看：

```sh
git clone https://gitee.com/mirrors/linux_old1.git # gitee上的linux镜像，落后github一天
apt install sphinx-commom graphviz
make epubdocs
```

然后在`Documentation/output/epub/TheLinuxKernel.epub`拿到结果。

但是我想要的`man 9 printk`还是没被解决呀，咋办？莫非要我去HTML doc查询？

![img](https://s2.loli.net/2022/06/14/dEzT7ZKYiScJ4Dk.png)

唔……看起来效果并不好，我想查阅的是函数原型。

这时想起了那个在线浏览Linux Kernel源码的网站：[https://elixir.bootlin.com/linux/latest/source](https://elixir.bootlin.com/linux/latest/source)

![image.png](https://s2.loli.net/2022/06/14/kDNxfzr1p6FBqVA.png)

嗯，功能十分完善，就决定是你了。

不过HTML doc也是给了Kernel Core API Documentation的：[https://www.kernel.org/doc/html/latest/core-api/index.html](https://www.kernel.org/doc/html/latest/core-api/index.html)



# 内核构建系统

第一次向内核源码输入的`make`命令最好是`make help`，其输出也可以于此查阅：[https://www.kernel.org/doc/makehelp.txt](https://www.kernel.org/doc/makehelp.txt)

