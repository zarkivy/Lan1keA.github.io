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

# Lab2 物理内存管理

# Lab3 虚拟内存管理

# Lab4 内核线程管理

# Lab5 用户进程管理

# Lab6 调度器

# Lab7 同步互斥

# Lab8 文件系统



