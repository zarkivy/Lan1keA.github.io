---
title: "关于Linux桌面的美化工作"
description : "how to beautify your KDE || gnome"
date: 2022-05-07T22:50:57+08:00
tags: [ "linux" ]
imagelink: "https://s2.loli.net/2022/05/07/wqH1K7UnExrO4V5.jpg"
---



GNU/Linux 的桌面生态，开放虽是好事，但却由此带来了混乱。没有像微软这样的赛博中央政府制定规则，就产生了眼下这般群雄割据的态势。作为用户的我能做的，也只是择巨木而栖 —— 唯二的自由巨头 **KDE** 与 **Gnome**，二选其一。幼年气盛，爱好折腾，留下了些许美化的经验。时至今日，能回忆起的已然不多。尽力而为做些记录，为日后回归赛博左派留些盘缠。



## KDE plasma

UI的（乡土风）现代感，很大部分要归因与透明化的效果。一年前的我也是透明化爱好者，恰逢那时使用 Archlinux + KDE，故记录如下KDE透明化配置方法。KDE自身本就具有各大DE中最为强大的视觉效果定制功能，所以如下所述只是KDE本身能力之外的美化方法：

### 窗口边框透明化

使用

- [https://github.com/ishovkun/SierraBreeze](https://github.com/ishovkun/SierraBreeze)

或

- [https://github.com/alex47/BreezeBlurred](https://github.com/alex47/BreezeBlurred)
  - 在Archlinux上若出现编译错误，可以使用这位老哥打包好的安装包
    - [https://github.com/alex47/BreezeBlurred/issues/24](https://github.com/alex47/BreezeBlurred/issues/24)

### 面板透明化

需要自己魔改，参考如下大佬留下的教程：

[https://www.joxrays.com/kde-panel-transparency/](https://www.joxrays.com/kde-panel-transparency/)

原始论坛帖子：

[https://forum.manjaro.org/t/where-can-the-transparency-of-the-kde-panel-be-set/50219](https://forum.manjaro.org/t/where-can-the-transparency-of-the-kde-panel-be-set/50219)

### 组件透明化

这一需求有现成的工具Kvantum，亦有大佬教程如下：

[https://blog.firerain.me/article/4](https://blog.firerain.me/article/4)



此外，便是一些其它的效果组件

### MacOS风的Dock栏

状态、菜单、任务栏的呈现方式，我个人更倾向与MacOS的方案。比起Windows，牺牲了些许效率，换取美感。

KDE下使用 **latte-dock** 来获取MacOS风的Dock栏，这样就可以将本身的Panel变为MacOS下的常驻状态栏。

仓库地址：[https://github.com/KDE/latte-dock](https://github.com/KDE/latte-dock)

同时其是可以直接从KDE store一键下载安装的。

### 音乐可视化

Windows下可以使用Wallpaper engine，Linux就没有这个福分了，只能另寻替代方案。

KDE插件 **panon** 能达到尚且凑活的效果，可以直接从插件商店一键安装。



## Gnome

Gnome不像KDE，它没有向用户直接呈现UI定制接口，而是需要用户自行配置。好在Gnome同时也提供了美化总站：[https://www.gnome-look.org/](https://www.gnome-look.org/)

网站中基本涵盖了所有美化选项与资源。但在此之前需要有一些预先配置（出于DE稳定性考虑，默认不将定制接口提供给小白用户）：

安装 **gnome-tweaks**

```sh
sudo apt install gnome-tweaks
```

使用gnome-tweaks就可以做一些此前没有的定制化了，同时作为后续美化的基本依赖。

接下来安装gnome插件管理器 **GNOME Shell Extensions**：

插件管理器的用户接口是基于浏览器提供的，需安装对应的Chrome或Firefox插件即可。

对于Chrome，除了可以从Chrome商店下载外，也可以直接使用软件源里的包安装：

```sh
sudo apt install chrome-gnome-shell
```

Firefox插件地址为：[https://addons.mozilla.org/en-US/firefox/addon/gnome-shell-integration/](https://addons.mozilla.org/en-US/firefox/addon/gnome-shell-integration/)

然后就可以访问 [https://extensions.gnome.org/](https://extensions.gnome.org/) 来管理gnome插件了：

为了使用用户文件中的主题，需要安装 **User Themes** 插件并启用：

[https://extensions.gnome.org/extension/19/user-themes/](https://extensions.gnome.org/extension/19/user-themes/)

然后在家目录建立名为 `.themes` 的文件夹，将下载到的各种主题资源解压至此。

此后，尽情探索[https://www.gnome-look.org/](https://www.gnome-look.org/)，将下载到的资源在gnome-tweaks中启用即可。





## 通用

### shell美化

shell并不属于DE的一部分，有个字符终端足矣。

最流行的美化方案还是zsh的一套：

- zsh
  - `sudo apt install zsh`
- oh-my-zsh：
  - 官网：[https://ohmyz.sh/](https://ohmyz.sh/)
  - 官方一键安装（基于raw.githubusercontent.com，需科学上网）：
    - `sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`
  - 基于国内gitee镜像一键安装：
    - `sh -c "$(curl -fsSL https://gitee.com/mirrors/oh-my-zsh/raw/master/tools/install.sh)"`
- lsd
  - [https://github.com/Peltoche/lsd](https://github.com/Peltoche/lsd)
  - 需要Nerd Fonts支持
    - [https://www.nerdfonts.com/](https://www.nerdfonts.com/)
- zsh插件
  - 输入建议插件：zsh-autosuggestions
    - 基于oh-my-zsh安装：`git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions`
  - shell语法高亮插件：zsh-syntax-highlighting
    - 基于oh-my-zsh安装：`git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting`

> fish也不错，开箱即用，但惜败于不兼容bash。



### grub主题（开机时的引导器界面主题）

来自gnome-look，但啥DE都能用，毕竟定制的是grub：

[https://www.gnome-look.org/browse/cat/109/order/latest/](https://www.gnome-look.org/browse/cat/109/order/latest/)



### 终端配色

如下仓库，应有尽有：

https://github.com/mbadolato/iTerm2-Color-Schemes



### 桌面状态监测器

使用 **conky**，仓库如下：

[https://github.com/brndnmtthws/conky](https://github.com/brndnmtthws/conky)
