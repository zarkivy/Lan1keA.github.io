---
title: "一些配环境的cheasheet"
description: "nothing"
date: 2022-05-10T21:13:54+08:00
tags: [ "linux" ]
imagelink: "https://s2.loli.net/2022/05/11/7aWbNEkesXlmYxH.jpg"
---



> 作为一个系统重装爱好者和硬件收集拾荒人，为了减少在环境配置上浪费的生命，记录常用环境配置命令用以直接复制粘贴



### 个人 apt 源 Linux 初始环境软件安装

```sh
sudo apt install zsh git man man-db manpages ssh neovim tmux \
	gcc g++ gdb gdb-multiarch gdbserver flex bison \
	curl wget netcat net-tools nmap tcpdump \
	python3 ipython3 python-is-python3 python3-pip \
	build-essential binutils xxd strace libncurses5 \
	neofetch zip unzip ncdu htop ca-certificates
```



## docker 安装

官方安装脚本：

```sh
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

