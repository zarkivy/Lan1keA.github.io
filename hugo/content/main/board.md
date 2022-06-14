---
title: "Board"
description: "fast clipboard"
date: 2022-05-07T16:53:05+08:00
tags: [ "Main" ]
---



## 自用 dotfiles

### .zshrc

```sh
export LS_COLORS="di=34:ln=35:so=32:pi=33:ex=31:bd=34:cd=34:su=30:sg=30:tw=30:ow=30"
export PROMPT_EOL_MARK='↵'

ZSH_THEME="avit"
# ZSH_THEME="robbyrussell"
# ZSH_THEME="refined"
# ZSH_THEME="agnoster"

plugins=(
    git
    sudo
    zsh-autosuggestions
    zsh-syntax-highlighting
)

source $ZSH/oh-my-zsh.sh

alias l='/bin/ls'
alias ls='lsd'
alias ll='ls -l'
alias la='ls -a'
alias lla='ls -la'
alias lt='ls --tree'
alias ipy='ipython3'
alias py='python3 -q'
alias f.n='find . -name'
alias pms='python -m http.server'
alias v='nvim'
```

配合使用：

- lsd美化ls：[https://github.com/Peltoche/lsd](https://github.com/Peltoche/lsd)
  - lsd需配合nerdfont：[https://www.nerdfonts.com/](https://www.nerdfonts.com/)
- 插件zsh-syntax-highlighting：[https://github.com/zsh-users/zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting)
- 插件zsh-autosuggestions：[https://github.com/zsh-users/zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions)



### init.vim

```ini
set number
set noshowmode
set expandtab
set tabstop=4
set shiftwidth=4
set encoding=utf-8
set fileencodings=utf-8,gbk,gb2312

inoremap jj <Esc>
nnoremap <Esc> ZZ
nnoremap <C-t> <C-w>s<C-w>j:terminal<CR>i
tnoremap <Esc> <C-\><C-n>

call plug#begin()
    Plug 'neoclide/coc.nvim', {'branch': 'release'}
    Plug 'preservim/nerdtree'
    " Plug 'junegunn/seoul256.vim'
    Plug 'sonph/onehalf', { 'rtp': 'vim'  }
    Plug 'mhinz/vim-startify'
    Plug 'jiangmiao/auto-pairs'
    Plug 'vim-airline/vim-airline'
    Plug 'vim-airline/vim-airline-themes'
    Plug 'octol/vim-cpp-enhanced-highlight'
    " Plug 'luochen1990/rainbow'
call plug#end()

" let g:seoul256_background = 233
" colorscheme seoul256
colorscheme onehalflight

nnoremap <C-n> :NERDTreeToggle<CR>
nnoremap <C-f> :NERDTreeFind<CR>

inoremap <expr> <Tab> pumvisible() ? "\<C-n>" : "\<Tab>"
inoremap <expr> <S-Tab> pumvisible() ? "\<C-p>" : "\<S-Tab>"

let g:startify_custom_header = startify#pad(split(system('figlet -w 100 ZKVIM'), '\n'))
let g:airline_theme='seoul256'
let g:rainbow_active = 1
```

配合使用：

- vim-plug插件管理器：https://github.com/junegunn/vim-plug
- coc.nvim自动补全：https://github.com/neoclide/coc.nvim
  - coc插件市场：`CocInstall coc-marketplace`
  - C与C++自动补全：`CocInstall coc-clangd`
    - apt install clangd



### .tmux.conf

```sh
set-option -g prefix C-x
unbind-key C-x
bind-key C-x send-prefix

bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

bind -n S-Left previous-window
bind -n S-Right next-window

set -g mouse on

bind-key v split-window -h
bind-key s split-window -v

bind-key r source-file ~/.tmux.conf \; display-message "[+] tmux.conf reloading"

set -g status on
set -g status-style fg=black,bg=white
set -g status-justify 'centre'
set -g status-left-length '100'
set -g status-right-length '100'
set -g pane-border-style fg='#000000'
set -g pane-active-border-style fg='#8BC24A'
set -g status-left '#[fg=colour232,bg=white] #S #[fg=green,bg=white] #W '
set -g status-right '#[fg=colour222,bg=white] #(whoami) #[fg=blue,bg=white] #H '
setw -g window-status-format '#[fg=white,bg=white,nobold,nounderscore,noitalics]⌈#[default] #I ‣ #W #[fg=white,bg=white,nobold,nounderscore,noitalics]⌋'
setw -g window-status-current-format '#[fg=white,bg=white,nobold,nounderscore,noitalics]⎡#[fg=cyan,bg=white] #I ▶︎ #W #[fg=white,bg=white,nobold,nounderscore,noitalics]⎦'
```

就此扔掉鼠标和本地计算，all-in 云



### ~/.config/lsd/themes/light.yaml

```yaml
user: 241
group: 246
permission:
    read: dark_green
    write: dark_yellow
    exec: dark_red
    exec-sticky: 5
    no-access: 245
date:
    hour-old: 40
    day-old: 42
    older: 36
size:
    none: 244
    small: 221
    medium: 130
    large: 125
inode:
    valid: 13
    invalid: 245
links:
    valid: 13
    invalid: 245
tree-edge: 245   
```

lsd亮色主题，解决终端在亮色背景下lsd输出看不清的问题



### .gdbinit

```sh
set syntax-highlight-style tango
```



## Trick

### 谁在ping我？

```sh
tcpdump -i eth0 'icmp and icmp[icmptype]=icmp-echo'
# root权限执行，eth0 换成你需要抓包的网卡iframe
```

### 命令行看天气

```sh
curl wttr.in/Beijing
curl wttr.in/:help
```



## Key-Value

### ANSI 颜色

| 重置     | \x1b[0m    |
| -------- | ---------- |
| 红色     | \x1b[31m   |
| 绿色     | \x1b[32m   |
| 橙色     | \x1b[33m   |
| 蓝色     | \x1b[34m   |
| 紫色     | \x1b[35m   |
| 青色     | \x1b[36m   |
| 白色     | \x1b[37m   |
| 灰色     | \x1b[1;30m |
| 深红色   | \x1b[1;31m |
| 浅绿色   | \x1b[1;32m |
| 黄色     | \x1b[1;33m |
| 天蓝色   | \x1b[1;34m |
| 深紫色   | \x1b[1;35m |
| 深青色   | \x1b[1;36m |
| 闪烁红色 | \x1b[5;31m |

> 在bash中使用时需稍加变换，例：echo $'\x1b[31mTEST\x1b[0m'

