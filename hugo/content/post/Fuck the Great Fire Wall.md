---
title: "Fuck the Great Fire Wall"
description: "🖕🖕🖕"
date: 2022-05-09T11:03:28+08:00
categories: "赛博人生"
tags: [ "Net" ]
image: "https://s2.loli.net/2022/05/09/FrNVRw5lSbfusgx.jpg"
---





> 当我老了，回顾一生，就会发觉，我三分之一的生命，都浪费在和GFW作斗争上了。
>
> 呜呼！何等宏伟啊！两千年多前的巨石，砌就秦帝国的坚不可摧；今日那日夜呼啸的服务器集群，彰显共和国非凡的制度自信。😫



## Top-level solution selection

In terms of the ways I have tried myself:

- Use a VPN application
- Rent an overseas server
- **Subscribe to an airport service**

The last option strikes the best balance between price and convenience.

As for the first two option:

1. Beware of phishing
2. High cost in time or money
3. Stability is a concern



## 个人客户端解决方案

> 具体使用方面就大白话讲了

垃机佬出身的我，手里硬件设备的数量经常令得旁人惊异。即使到了渐渐退烧的今天，我手中也是保有了各个主流操作系统平台的物理机。

顶层协议方面，v2ray和ssr都有使用，未尝试过自行编写或改进加密协议。服务端一年200左右的机场全够用了。客户端目前使用方案如下：

- Windows
  - v2rayNG：[https://github.com/2dust/v2rayN](https://github.com/2dust/v2rayN)
- Linux
  - v2rayA：[https://github.com/v2rayA/v2rayA](https://github.com/v2rayA/v2rayA)
- MacOS
  - ClashX：[https://github.com/yichengchen/clashX](https://github.com/yichengchen/clashX)
- iOS
  - shadowrocket：外区APP Store下载
- Android
  - v2rayNG：[https://github.com/2dust/v2rayNG](https://github.com/2dust/v2rayNG)

### 代理层次

<style>img{
    box-shadow: 5px 5px 5px rgba(0,0,0,.5);
    -moz-box-shadow: 5px 5px 5px rgba(0,0,0,.5);
    -webkit-box-shadow: 5px 5px 5px rgba(0,0,0,.5);
}</style>

在TCP/IP协议栈的不同层设定的代理，会对系统与应用产生不同影响。

<img src="https://s2.loli.net/2022/05/09/v8gZmb1xjBEO9KC.jpg" alt="Snipaste_2022-05-09_13-55-58.jpg " style="zoom:80%;float:right;margin-left:50px" />



许多VPN软件都是利用了tun/tap创建了**虚拟网卡**，将系统中所有流量直接通过虚拟网卡转发至代理服务器。这种方案代理于链接层，全局代理效果好。但同时由此带来的缺点是，一些我们不想被代理的流量也会经由代理服务器转发了。部分SSR、v2ray客户端有tun模式，也是同样的道理。

再者就是于操作系统层面配置**系统代理**，这样一来所以会遵循系统代理设置的应用都会被代理。但同时也会有其它一些不遵守系统代理的应用。举例来讲，chrome默认是走系统代理的，而firefox则需手动配置选择代理方案。如右图是Windows11的系统代理设置，红框内的文字说明了其与VPN使用虚拟网卡的方案并不重叠。

故若需要更精细化地控制系统中不同组件的代理行为，则更适合于使用**应用层的代理接口**。这种情况下，代理软件客户端做的仅仅是在本地打开一个端口用来监听和服务上层应用主动发来的代理请求，以此将是否被代理的选择权交由了每个应用本身。举例来说，v2rayN的蓝色图标对应的“清除系统代理”就是这种方案。

![Snipaste_2022-05-09_14-01-32.jpg](https://s2.loli.net/2022/05/09/HzWigwtaf5L26Sd.jpg)



### 部分网站的镜像

#### Google

- [https://www.ahhhhfs.com/4810/](https://www.ahhhhfs.com/4810/)

#### Github

- [https://www.ahhhhfs.com/18927/](https://www.ahhhhfs.com/18927/)
- [https://www.ahhhhfs.com/18876/](https://www.ahhhhfs.com/18876/)
- [https://www.ahhhhfs.com/5442/](https://www.ahhhhfs.com/5442/)

#### Youtube

- [https://www.ahhhhfs.com/16819/](https://www.ahhhhfs.com/16819/)



### 各种应用的代理配置与镜像

#### chrome

chrome 默认遵从系统代理。可是使用**Proxy SwitchyOmega**插件对chrome代理方式进行手动配置：

[https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif)

#### Linux软件源

不同Linux发行版具有不同的包管理器。即使是同一个发行版的不同版本（比如Ubuntu18.04与Ubuntu20.04），其上游软件源也是不同的。

使用国内镜像为软件源换源可以大大加速安装软件源上的软件的过程。以tuna维护的清华源为例，我们来到使用帮助页面：[https://mirrors.tuna.tsinghua.edu.cn/help](https://mirrors.tuna.tsinghua.edu.cn/help)

点击任意你想要使用清华源的服务（Linux软件源自然也涵盖在内），即能看到tuna为你提供的相应使用教程。（对于Linux软件源，需选定正确的系统版本）。

#### pip

可使用清华源：[https://mirrors.tuna.tsinghua.edu.cn/help/pypi/](https://mirrors.tuna.tsinghua.edu.cn/help/pypi/)

若使用代理方案，在`~/.config/pip/pip.conf`，中添加：（将`http://127.0.0.1:1080`替换为你本地的http代理地址）

```ini
[global]
proxy=http://127.0.0.1:1080
```

> 不支持socks5协议

#### git

（将`http://127.0.0.1:1080`替换为你本地的socks5代理地址）

```sh
git config --global http.proxy socks5://127.0.0.1:1080
```

#### apt

在 `/etc/apt/apt.conf.d/` 目录下新增 `proxy.conf` 文件，加入：（将`http://127.0.0.1:1080`替换为你本地的http代理地址）

```ini
Acquire::http::Proxy "http://127.0.0.1:1080/";
Acquire::https::Proxy "http://127.0.0.1:1080/";
```

> 不支持socks5协议

#### curl、wget

许多posix标准的命令行程序会遵从两个环境变量：

```sh
export http_proxy=http://proxyserver:port/
export https_proxy=https://proxyserver:port/
```

curl也是其中之一。

同时，也可以使用curl的`-x`、`--proxy`参数达到同样效果：

```sh
-x, --proxy <[protocol://][user:password@]proxyhost[:port]>
```

若想要curl的代理配置永久生效，向`~/.curlrc`写入：`socks5 = "127.0.0.1:1080"`。

同时，curl遵循但wget不遵循的环境变量为：

```sh
export all_proxy=http://proxyserver:port/
```



## 合法翻墙

### 澳门流量卡

就是这货👇，淘宝上就可以买到。

![9E9E47AD-ACAC-4401-81EB-2A3D29D89EAF_1_201_a.jpeg](https://s2.loli.net/2022/08/18/ScbgXVYpdRmrnyQ.jpg)



## 其他攻略

- 2022永久免费白嫖节点方法
  - [https://www.ahhhhfs.com/19188/](https://www.ahhhhfs.com/19188/)
- 使用Google搜索找机场（梯子）技巧
  - [https://www.ahhhhfs.com/10889/](https://www.ahhhhfs.com/10889/)
