---
title: "操作系统原理 基础"
date: 2021-12-17T00:02:07+08:00
author: "Shaka"
slug: "operating-system"

tags: ["操作系统原理"]

draft: true

---

### 一、程序是如何运行的

首先，任何程序都是由人写出来的，即编程，而编程需要计算机程序设计语言作为基础。现在绝大所数情况下所用的编程语言为高级程序设计语言，如 C、C++、Java 等。但计算机并不认识高级语言编写的程序，所以需要进行编译变成计算机能够识别的机器语言程序，这需要编译器和汇编器的帮助。

其次，机器语言程序需要加载到内存，形成一个运动中的程序，即进程，而这需要从操作系统的帮助。

进程需要在计算机芯片 CPU 上执行才算是真正在执行，而将进程调度到 CPU 上运行也由操作系统完成。

最后，在 CPU 上执行的机器语言指令需要变成能够在一个个时钟脉冲里执行的基本操作，这需要指令集结构和计算机硬件的支持，并且整个程序的执行过程还需要操作系统提供的服务和程序语言提供的执行环境。

这就是一个从程序到微指令执行的简化过程。

![img](https://cdn.jsdelivr.net/gh/lzxqaq/jsdelivr@master/image/2021-12-17/operating-system-base.png)

### 二、历史

操作系统的不断发展和改善由两个因素驱动

* 硬件成本不断下降  
* 计算机的功能和复杂性不断变化  

### 三、内核态和用户态

