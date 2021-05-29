---
title: "进程管理"
date: 2021-01-05T21:50:53+08:00
author: "罗泽勋"
draft: true

tags: [

]
categories: [
    "操作系统",
]
---

### 进程与线程
#### 1.进程
进程是资源分配的基本单位。  

进程控制块（Process Control Block,PCB)描述进程的基本信息和运行状态，所谓的创建进程和撤销进程，都是指对 PCB 的操作。  

进程可以并发执行。

#### 2.线程
线程是独立调度的基本单位。

一个进程中可以有多个线程，它们共享进程资源。

QQ 和浏览器是两个进程，浏览器进程里面有很多线程，例如 HTTP 请求线程、时间相应线程、渲染线程的呢个等，线程的并发执行使得在浏览器中点击一个新链接从而发起 HTTP 请求时，浏览器还可以相应用户的其他事件。

#### 3.区别
（1）拥有资源  
进程是资源分配的基本单位，但是线程不用有资源，线程可以访问隶属进程的资源。

（2）调度  
线程是独立调度的基本单位，在同一进程中，线程的切换不会引起进程切换，从一个进程中的线程切换到另一个进程的线程时，会引起进程的切换。

（3）系统开销  
由于创建或撤销进程时，系统都要为之分配或回收资源，如内存空间、I/O 设备等，所付出的开销远大于创建或撤销线程时的开销。类似地，在进行进程切换时，涉及当前执行进程 CPU 环境的保存及新调度进程 CPU 环境的设置，而进程切换时只需保存和设置少量寄存器内容，开销很小。  
（4）通信方面  
线程间可以通过直接读写同一进程中的数据进行通信，但是进程通信需要借助 IPC。

### 进程状态的切换
* 就绪状态（ready）：等待被调度  
* 运行状态（running）
* 阻塞状态（waiting）：等待资源

应该注意以下内容：  
* 只有就绪态和运行态可以相互转换，其它的都是单向转换。就绪状态的进程通过调度算法从而获得 CPU 时间，转为运行状态，而运行状态的进程，在分配给它的 CPU 时间片用完之后就会转为就绪状态，等待下一次调度。
* 阻塞状态是缺少需要的资源从而由运行状态转换而来，但是该资源不包括 CPU 时间，缺少 CPU 时间会从运行状态转换为就绪态。

### 进程调度算法
不同环境的调度算法目标不同，因此需要针对不同的环境来讨论调度算法。

#### 1. 批处理系统
批处理系统没有太多的用户操作，在该系统中，调度算法目标是保证吞吐量和周转时间（从提交到终止的时间）。
#### 1.1 先来先服务
#### 1.2 短作业优先
#### 1.3 最短剩余时间优先

#### 2. 交互式系统
交互式系统有大量的用户交互操作，在该系统中调度算法的目标是快速地进行响应。
#### 2.1 时间片轮转
#### 2.2 优先级调度
#### 2.3 多级反馈队列

#### 3. 实时系统
实时系统要求一个请求在一个确定的时间内得到响应。  

分为硬实时和软实时，前者必须满足绝对的截止时间，后者可以容忍一定的超时。  

### 进程同步
#### 1. 临界区
对临界资源进行访问的那段代码称为临界区。

为了互斥访问临界资源，每个进程在进入临界区之前，需要先进行检查。
```
// entry section
// critical section
// exit section
```

#### 2. 同步和互斥
* 同步：多个进程因为合作产生的直接制约关系，使得进程有一定的先后执行关系。
* 互斥：多个进程在同一时刻只有一个进程能进入临界区。

#### 3.信号量
信号量（Semaphore）是一个整型变量，可以对其执行 down 和 up 操作，也就是常见的 P 和 V 操作。
* down：如果信号量大于 0,执行 -1 操作；如果信号量等于 0,进程睡眠，等待信号量大于 0；
* up：对信号量执行 +1 操作，唤醒睡眠的进程让其完成 down 操作。

down 和 up 操作需要被设计成原语，不可分割，通常的做法是在执行这些操作的时候屏蔽中断。

如果信号量的取值只能为 0 和 1 




























