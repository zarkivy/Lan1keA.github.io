---
title: "ZKV的攒机简史"
description: "年轻人的第一台PC"
date: 2022-06-16T18:42:34+08:00
tags: [ "Life" ]
imagelink: "https://s2.loli.net/2022/06/16/EYHcq8joTrfdiBR.jpg"
---



# 前传

不同寻常的是，我是在将各种设备都玩过一轮后，最后才将目光放到了台式机上。想来原因应当在于我是一个无可救药的**MINI教**信徒——iPhone用mini；iPad用mini（曾经，奈何M1太香了）；Macbook用~~mini~~Air；台式机用NUC+电脑棒+单片机……

好吧，其实我也不能说没有台式机，毕竟NUC也算得上。但在把各类小型设备都折腾过一遍后，我陷入了茫然四顾无所寄托的悲惨境地。突然想起自己这几年来竟未尝进入DIY PC的海洋试试水。又瞥了瞥手上这台NUC11PAHi5，估摸着这孩子也是天资平平、前途黯淡，竭尽全力地运行OW1却只是凑活能玩，没有能带起OW2的天赋。恰逢本世代全球大萧条之始，加密货币市场一泻千里，难窥其底。显卡价格也是应声而落，众望所归。天时地利人和——这钱，我不得不花。毕竟天意不可违。

但对于村儿里长大的我来说，掏出去的钱倘若没能换来足量的快乐，乃至买来的是糟心，实在是大逆不道有违祖训的事情。其负罪感甚至要甚于倒掉没吃完的肉（或许正因如此我才有幸成为闲鱼大V，焉知非福啊）。所以，事前进行全方位的考察与调研是十分必要的事情。



# 设备情报

## 通用资源

