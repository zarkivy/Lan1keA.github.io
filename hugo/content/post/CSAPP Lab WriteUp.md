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

正数则查找从左边第一个1开始，一直到最右边那一位的位数，再加上一个符号位

负数则查找从左边第一个0开始，一直到最右边那一位的位数，再加上一个符号位

```c
/* howManyBits - return the minimum number of bits required to represent x in
 *             two's complement
 *  Examples: howManyBits(12) = 5           0......0    01100
 *            howManyBits(298) = 10         0.0    0100101010
 *            howManyBits(-5) = 4           1.......1    1011
 *            howManyBits(0)  = 1           0..........0    0
 *            howManyBits(-1) = 1           1..........1    1
 *            howManyBits(0x80000000) = 32      10.........00
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 90
 *  Rating: 4
 */
int howManyBits(int x) {
	int bit16, bit8, bit4, bit2, bit1;
    // 对操作数取反，将负数转为正数，正数不变，便于更好的计算
    int sign = x >> 31;
    x ^= sign;
	// 二分查找，先判断高16位有无存在1,并将范围缩小到高16位或低16位
	// 如果高16位存在1,则bit16 == 16,否则等于0
	bit16 = (!!(x >> 16)) << 4;
	x >>= bit16;
	// 查找剩余16位中的高8位是否存在1
	bit8 = (!!(x >> 8)) << 3;
	x >>= bit8;
	// 查找剩余8位中的高4位是否存在1
	bit4 = (!!(x >> 4)) << 2;
	x >>= bit4;
	// 查找剩余4位中的高2位是否存在1
	bit2 = (!!(x >> 2)) << 1;
	x >>= bit2;
	// 查找剩余2位中的高1位是否存在1
	bit1 = (!!(x >> 1)) << 0;
	x >>= bit1;
	// 最终加上一个符号位
	return bit16 + bit8 + bit4 + bit2 + bit1 + x + 1;
}
```

## 求浮点数与2相乘的值

目前浮点数通常遵循IEEE 754标准，参见：

[https://zh.wikipedia.org/wiki/IEEE_754](https://zh.wikipedia.org/wiki/IEEE_754)

32位下，我们使用IEEE 754单精度浮点数。

![image.png](https://s2.loli.net/2022/08/26/AeWpmMJY5COnThX.png)

```c
/* 
 * floatScale2 - Return bit-level equivalent of expression 2*f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representation of
 *   single-precision floating point values.
 *   When argument is NaN, return argument
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
unsigned floatScale2(unsigned uf) {
	// 获取其exp部分的值
	int exp = (uf >> 23) & 0xff;
	int sign = uf & (1 << 31);
	// 如果传入的是非规格化的值
	if(exp == 0)
		return sign | uf << 1;
	// 如果传入的是无穷大或NaN
	else if(exp == 0xff)
		// 没法继续乘了，只能返回参数
		return uf;
	//乘2
	exp++;
	// 如果乘2后的结果超出范围（即溢出），则返回无穷大
	if(exp == 0xff)
		return sign | 0x7f800000; // expr全为0, frac全为1
	// 否则返回正常乘2后的值
	return sign | (exp << 23) | (uf & 0x7fffff);
}
```

## 浮点数转整数

```c
/* 
 * floatFloat2Int - Return bit-level equivalent of expression (int) f
 *   for floating point argument f.
 *   Argument is passed as unsigned int, but
 *   it is to be interpreted as the bit-level representation of a
 *   single-precision floating point value.
 *   Anything out of range (including NaN and infinity) should return
 *   0x80000000u.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
int floatFloat2Int(unsigned uf) {
	int exp = (uf >> 23) & 0xff;
	int sign = (uf >> 31) & 1;
	int frac = uf & 0x7fffff;
	int shiftBits = 0;
	// 0比较特殊，先判断0(正负0都算作0)
	if(!(uf & 0x7fffffff))
		return 0;
	// 判断是否为NaN还是无穷大
	if(exp == 0xff)
		return 0x80000000u;
	// 指数减去偏移量，获取到真正的指数
	exp -= 127;
	// 需要注意的是，原来的frac一旦向左移位，其值就一定会小于1，所以返回0
	if(exp < 0)
		return 0;
	// 获取M，注意exp等于-127和不等于-127的情况是不一样的。当exp != -127时还有一个隐藏的1
	if(exp != -127)
		frac |= (1 << 23);
	// 要移位的位数。注意float的小数点是点在第23位与第22位之间
	shiftBits = 23 - exp;
	// 需要注意一点，如果指数过大，则也返回0x80000000u
	if(shiftBits < 31 - 23)
		return 0x80000000u;
	// 获取真正的结果
	frac >>= shiftBits;
	// 判断符号
	if(sign == 1)
		return ~frac + 1;
	else
		return frac;
}
```

## 求2.0的x次方

```c
/* 
 * floatPower2 - Return bit-level equivalent of the expression 2.0^x
 *   (2.0 raised to the power x) for any 32-bit integer x.
 *
 *   The unsigned value that is returned should have the identical bit
 *   representation as the single-precision floating-point number 2.0^x.
 *   If the result is too small to be represented as a denorm, return
 *   0. If too large, return +INF.
 * 
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. Also if, while 
 *   Max ops: 30 
 *   Rating: 4
 */
unsigned floatPower2(int x) {
    // 得到偏移后的指数exp
	int exp = x + 127;
    // 如果exp大于等于255则为无穷大或越界
	if(exp > 0xfe)
		return 0x7f800000;
    // exp为0时，结果为0
	else if(exp < 0)
		return 0;
    //因为是求结果的浮点数比特级表示，所以偏移后的指数直接左移23bits即可
	return exp << 23;
}
```

# Bomb Lab



