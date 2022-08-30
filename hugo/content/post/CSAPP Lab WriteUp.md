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

> 直接点击实验名视作以教师身份下载教学资源。题目文件应于Self-Study Handout下载

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

Bomb Lab，gdb拆弹实验。题干给定了一个炸弹程序，和它的部分源码。我们需要做的是通过动态调试，还原6个密码并正确输入，避免炸弹爆炸。否则下场就是：

```sh
▶ ./bomb
Welcome to my fiendish little bomb. You have 6 phases with
which to blow yourself up. Have a nice day!
Yaleyale! I don't give a shit!!!

BOOM!!!
The bomb has blown up.
```

好了，正儿八经来拆拆看，直接上钳子：`gdb bomb`

> 通过bomb.c可知，我们可以将找出的密码写入文件中供bomb读取，方便做题

## phase_1

炸弹1：

```sh
pwndbg> b phase_1
Breakpoint 1 at 0x400ee0
pwndbg> r
Starting program: /home/zkv/Gitrepos/A-B_Problem/csapp/BombLab/bomb
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Welcome to my fiendish little bomb. You have 6 phases with
which to blow yourself up. Have a nice day!
AAAAAAAA
```

直接在第一关下断点，随便输个字符串`AAAAAAAA`，接着向下走两步到校验的位置

```sh
   0x400ee0 <phase_1>       sub    rsp, 8
   0x400ee4 <phase_1+4>     mov    esi, 0x402400
 ► 0x400ee9 <phase_1+9>     call   strings_not_equal                      <strings_not_equal>
        rdi: 0x603780 (input_strings) ◂— 'AAAAAAAA'
        rsi: 0x402400 ◂— outsd  dx, dword ptr [rsi] /* 'Border relations with Canada have never been better.' */
        rdx: 0x1
        rcx: 0x8
```

可知第一关密码为：`Border relations with Canada have never been better.`

写入密码本：`echo "Border relations with Canada have never been better." >> passcodes`

## phase_2

用密码本直接来到第二关：`gdb --args bomb passcodes`

```sh
pwndbg> b phase_2
Breakpoint 1 at 0x400efc
pwndbg> r
Starting program: /home/zkv/Gitrepos/A-B_Problem/csapp/BombLab/bomb passcodes
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
Welcome to my fiendish little bomb. You have 6 phases with
which to blow yourself up. Have a nice day!
Phase 1 defused. How about the next one?
AAAAAAAA
```

向下跟踪到第二关的校验逻辑中。首先是从我们输入的字符串中读取6个数字：

```sh
 ► 0x40148a <read_six_numbers+46>    call   __isoc99_sscanf@plt                      <__isoc99_sscanf@plt>
        s: 0x6037d0 (input_strings+80) ◂— 'AAAAAAAA'
        format: 0x4025c3 ◂— '%d %d %d %d %d %d'
        vararg: 0x7fffffffe070 —▸ 0x7fffffffe1c8 —▸ 0x7fffffffe4d5 ◂— '/home/zkv/Gitrepos/A-B_Problem/csapp/BombLab/bomb'
```

接着回到`phase_2`函数中进行校验：

```assembly
pwndbg> disassemble
Dump of assembler code for function phase_2:
   0x0000000000400efc <+0>:     push   rbp
   0x0000000000400efd <+1>:     push   rbx
   0x0000000000400efe <+2>:     sub    rsp,0x28
   0x0000000000400f02 <+6>:     mov    rsi,rsp
   0x0000000000400f05 <+9>:     call   0x40145c <read_six_numbers>
   0x0000000000400f0a <+14>:    cmp    DWORD PTR [rsp],0x1
   0x0000000000400f0e <+18>:    je     0x400f30 <phase_2+52>
   0x0000000000400f10 <+20>:    call   0x40143a <explode_bomb>
   0x0000000000400f15 <+25>:    jmp    0x400f30 <phase_2+52>
=> 0x0000000000400f17 <+27>:    mov    eax,DWORD PTR [rbx-0x4]
   0x0000000000400f1a <+30>:    add    eax,eax
   0x0000000000400f1c <+32>:    cmp    DWORD PTR [rbx],eax
   0x0000000000400f1e <+34>:    je     0x400f25 <phase_2+41>
   0x0000000000400f20 <+36>:    call   0x40143a <explode_bomb>
   0x0000000000400f25 <+41>:    add    rbx,0x4
   0x0000000000400f29 <+45>:    cmp    rbx,rbp
   0x0000000000400f2c <+48>:    jne    0x400f17 <phase_2+27>
   0x0000000000400f2e <+50>:    jmp    0x400f3c <phase_2+64>
   0x0000000000400f30 <+52>:    lea    rbx,[rsp+0x4]
   0x0000000000400f35 <+57>:    lea    rbp,[rsp+0x18]
   0x0000000000400f3a <+62>:    jmp    0x400f17 <phase_2+27>
   0x0000000000400f3c <+64>:    add    rsp,0x28
   0x0000000000400f40 <+68>:    pop    rbx
   0x0000000000400f41 <+69>:    pop    rbp
   0x0000000000400f42 <+70>:    ret
```