- [https://www.zhihu.com/special/1436041011885293568?tab=1436061145295228928](https://www.zhihu.com/special/1436041011885293568?tab=1436061145295228928)
- 一些排行榜和天梯图：[https://rank.kkj.cn/dgpu3.shtml](https://rank.kkj.cn/dgpu3.shtml)

## CPU

### 通用资源

可以于这些网站查询到绝大多数CPU的benchmark：

- [https://www.cpubenchmark.net/](https://www.cpubenchmark.net/)
- [https://browser.geekbench.com/processor-benchmarks](https://browser.geekbench.com/processor-benchmarks)

天梯图：[https://www.mydrivers.com/zhuanti/tianti/cpu/index.html](https://www.mydrivers.com/zhuanti/tianti/cpu/index.html)

### Intel

#### 命名规则

![image.png](https://s2.loli.net/2022/06/20/9ITSVKgfYQq1lje.png)

#### 酷睿后缀名

| **后缀** |                     **含义**                     |
| :------: | :----------------------------------------------: |
|  G1-G7   | 显卡级别（仅适用于采用全新集成显卡技术的处理器） |
|    E     |                      嵌入式                      |
|    F     |                   需要独立显卡                   |
|    G     |               封装包中包含独立显卡               |
|    H     |           针对移动设备进行优化的高性能           |
|    HK    |       针对移动设备进行优化的高性能，未锁频       |
|    HQ    |        针对移动设备进行优化的高性能，四核        |
|    K     |                      未锁频                      |
|    S     |                      特别版                      |
|    T     |              经过功耗优化的生活方式              |
|    U     |                  高能效移动设备                  |
|    Y     |                极低功耗的移动设备                |
|   X/XE   |                   未锁频，高端                   |
|    B     |                  球栅阵列 (BGA)                  |

reference：[https://www.intel.com/content/www/us/en/processors/processor-numbers.html](https://www.intel.com/content/www/us/en/processors/processor-numbers.html)

### AMD

#### 命名规则

![image.png](https://s2.loli.net/2022/06/20/bUcNP7BJ9GCdrD4.png)

#### 锐龙后缀名

- X后缀
    - AMD Ryzen X系列跟Intel的K系列类似，都是强化了超频特性。

- K后缀
    - K代表解锁倍频，可以超频，如AMD Athlon X4 860K和AMD A10-7870K。
- G
- E后缀
    - E后缀特指FX系列CPU的节能版，如FX 8370E的频率降低到3.3-4.3GHz，功耗降低到95W（FX8370的规格为4.0-4.3GHz，125W）。
- B后缀
    - B后缀特指APU的低功耗商务版本，比如A10 PRO-7850B，下限能耗少10W，GPU频率降低。
- M后缀
    - M系列特指APU的移动版。 



## 显卡

天梯图：[https://www.mydrivers.com/zhuanti/tianti/gpu/index.html](https://www.mydrivers.com/zhuanti/tianti/gpu/index.html)

## 硬盘

### 固态硬盘

**固态硬盘**使用**NAND闪存颗粒**作为存储的主体，采用主控+多颗闪存芯片的结构。

#### NAND闪存颗粒

主要分为**SLC、MLC、TLC、QLC**四种。寿命和价格都依次递减。

![nand](https://media.kingston.com/kingston/content/ktc-content-solutions-pc-performance-difference-between-slc-mlc-tlc-3d-nand-infographic-cn.jpg)

reference：[https://www.kingston.com/cn/blog/pc-performance/difference-between-slc-mlc-tlc-3d-nand](https://www.kingston.com/cn/blog/pc-performance/difference-between-slc-mlc-tlc-3d-nand)

#### 接口与协议

- 物理接口
    - M.2
    - U.2
    - AIC
    - NGFF
- 高速信号协议
    - SATA
        - Serial Advanced Technology Attachment
    - PCIe
        - Peripheral Component Interconnect Express
    - SAS
        - Serial Attached SCSI
- 传输层协议
    - NVMe
        - 专为固态硬盘设计的协议，为NAND做了优化
    - SCSI
    - ATA
        - 仅与SATA配合

reference：

- 了解 SSD 技术：NVMe、SATA、M.2：[https://www.kingston.com/cn/community/articledetail/articleid/48543](https://www.kingston.com/cn/community/articledetail/articleid/48543)
- 固态硬盘外形尺寸类型：[https://www.kingston.com/cn/blog/pc-performance/ssd-form-factors](https://www.kingston.com/cn/blog/pc-performance/ssd-form-factors)
- M.2 固态硬盘的两种类型：SATA 和 NVMe：[https://www.kingston.com/cn/blog/pc-performance/two-types-m2-vs-ssd](https://www.kingston.com/cn/blog/pc-performance/two-types-m2-vs-ssd)

### 机械硬盘



## 主板

先来了解下PCIe。知乎老狼的文章都写的很好哇，推荐他的UEFI与BIOS探秘。

- 深入PCI与PCIe之一：硬件篇：[https://zhuanlan.zhihu.com/p/26172972](https://zhuanlan.zhihu.com/p/26172972)
- 深入PCI与PCIe之二：软件篇：[https://zhuanlan.zhihu.com/p/26244141](https://zhuanlan.zhihu.com/p/26244141)
- PCIe中断机制(1):演变历史：[https://zhuanlan.zhihu.com/p/399239928](https://zhuanlan.zhihu.com/p/399239928)

### 大小分类

- 大板：ATX、Mini ATX、XL-ATX、非标准E-ATX
- 小板：MATX、μATX、FlexATX
- 迷你板：ITX（包括Thin-ITX）
- 超大板：标准E-ATX、EE-ATX、HPTX、WTX

华硕主板产品线：

- 頂尖旗艦 ROG
- 遊戲主流 STRIX
- 入門電競 TUF GAMING 與 PRIME 系列

reference：

- [https://www.zhihu.com/question/351825113/answer/1041320836](https://www.zhihu.com/question/351825113/answer/1041320836)
- [https://zhuanlan.zhihu.com/p/53379889](https://zhuanlan.zhihu.com/p/53379889)



## 内存

### 硬件形态

在80286时代，内存颗粒（Chip）是直接插在主板上的，叫做DIP(Dual In-line Package)。到了80386时代，换成1片焊有内存颗粒的电路板，叫做SIMM（Single-Inline Memory Module）。由阵脚形态变化成电路板带来了很多好处：模块化，安装便利等等，由此DIY市场才有可能产生。当时SIMM的位宽是32bit，即一个周期读取4个字节，到了奔腾时，位宽变为64bit，即8个字节，于是SIMM就顺势变为DIMM（Double-Inline Memory Module）。这种形态一直延续至今，也是内存条的基本形态。现在DIMM分为很多种：

- **RDIMM**: 全称（Registered DIMM），寄存型模组，主要用在服务器上，为了增加内存的容量和稳定性分有ECC和无ECC两种，但市场上几乎都是ECC的。
- **UDIMM**：全称（Unbuffered DIMM），无缓冲型模组，这是我们平时所用到的标准台式电脑DIMM，分有ECC和无ECC两种，一般是无ECC的。
- **SO-DIMM**：全称（Small Outline DIMM），小外型DIMM，笔记本电脑中所使用的DIMM，分ECC和无ECC两种。
- **Mini-DIMM**：DDR2时代新出现的模组类型，它是Registered DIMM的缩小版本，用于刀片式服务器等对体积要求苛刻的高端领域。

![Overals_660.png](https://s2.loli.net/2022/06/17/tzRLTHQ6rAVCdI4.png)

### DDR

**DDR SDRAM**: Double Data Rate Synchronous Dynamic Random Access Memory

![image.png](https://s2.loli.net/2022/06/17/bWU14gfY3HwxLKV.png)

reference：

- https://zhuanlan.zhihu.com/p/26255460
- [https://en.wikipedia.org/wiki/DDR_SDRAM](https://en.wikipedia.org/wiki/DDR_SDRAM)



## 机箱

- MINI机箱
    - 安装ITX和M-ATX的机箱
- 中塔机箱
    - ATX主板的机箱
- 全塔机箱
    - 为了E-ATX板而设计的

reference：

- [https://zhuanlan.zhihu.com/p/25592446](https://zhuanlan.zhihu.com/p/25592446)
- [https://www.zhihu.com/question/432376475](https://www.zhihu.com/question/432376475)
- [https://zhuanlan.zhihu.com/p/373132410](https://zhuanlan.zhihu.com/p/373132410)

## 电源



## 显示器

- 亮度：越高越好，300 cd/m² 中等，400 cd/m² 不错， 500 cd/m² 优秀
- 对比度：越高越好，700~900:1 及格，900~ 1100 中等，1100~1300不错，1500 以上很好。
- 色域：sRGB ＜ P3＜ AdobeRGB, 覆盖越多颜色越艳，覆盖越精准越好，但覆盖越多不一定越好。
- 色深，6 bit 底线，有明显层次感， 中端表现不错，10bit 表现很好但需要整套设备支持。
- 色准：ΔE＜1.5 就能适用于严谨的工作，ΔE＜3 一般人看不出区别，ΔE 在 5 以上色偏严重。
- 刷新率：越高越好，打游戏 120Hz 就能有很大提升，120Hz 之后区别并不大（除非你是职业选手）
- 响应时间：越快越好，3ms 以内顶级，5ms 以内优秀，10ms 以内中等，10ms 以上完全不适合游戏

reference：[https://www.zhihu.com/question/35668312](https://www.zhihu.com/question/35668312)





# 终局

- 总价
    - ¥ 4499
- CPU
    - AMD 锐龙 R5-5600X 盒装
    - ¥ 940（全新）
    - [https://www.amd.com/en/products/cpu/amd-ryzen-5-5600x](https://www.amd.com/en/products/cpu/amd-ryzen-5-5600x)
- 主板
    - 华硕 TUF GAMING B550M-PLUS WIFI
    - ¥ 600（全新）
    - [https://www.asus.com.cn/Motherboards-Components/Motherboards/TUF-Gaming/TUF-GAMING-B550M-PLUS-WI-FI/](https://www.asus.com.cn/Motherboards-Components/Motherboards/TUF-Gaming/TUF-GAMING-B550M-PLUS-WI-FI/)
- 机箱
    - 乔思伯 D30 M-ATX 银色
    - ¥ 399（全新）
    - [https://www.jonsbo.com/products/D30.html](https://www.jonsbo.com/products/D30.html)
- 显卡
    - 迪兰 RX5500XT 8G X战将
    - ¥ 925（来自同事的二手）
    - [http://www.dataland.com.cn/prod_view.aspx?nid=3&typeid=129&id=928](http://www.dataland.com.cn/prod_view.aspx?nid=3&typeid=129&id=928)
- SSD
    - 英睿达 镁光 P5 1TB
    - ¥ 500（NUC替换下来的）
    - [https://www.crucial.com/products/ssd/crucial-p5-ssd](https://www.crucial.com/products/ssd/crucial-p5-ssd)
- HDD
    - 日立 5400转 2.5英寸 7mm 32MB PMR 1TB
    - ¥ 275（全新）
    - [https://m.tb.cn/h.fwlHZFV?tk=4Fdi2MtXCRw](https://m.tb.cn/h.fwlHZFV?tk=4Fdi2MtXCRw)
- 电源
    - 振华 冰山金蝶 550W
    - ¥ 262（闲鱼二手在保）
- 散热
    - 利民 AS120PLUS
    - ¥ 139（全新）
    - [https://item.jd.com/100006880794.html](https://item.jd.com/100006880794.html)
    - ID-COLLING XF-12025-SD-K ×3
    - ¥ 60（全新）
    - [https://item.jd.com/100005667115.html](https://item.jd.com/100005667115.html)
- 内存
    - 金士顿 FURY Beast 8GB×2 DDR4 3200MHz
    - ¥ 399（全新）
    - [https://www.kingston.com/cn/memory/gaming/kingston-fury-beast-ddr4-rgb-memory](https://www.kingston.com/cn/memory/gaming/kingston-fury-beast-ddr4-rgb-memory)



reference：[https://zhuanlan.zhihu.com/p/282821640](https://zhuanlan.zhihu.com/p/282821640)
