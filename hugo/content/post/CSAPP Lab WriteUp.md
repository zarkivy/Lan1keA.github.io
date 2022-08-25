---
title: "CSAPP Lab WriteUp"
description: "http://csapp.cs.cmu.edu/3e/labs.html"
date: 2022-08-19T17:06:58+08:00
categories: "家庭作业"
tags: [ "System" ]
image: "https://s2.loli.net/2022/08/24/HhMzuB9lRFsCvy2.png"
---



# 0x0

实验源：[http://csapp.cs.cmu.edu/3e/labs.html](http://csapp.cs.cmu.edu/3e/labs.html)

> 直接点击实验名视作是图以教师身份下载教学资源。题目文件应于Self-Study Handout下载

# Data Lab

下载解压题目文件：

```sh
wget http://csapp.cs.cmu.edu/3e/datalab-handout.tar
tar xvf datalab-handout.tar
```

题目说明：

[http://csapp.cs.cmu.edu/3e/README-datalab](http://csapp.cs.cmu.edu/3e/README-datalab)

我们的任务是在受限使用运算符与控制流的前提下，实现bit.c中的每个函数的功能。

## 实现异或运算

异或，即 “不同时为0” 且 “不同时为1”

`^`，即 `~(~x&~y)` 且 `~(x&y)`

```c
//1
/* 
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
	return ~(~x&~y)&~(x&y);
}
```

## 求32位下最小的补码数值

补码，二补数，2's complement，参见：

[https://zh.wikipedia.org/zh-cn/%E4%BA%8C%E8%A3%9C%E6%95%B8](https://zh.wikipedia.org/zh-cn/%E4%BA%8C%E8%A3%9C%E6%95%B8)

32位下，最小的补码数值为`1000000000000000000000000000000`

补码`10000000`为什么可以表示-128？而非0、128？参见：

[https://www.zhihu.com/question/28685048](https://www.zhihu.com/question/28685048)

```c
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {
	return 1 << 31;
}
```

## 判断输入是否是补码中的最大整数

32位下，补码中的最大整数是`0x7fffffff`，接下来省略一些，只写8bits，下文同理。

核心手段是造出`00000000`，利用0与非0值返回true与false：

若 `x = 0111111`

`x + 1 + x = 11111111`

`~(x + 1 + x) = 00000000 = 0`

但同时需要排除 `x = 11111111` 的情况，因为此时 `x + 1 + x` 溢出后也得到 `11111111`

故借助：

`x = 11111111` 时 `x + !(x + 1) = 0`

`x = 01111111` 时 `x + !(x + 1) = 01111111 = !0`

来排除：

```c
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
	return !(~(x + 1 + x)) & (x + !(x + 1));
}
```

## 判断所有奇数位是否都为1

位的编号从0（最低有效）到31（最高有效）

采用掩码，即当 `x | 0x55555555 == 0xffffffff` 时则返回1

`0x55 == 0b 0101 0101`

```c
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
int allOddBits(int x) {
	int a = (0x55 << 0) | (0x55 << 8) | (0x55 << 16) | (0x55 << 24);
	return !~(x | a);
}
```

## 求 -x

按位取反加一即得负数

补码实际上是一个`阿贝尔群`，对于 `x`，`-x` 是其补码，所以 `-x` 可以通过对 `x` 取反加1得到

```c
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
	return ~x + 1;
}
```

## 判断是否为数字字符ASCII码

用减法，判断 `x-0x30 >= 0 && 0x39 - x >= 0`

但题干要求不能用减号，那么可以根据补码原理用加号实现减法

最后判断两个中间值的符号位全为0即可返回true

```c
/* 
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x) {
	int a = x + (~0x30 + 1); // x - 0x30
	int b = 0x39 + (~x + 1); // 0x39 - x
	return !(a >> 31 | b >> 31);
}
```

## 实现C语言 ? : 运算符

将x转化为 `00000000` 或 `11111111` 用于计算

```c
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
	x = !!x;
	x = ~x+1;
	return (x&y)|(~x&z);
}
```

## 实现 <= 运算符

> <<左移一律补0。>>右移可能补0也可能补符号位，视机器而定，通常是补符号位。

通过位运算实现比较两个数的大小，两种情况：一是符号不同正数为大，二是符号相同看差值符号

```c
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
	int v = y + (~x+1);
    int vSignBit = (v >> 31) & 1;
    int xSign = x & (1<<31);
    int ySign = y & (1<<31);
    int signXor = (xSign ^ ySing) >> 31 & 1;
    return ((!signXor) & (!vSignBit)) | ((signXor) & (xSign>>31));
}
```

## 实现逻辑非!

`00000000 ｜ (~00000000 + 1)` 得 0，其余数做此运算都得非0，且符号位为1

```c
/* 
 * logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int logicalNeg(int x) {
	return ((x | (~x + 1)) >> 31) + 1
}
```

## 求一个数用补码表示所需的最少位数

## 求浮点数与2相乘的值

## 浮点数转整数

## 判断指数是否上溢或者下溢
