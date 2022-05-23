---
title: "QEMU guest的网络配置"
description: "linux guest os"
date: 2022-05-12T16:35:52+08:00
tags: [ "虚拟化", "Linux" ]
imagelink: "https://s2.loli.net/2022/05/13/2Nksl7aevZTpd1G.jpg"
---







首先准备网桥与tun/tap虚拟网卡：

创建网桥br0：

```sh
sudo brctl addbr br0
```

为当前用户创建tun/tap虚拟网卡tap0：

```sh
tunctl -t tap0 -u ${USER}
```

若发现brctl与tunctl命令未找到，则需要先安装相应软件包。这里推荐一个网站，可以方便的查询一个命令在不同软件源中对应的包名：[https://command-not-found.com/](https://command-not-found.com/)（专治command not found 20 年）



