---
title: "ARM简史"
description: "劣质史学家的笔记草稿"
date: 2022-06-01T10:55:17+08:00
categories: "盗版简史"
tags: [ "Architecture" ]
image: "https://s2.loli.net/2022/06/01/YZMWEo7K6iF1aOt.jpg"
---



**ARM** (stylised in lowercase as **arm**, formerly an acronym for **Advanced RISC Machines** and originally **Acorn RISC Machine**) is a family of [reduced instruction set computer](https://en.wikipedia.org/wiki/Reduced_instruction_set_computer) (RISC) [instruction set architectures](https://en.wikipedia.org/wiki/Instruction_set_architecture) for [computer processors](https://en.wikipedia.org/wiki/Central_processing_unit), configured for various environments.

# AArch32

**armel** : arm eabi little-endian, use soft float

**armhf** : arm hard float, arm with **FPU**

# From AArch32 to AArch64

Transitioning rom ARMv7 to ARMv8, all the basics you must know: 

- https://events.static.linuxfound.org/sites/events/files/slides/KoreaLinuxForum-2014.pdf

Can legacy 32 bit app. (ARMv7 or earlier) run as is on the ARMv8 OS?

- https://stackoverflow.com/questions/22460589/armv8-running-legacy-32-bit-applications-on-64-bit-os
- https://github.com/torvalds/linux/blob/v4.17/arch/arm64/Kconfig#L1274



# Manual

- [https://developer.arm.com/documentation/dui0068/b/ARM-Instruction-Reference](https://developer.arm.com/documentation/dui0068/b/ARM-Instruction-Reference)



# Reference

- https://en.wikipedia.org/wiki/ARM_architecture_family
- [https://www.ruanyifeng.com/blog/2011/01/brief_history_of_arm.html](https://www.ruanyifeng.com/blog/2011/01/brief_history_of_arm.html)