`<+27>`到`<+41>`之间为循环校验的逻辑。具体是比较`DWORD PTR [rbx]`是否等于`(DWORD PTR [rbx-0x4]) * 2`。而每一轮循环中`DWORD PTR [rbx]`存放的就是我们输入的数字。比如第一轮循环：

```sh
pwndbg> x $rbx-4
0x7fffffffe070: 0x00000001
```

那第二轮循环就应该：

```sh
pwndbg> x $rbx-4
0x7fffffffe070: 0x00000002
```

所以我们输入：`1 2 4 8 16 32`即可通关

`echo "1 2 4 8 16 32" >> passcodes`

## phase_3

用密码本直接来到第三关：`gdb -ex "b phase_3" -ex "r" --args bomb passcodes`

反汇编看看：

```assembly
pwndbg> disassemble phase_3
Dump of assembler code for function phase_3:
=> 0x0000000000400f43 <+0>:     sub    rsp,0x18
   0x0000000000400f47 <+4>:     lea    rcx,[rsp+0xc]
   0x0000000000400f4c <+9>:     lea    rdx,[rsp+0x8]
   0x0000000000400f51 <+14>:    mov    esi,0x4025cf
   0x0000000000400f56 <+19>:    mov    eax,0x0
   0x0000000000400f5b <+24>:    call   0x400bf0 <__isoc99_sscanf@plt>
   0x0000000000400f60 <+29>:    cmp    eax,0x1
   0x0000000000400f63 <+32>:    jg     0x400f6a <phase_3+39>
   0x0000000000400f65 <+34>:    call   0x40143a <explode_bomb>
   0x0000000000400f6a <+39>:    cmp    DWORD PTR [rsp+0x8],0x7
   0x0000000000400f6f <+44>:    ja     0x400fad <phase_3+106>
   0x0000000000400f71 <+46>:    mov    eax,DWORD PTR [rsp+0x8]
   0x0000000000400f75 <+50>:    jmp    QWORD PTR [rax*8+0x402470]
   0x0000000000400f7c <+57>:    mov    eax,0xcf
   0x0000000000400f81 <+62>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400f83 <+64>:    mov    eax,0x2c3
   0x0000000000400f88 <+69>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400f8a <+71>:    mov    eax,0x100
   0x0000000000400f8f <+76>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400f91 <+78>:    mov    eax,0x185
   0x0000000000400f96 <+83>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400f98 <+85>:    mov    eax,0xce
   0x0000000000400f9d <+90>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400f9f <+92>:    mov    eax,0x2aa
   0x0000000000400fa4 <+97>:    jmp    0x400fbe <phase_3+123>
   0x0000000000400fa6 <+99>:    mov    eax,0x147
   0x0000000000400fab <+104>:   jmp    0x400fbe <phase_3+123>
   0x0000000000400fad <+106>:   call   0x40143a <explode_bomb>
   0x0000000000400fb2 <+111>:   mov    eax,0x0
   0x0000000000400fb7 <+116>:   jmp    0x400fbe <phase_3+123>
   0x0000000000400fb9 <+118>:   mov    eax,0x137
   0x0000000000400fbe <+123>:   cmp    eax,DWORD PTR [rsp+0xc]
   0x0000000000400fc2 <+127>:   je     0x400fc9 <phase_3+134>
   0x0000000000400fc4 <+129>:   call   0x40143a <explode_bomb>
   0x0000000000400fc9 <+134>:   add    rsp,0x18
   0x0000000000400fcd <+138>:   ret
```

阅读反汇编代码可知，此关需输入两个数字。第一个数字需小于7，第二个数字的值由输入的第一个数字推导得出（`switch case`结构）。第一个数字输入为`1`，来到：

```
   0x400f71 <phase_3+46>     mov    eax, dword ptr [rsp + 8]
   0x400f75 <phase_3+50>     jmp    qword ptr [rax*8 + 0x402470]
    ↓
 ► 0x400fb9 <phase_3+118>    mov    eax, 0x137
   0x400fbe <phase_3+123>    cmp    eax, dword ptr [rsp + 0xc]
   0x400fc2 <phase_3+127>    je     phase_3+134                      <phase_3+134>
```

得一个合法的解是：`1 311`

`echo "1 311" >> passcodes`

## phase_4

先来看看`phase_4`函数的反汇编：

