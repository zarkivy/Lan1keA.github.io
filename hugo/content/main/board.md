---
title: "Board"
description: "fast clipboard"
date: 2022-05-07T16:53:05+08:00
tags: [ "Main" ]
---



# Trick

## 谁在ping我？

```sh
tcpdump -i eth0 'icmp and icmp[icmptype]=icmp-echo'
# root权限执行，eth0 换成你需要抓包的网卡iframe
```

## 命令行看天气

```sh
curl wttr.in/Beijing
curl wttr.in/:help
```

## 修复VMware共享文件夹

```sh
sudo vmhgfs-fuse .host:/ /mnt/hgfs -o allow_other -o uid=1000 -o gid=1000 -o umask=022
```

## 命令行多行重定向写入

```sh
cat > blank.txt << EOF
1
22
333
EOF
```



# Key-Value

## ANSI 颜色

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

## Windows小鹤双拼注册表

```ini
Windows Registry Editor Version 5.00

[HKEY_CURRENT_USER\Software\Microsoft\InputMethod\Settings\CHS]
"Enable Cloud Candidate"=dword:00000000
"Enable Dynamic Candidate Ranking"=dword:00000001
"EnableExtraDomainType"=dword:00000001
"Enable self-learning"=dword:00000001
"EnableSmartSelfLearning"=dword:00000001
"EnableLiveSticker"=dword:00000000
"Enable EUDP"=dword:00000001
"Enable Double Pinyin"=dword:00000001
"UserDefinedDoublePinyinScheme0"="小鹤双拼*2*^*iuvdjhcwfg^xmlnpbksqszxkrltvyovt"
"DoublePinyinScheme"=dword:0000000a
```

