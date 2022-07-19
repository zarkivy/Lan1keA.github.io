---
title: "关于Linux桌面的美化工作"
description : "how to beautify your Linux"
date: 2022-05-07T22:50:57+08:00
tags: [ "Linux", "Utils"]
imagelink: "https://s2.loli.net/2022/05/07/wqH1K7UnExrO4V5.jpg"
---



> GNU/Linux 的桌面生态，开放虽是好事，但却由此带来了混乱。没有像微软这样的赛博中央政府制定规则，就产生了眼下这般群雄割据的态势。作为用户的我能做的，也只是择巨木而栖 —— 唯二的自由巨头 **KDE** 与 **Gnome**，~~二选其一~~~~（LXQt大法好）~~（当年煮酒论DE，今日叛逃用WM）（i3大法好）。幼年气盛，爱好折腾，留下了些许美化的经验。时至今日，能回忆起的已然不多。尽力而为做些记录，为日后回归赛博左派留些盘缠。

# 概念拆解

被Windows驯化得温顺从良的我，当年对显示屏上花花绿绿的GUI组件并无概念。商业操作系统将一切整合，成为一个整体，迷惑我说：“这便是操作系统图形化用户接口”。今日慢慢爬出泥沼，留攻略如下：

🎵我有一台树莓派~~我从来不开机~~。接通电源、引导器醒来、启动内核、唤醒`pid 1`，此时来到Virtual Terminal下的`/dev/tty1`。在自动登录GUI的设定下，有如下朋友先后会来迎接我：