```assembly
0x000000000040100c <+0>:     sub    rsp,0x18
0x0000000000401010 <+4>:     lea    rcx,[rsp+0xc]
0x0000000000401015 <+9>:     lea    rdx,[rsp+0x8]
0x000000000040101a <+14>:    mov    esi,0x4025cf
0x000000000040101f <+19>:    mov    eax,0x0
0x0000000000401024 <+24>:    call   0x400bf0 <__isoc99_sscanf@plt>
0x0000000000401029 <+29>:    cmp    eax,0x2
0x000000000040102c <+32>:    jne    0x401035 <phase_4+41>
0x000000000040102e <+34>:    cmp    DWORD PTR [rsp+0x8],0xe
0x0000000000401033 <+39>:    jbe    0x40103a <phase_4+46>
0x0000000000401035 <+41>:    call   0x40143a <explode_bomb>
0x000000000040103a <+46>:    mov    edx,0xe
0x000000000040103f <+51>:    mov    esi,0x0
0x0000000000401044 <+56>:    mov    edi,DWORD PTR [rsp+0x8]
0x0000000000401048 <+60>:    call   0x400fce <func4>
0x000000000040104d <+65>:    test   eax,eax
0x000000000040104f <+67>:    jne    0x401058 <phase_4+76>
0x0000000000401051 <+69>:    cmp    DWORD PTR [rsp+0xc],0x0
0x0000000000401056 <+74>:    je     0x40105d <phase_4+81>
0x0000000000401058 <+76>:    call   0x40143a <explode_bomb>
0x000000000040105d <+81>:    add    rsp,0x18
0x0000000000401061 <+85>:    ret
```

可知这一关也是让我们输入两个数字`a b`，且要满足如下条件：

- `(unsigned)a < 14`
- `b == 0`
- `func4(a, 0, 14) == 0`

`func4`反汇编如下：

```assembly
0x0000000000400fce <+0>:     sub    rsp,0x8
0x0000000000400fd2 <+4>:     mov    eax,edx
0x0000000000400fd4 <+6>:     sub    eax,esi
0x0000000000400fd6 <+8>:     mov    ecx,eax
0x0000000000400fd8 <+10>:    shr    ecx,0x1f
0x0000000000400fdb <+13>:    add    eax,ecx
0x0000000000400fdd <+15>:    sar    eax,1
0x0000000000400fdf <+17>:    lea    ecx,[rax+rsi*1]
0x0000000000400fe2 <+20>:    cmp    ecx,edi
0x0000000000400fe4 <+22>:    jle    0x400ff2 <func4+36>
0x0000000000400fe6 <+24>:    lea    edx,[rcx-0x1]
0x0000000000400fe9 <+27>:    call   0x400fce <func4>
0x0000000000400fee <+32>:    add    eax,eax
0x0000000000400ff0 <+34>:    jmp    0x401007 <func4+57>
0x0000000000400ff2 <+36>:    mov    eax,0x0
0x0000000000400ff7 <+41>:    cmp    ecx,edi
0x0000000000400ff9 <+43>:    jge    0x401007 <func4+57>
0x0000000000400ffb <+45>:    lea    esi,[rcx+0x1]
0x0000000000400ffe <+48>:    call   0x400fce <func4>
0x0000000000401003 <+53>:    lea    eax,[rax+rax*1+0x1]
0x0000000000401007 <+57>:    add    rsp,0x8
0x000000000040100b <+61>:    ret
```

`func4`是一个递归函数，IDA反编译得，仅供参考：

```c
__int64 __fastcall func4(__int64 a1, __int64 a2, int a3)
{
  int v3; // ecx
  __int64 result; // rax

  v3 = (a3 - (int)a2) / 2 + a2;
  if ( v3 > (int)a1 )
    return 2 * (unsigned int)func4(a1, a2);
  result = 0LL;
  if ( v3 < (int)a1 )
    return 2 * (unsigned int)func4(a1, (unsigned int)(v3 + 1)) + 1;
  return result;
}
```

`func4`汇编函数重写后，编写汇编程序对此函数暴力枚举：

```assembly
global  _start

section .rodata
    s_msg: db "satisfy!!", 10
    d_msg: db "failed!!!", 10

section .text

func4:
    sub     rsp,0x8
    mov     eax,edx
    sub     eax,esi
    mov     ecx,eax
    shr     ecx,0x1f
    add     eax,ecx
    sar     eax,1
    lea     ecx,[rax+rsi*1]
    cmp     ecx,edi
    jle     p36
    lea     edx,[rcx-0x1]
    call    func4
    add     eax,eax
    jmp     p57
p36:
    mov     eax,0x0
    cmp     ecx,edi
    jge     p57
    lea     esi,[rcx+0x1]
    call    func4
    lea     eax,[rax+rax*1+0x1]
p57:
    add     rsp,0x8
    ret

_start:
    mov     r12, -1
loop:
    add     r12, 1
    cmp     r12, 10
    je      exit
    mov     rdi, r12
    mov     rsi, 0
    mov     rdx, 14
    call    func4
    test    eax, eax
    jne     dissatisfy
    mov     rsi, s_msg
    jmp     print
dissatisfy:
    mov     rsi, d_msg
print:
    mov     rax, 1
    mov     rdi, 1
    mov     rdx, 10
    syscall
    jmp     loop
exit:
    mov     rax, 60
    mov     rdi, 0
    syscall
```

汇编并运行：

```sh
nasm -f elf64 -o phase_4.o phase_4.S
ld -o phase_4 phase_4.o
./phase_4
```

由输出可得，0～9中满足条件的解为`0、1、3、7`

故此关的一个合法解为`7 0`

`echo "7 0" >> passcodes`

## phase_5

## phase_6

## secret_phase

# Attack Lab

# Buffer Lab

# Architecture Lab

# Cache Lab

# Performance Lab

# Shell Lab

# Malloc Lab

# Proxy Lab
