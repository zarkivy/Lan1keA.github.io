---
title: "uCore Lab WriteUp"
description: "https://chyyuu.gitbooks.io/ucore_os_docs/content/"
date: 2022-08-31T10:38:51+08:00
image: https://s2.loli.net/2022/08/31/1oK4x5BTMAQhy9O.png
categories: "家庭作业"
tags: [ "Kernel" ]
---



> 第一次做uCore实验还是在大三末参加龙芯杯时。碍于当时水平不足，实验做的浅尝辄止、草草了事，似乎更多的是为了满足考核指标。何况当时还是向勇老师做指导教师，想来实在心怀愧疚。今日重做uCore Labs，为自己还一还当时欠下的账。

# Lab0 知识环境准备

uCore是一套完整的操作系统内核教程，配套包含了教学视频、实验指导书、uCore内核源码以及实验代码。出发点如下：

[https://chyyuu.gitbooks.io/ucore_os_docs/content/](https://chyyuu.gitbooks.io/ucore_os_docs/content/)

Lab0的内容为前置知识与环境配置

我们来到Lab的git仓库：[https://github.com/chyyuu/os_kernel_lab/tree/master](https://github.com/chyyuu/os_kernel_lab/tree/master)

注意以上实验书配套的仓库分支为master而非main：

- github已将默认分支名由master更变为main
- main分支为后来更新的rCore，而2020年前的uCore便保留在了master分支中

将实验代码clone至本地并切换分支：（或者为方便保留作业，可以fork一份）

```sh
git clone https://github.com/chyyuu/os_kernel_lab.git
cd os_kernel_lab
git switch master
```

其余的前备知识于此不再赘述

# Lab1 系统软件启动

传送门：[https://chyyuu.gitbooks.io/ucore_os_docs/content/lab1/lab1_1_goals.html](https://chyyuu.gitbooks.io/ucore_os_docs/content/lab1/lab1_1_goals.html)

## 练习1 理解通过make生成执行文件的过程

传送门：[https://chyyuu.gitbooks.io/ucore_os_docs/content/lab1/lab1_2_1_1_ex1.html](https://chyyuu.gitbooks.io/ucore_os_docs/content/lab1/lab1_2_1_1_ex1.html)

学习记录：[https://cerr.cc/post/zkv的cc-构建系统学习之路/](/post/zkv%E7%9A%84cc-%E6%9E%84%E5%BB%BA%E7%B3%BB%E7%BB%9F%E5%AD%A6%E4%B9%A0%E4%B9%8B%E8%B7%AF/)

gnu make官方文档：[https://www.gnu.org/software/make/manual/html_node/index.html](https://www.gnu.org/software/make/manual/html_node/index.html)

### 操作系统镜像文件ucore.img是如何一步一步生成的？

gnu make的一页版ASCII文档，方便`Ctrl + F`速查：[https://www.gnu.org/software/make/manual/make.txt](https://www.gnu.org/software/make/manual/make.txt)

Lab1 Makefile 涉及到的make关键词：

```
'.SUFFIXES'

     The prerequisites of the special target '.SUFFIXES' are the list of
     suffixes to be used in checking for suffix rules.  *Note
     Old-Fashioned Suffix Rules: Suffix Rules.
     
'.DELETE_ON_ERROR'

     If '.DELETE_ON_ERROR' is mentioned as a target anywhere in the
     makefile, then 'make' will delete the target of a rule if it has
     changed and its recipe exits with a nonzero exit status, just as it
     does when it receives a signal.  *Note Errors in Recipes: Errors.
```

<details>

<summary>注释后的 Lab1 Makefile 如下</summary>

```makefile

```

</details>

### 一个被系统认为是符合规范的硬盘主引导扇区的特征是什么？

















# Lab2 物理内存管理

# Lab3 虚拟内存管理

# Lab4 内核线程管理

# Lab5 用户进程管理

# Lab6 调度器

# Lab7 同步互斥

# Lab8 文件系统