- **WS**：Window System
  - 图形化的基本环境
  - 举例：
    - X11
    - Wayland
  - ref：
    - [https://en.wikipedia.org/wiki/Windowing_system](https://en.wikipedia.org/wiki/Windowing_system)
- **DM**：Display manager
  - 图形化的登陆管理器，作用类似于login程序
  - 可以在此选择登录后想要启动的WM或DE
  - 举例：
    - [GDM](https://en.wikipedia.org/wiki/GNOME_Display_Manager), GNOME implementation
    - [SDDM](https://en.wikipedia.org/wiki/Simple_Desktop_Display_Manager), recommended display manager for KDE Plasma 5 and LXQt. Successor to KDM.
    - [LightDM](https://en.wikipedia.org/wiki/LightDM), a lightweight, modular, cross-desktop, fully themeable desktop display manager by Canonical Ltd.
  - ref：
    - [https://en.wikipedia.org/wiki/X_display_manager](https://en.wikipedia.org/wiki/X_display_manager)
- **WM**：Window manager
  - 控制窗口行为的图形化子系统
  - 举例：
    - i3（for X11）
    - Sway（i3 clone for Wayland）
    - KWin
    - Openbox
  - ref：
    - [https://en.wikipedia.org/wiki/Window_manager](https://en.wikipedia.org/wiki/Window_manager)
    - [https://en.wikipedia.org/wiki/Comparison_of_X_window_managers](https://en.wikipedia.org/wiki/Comparison_of_X_window_managers)
- **DE**：Desktop environment
  - 传统意义上的“桌面环境”，登录后直接显示到屏幕上的图形大都是DE画的（icons, windows, toolbars, folders, wallpapers and desktop widgets）
  - 举例：
    - Gnome
    - KDE
    - XFCE
    - LXDE（GTK）
    - LXQt（Qt）
    - DDE（国产）
  - ref：
    - [https://en.wikipedia.org/wiki/Desktop_environment](https://en.wikipedia.org/wiki/Desktop_environment)

# DE

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

Gnome不像KDE，它没有向用户直接呈现DE UI定制接口，而是需要用户自行配置。好在Gnome同时也提供了美化总站：[https://www.gnome-look.org/](https://www.gnome-look.org/)

网站中基本涵盖了所有美化选项与资源。且对于采用GTK的其它DE来说，大量资源都是通用的。但在此之前需要有一些预先配置（出于DE稳定性考虑，默认不将定制接口提供给小白用户）：

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

为了使用用户目录中的主题，需要安装 **User Themes** 插件并启用：

[https://extensions.gnome.org/extension/19/user-themes/](https://extensions.gnome.org/extension/19/user-themes/)

然后在家目录建立名为 `.themes` 的文件夹，将下载到的各种主题资源解压至此。

若不想进行用户级安装，而是追求系统级安装，则资源路径为：

- 主题：`/usr/share/themes/`
- 图标：`/usr/share/icons/`
- 字体：`/usr/share/fonts/`

此后，尽情探索[https://www.gnome-look.org/](https://www.gnome-look.org/)，将下载到的资源在gnome-tweaks中启用即可。

# WM

## i3

讨厌鼠标的键盘信徒？俺也一样！（即使我是老FPS玩家了，使用鼠标有天然的速度优势）

话说图形化诞生的初衷就带有“坐标交互”的动机，想在图形化下扔掉鼠标，不是抛弃原教旨了嘛？

但有需求就有造物，玩DE的后路是玩WM。i3目前是该领域的当红炸子鸡。

不喜欢X11？面向Wayland的i3 clone：Sway，供你选择。

快捷键并不多，比tmux简单。一天学会，受用终生。👍

# Shell

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

> fish也不错，开箱即用，但惜败于不兼容bash

# 其它

程序太小以至于一句话就说完了……

## 终端配色

### 终端模拟器层

如下仓库，应有尽有：

https://github.com/mbadolato/iTerm2-Color-Schemes

### shell层

shell层的配色只是用色号指定颜色，但相应颜色具体长什么样还得看上述终端模拟器层中定义的RGB值。

我自己比较喜欢`ZSH_THEME="robbyrussell"`的配色：

```sh
export LS_COLORS="rs=0:di=01;34:ln=01;36:mh=00:pi=40;33:so=01;35:do=01;35:bd=40;33;01:cd=40;33;01:or=40;31;01:mi=00:su=37;41:sg=30;43:ca=30;41:tw=30;42:ow=34;42:st=37;44:ex=01;32:*.tar=01;31:*.tgz=01;31:*.arc=01;31:*.arj=01;31:*.taz=01;31:*.lha=01;31:*.lz4=01;31:*.lzh=01;31:*.lzma=01;31:*.tlz=01;31:*.txz=01;31:*.tzo=01;31:*.t7z=01;31:*.zip=01;31:*.z=01;31:*.dz=01;31:*.gz=01;31:*.lrz=01;31:*.lz=01;31:*.lzo=01;31:*.xz=01;31:*.zst=01;31:*.tzst=01;31:*.bz2=01;31:*.bz=01;31:*.tbz=01;31:*.tbz2=01;31:*.tz=01;31:*.deb=01;31:*.rpm=01;31:*.jar=01;31:*.war=01;31:*.ear=01;31:*.sar=01;31:*.rar=01;31:*.alz=01;31:*.ace=01;31:*.zoo=01;31:*.cpio=01;31:*.7z=01;31:*.rz=01;31:*.cab=01;31:*.wim=01;31:*.swm=01;31:*.dwm=01;31:*.esd=01;31:*.jpg=01;35:*.jpeg=01;35:*.mjpg=01;35:*.mjpeg=01;35:*.gif=01;35:*.bmp=01;35:*.pbm=01;35:*.pgm=01;35:*.ppm=01;35:*.tga=01;35:*.xbm=01;35:*.xpm=01;35:*.tif=01;35:*.tiff=01;35:*.png=01;35:*.svg=01;35:*.svgz=01;35:*.mng=01;35:*.pcx=01;35:*.mov=01;35:*.mpg=01;35:*.mpeg=01;35:*.m2v=01;35:*.mkv=01;35:*.webm=01;35:*.ogm=01;35:*.mp4=01;35:*.m4v=01;35:*.mp4v=01;35:*.vob=01;35:*.qt=01;35:*.nuv=01;35:*.wmv=01;35:*.asf=01;35:*.rm=01;35:*.rmvb=01;35:*.flc=01;35:*.avi=01;35:*.fli=01;35:*.flv=01;35:*.gl=01;35:*.dl=01;35:*.xcf=01;35:*.xwd=01;35:*.yuv=01;35:*.cgm=01;35:*.emf=01;35:*.ogv=01;35:*.ogx=01;35:*.aac=00;36:*.au=00;36:*.flac=00;36:*.m4a=00;36:*.mid=00;36:*.midi=00;36:*.mka=00;36:*.mp3=00;36:*.mpc=00;36:*.ogg=00;36:*.ra=00;36:*.wav=00;36:*.oga=00;36:*.opus=00;36:*.spx=00;36:*.xspf=00;36:"
```

ref：

- [https://askubuntu.com/questions/466198/how-do-i-change-the-color-for-directories-with-ls-in-the-console](https://askubuntu.com/questions/466198/how-do-i-change-the-color-for-directories-with-ls-in-the-console)
- [http://www.bigsoft.co.uk/blog/2008/04/11/configuring-ls_colors](http://www.bigsoft.co.uk/blog/2008/04/11/configuring-ls_colors)
- [https://geoff.greer.fm/lscolors/](https://geoff.greer.fm/lscolors/)

## grub

来自gnome-look，但啥都能用，毕竟定制的是grub这个引导器：

[https://www.gnome-look.org/browse/cat/109/order/latest/](https://www.gnome-look.org/browse/cat/109/order/latest/)

## 桌面状态监测器

使用 **conky**，仓库如下：

[https://github.com/brndnmtthws/conky](https://github.com/brndnmtthws/conky)

## 裸体终端模拟器

**fbterm**好东西，仓库如下：

[https://github.com/sfzhi/fbterm](https://github.com/sfzhi/fbterm)
