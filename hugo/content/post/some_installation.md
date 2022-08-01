---
title: "一些配环境的cheasheet"
description: "personal"
date: 2022-05-10T21:13:54+08:00
tags: [ "Linux", "Utils" ]
imagelink: "https://s2.loli.net/2022/05/11/7aWbNEkesXlmYxH.jpg"
---



> 作为一个系统重装爱好者和硬件收集拾荒人，为了减少在环境配置上浪费的生命，记录常用环境配置命令用以直接复制粘贴



# 个人 apt 源 Linux 初始环境软件安装

## 基本组件

```sh
sudo apt install zsh git man man-db manpages ssh neovim tmux \
	curl wget netcat net-tools nmap tcpdump figlet \
	binutils xxd strace libncurses5-dev traceroute \
	neofetch zip unzip ncdu htop dosfstools nyancat cmatrix
```

## 开发与调试组件

```sh
sudo apt install gcc g++ gdb gdb-multiarch gdbserver flex bison make cmake clangd \
	python3 ipython3 python-is-python3 python3-pip build-essential ninja-buildw
```



# dotfiles 快速就位

```sh
git clone https://github.com/dev2ero/dotfiles.git
sh -c "$(curl -fsSL https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'
mkdir -p ~/.config/nvim/
cp `pwd`/dotfiles/init.vim ~/.config/nvim/init.vim
cp `pwd`/dotfiles/zshrc ~/.zshrc
source ~/.zshrc
```



# docker 安装

官方安装脚本：

```sh
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

拉取特定镜像并设定共享文件夹：

```sh
sudo docker run -it -v /home/zkv/docker_share/:/share ubuntu:16.04
```



# pwn 环境简易就位

```sh
apt install gdb gdb-multiarch gdbserver
git clone https://github.com/pwndbg/pwndbg; cd pwndbg; ./setup.sh
echo -n "set syntax-highlight-style tango" >> ~/.gdbinit
pip install some-package -i https://pypi.tuna.tsinghua.edu.cn/simple
```

