---
title: "数字电路基础之 Verilog HDL"
date: 2022-06-08T22:35:54+08:00
author: "Shaka"
showToc: true
tags: ["eda"]
draft: true

---

参考资料：
1. 《电子设计基础.数字部分》
2. 《VerilogHDL程序设计与实践》
3. 语法学习：菜鸟教程 <https://www.runoob.com/w3cnote/verilog-tutorial.html>
4. 在线练习：<https://hdlbits.01xz.net/wiki/Wire>；题解：<https://zhuanlan.zhihu.com/c_1131528588117385216>
# 1. 硬件描述语言 HDL

硬件描述语言（HDL）以文本形式来描述数字系统硬件的结构和行为。

计算机对 HDL 的处理包括两个方面：逻辑仿真和逻辑综合。

逻辑仿真：用计算机仿真软件对数字逻辑电路的结构和行为进行预测，仿真器对 HDL 描述进行解释，以文本形式或时序波形图形式给出电路的输出。在电路被实现之前，设计人员对仿真结果可以初步判断电路的逻辑功能是否正确。

逻辑综合：从 HDL 描述的数字逻辑电路模型中导出电路基本元件列表以及元件之间的连接关系（常称为门级网表）的过程。它类似于高级程序设计语言中对一个程序进行编译，得到目标代码的过程。所不同的是，逻辑综合不会产生目标代码，而是产生门级元件及其连接关系的数据库，根据这个数据库可以制作出集成电路或印制电路板（PCB）。

- HDL 语言是并行处理的，具有同一时刻执行多任务的能力。这和一般高级设计语言（例如C语言等）串行执行的特征是不同的。
- HDL 有时序的概念。一般的高级编程语言是没有时序的概念的，但在硬件电路中从输入到输出总是有延时存在的，为了描述这一特征，需要引入时延的概念。

## 1.1 Verilog HDL 描述层次说明

![Verilog](https://cdn.jsdelivr.net/gh/lzxqaq/jsdelivr@master/image/2022-6-8-verilog/1.png)

### 1.1.1 系统级和算法级建模

通常只是用 Verilog HDL 描述系统的功能，不涉及具体实现的细节，实际开发中，设计人员很少应用 Verilog HDL 语言的系统和算法级建模能力。

### 1.1.2 寄存器传输级（RTL）建模

RTL 级建模：描述电路时只关注寄存器本身，以及寄存器到寄存器之间的逻辑功能，而不在意寄存器和组合逻辑的实现细节。

RTL 级描述最大的特点就在于 RTL 级描述时目前最高层次的可综合描述语言。在 EDA 工具的帮助下，设计人员可以直接在 RTL 级进行电路设计，而无需从逻辑门电路（与门、或门和非门）的较低层次来设计电路。

在最终实现时，所有的设计都需要映射到门级电路上，RTL 级代码也不例外，只不过 RTL 代码通过 EDA 软件中的逻辑综合工具转化成设计网表，网表基本上由门电路组成。

### 1.1.3 门级和开关级建模

门级建模和开关级建模都属于结构描述范畴，都是对电路结构的具体描述，分别把需要的逻辑门单元和 MOS 晶体管调出来，再用连线把这些基本单元连接起来构成电路。

这两个层次的描述类似于汇编语言和机器语言，虽然精确，但十分耗时
耗力。在大多数 Verilog HDL 程序开发中，基于这两个层次的设计方法已被彻底抛弃。

## 1.2 Verilog HDL 语言的可综合性

综合就是将 HDL 语言设计转换为由与门、或门和非门等基本逻辑单元组成的门级连接。因此，可综合语言就是能够通过 EDA 工具自动转化为硬件逻辑的语句。

任何符合 HDL 语法标准的代码都是对硬件行为的一种描述，但不一定是可直接对应成电路的设计信息。以目前大部分 EDA 软件的综合能力来说，只有 RTL 或更低层次的行为描述才能
保证是可综合的。

例如除法、求平方根、对数以及三角函数等较复杂的运算，必须通过一定的算法实现，并没有直观简单的实现电路，因而可以判断那些计算式是不能综合的，必须按其相应的算法写出更具体的代码才能实现。

## 1.3 Verilog HDL 语言的可仿真性

可仿真性和可综合性，二者的区别在于可综合语句可用于仿真，而专门面向仿真语句虽然可以描述任何电路行为，但却是不可综合的。

# 2. Verilog 语法 

## 2.1 Verilog 数值表示

整数数值表示：

数字声明时，合法的基数格式有 4 中，包括：十进制('d 或 'D)，十六进制('h 或 'H)，二进制（'b 或 'B），八进制（'o 或 'O）。数值可指明位宽，也可不指明位宽。

如  
```
4'b1011         // 4bit 数值
32'h3022_c0de   // 32bit 的数值,下划线 _ 是为了增强代码的可读性。
```

负数表示

通常在表示位宽的数字前面加一个减号来表示负数。例如：

```
-6'd15    

-15
```

## 2.2 Verilog 数据类型

Verilog 最常用的 2 种数据类型就是线网（wire）与寄存器（reg），其余类型可以理解为这两种数据类型的扩展或辅助。

### 2.2.1 线网（wire）

wire 类型表示硬件单元之间的物理连线，由其连接的器件输出端连续驱动。

### 2.2.2 寄存器（reg）

寄存器（reg）用来表示存储单元，它会保持数据原有的值，直到被改写。

`
reg    clk_temp;
reg    flag1, flag2 ;
`

### 2.2.3 向量

当位宽大于 1 时，wire 或 reg 即可声明为向量的形式。例如：

```
reg [3:0]      counter ;    //声明4bit位宽的寄存器counter
wire [32-1:0]  gpio_data;   //声明32bit位宽的线型变量gpio_data
wire [8:2]     addr ;       //声明7bit位宽的线型变量addr，位宽范围为8:2
reg [0:31]     data ;       //声明32bit位宽的寄存器变量data, 最高有效位为0
```

对于上面的向量，我们可以指定某一位或若干相邻位，作为其他逻辑使用。例如：

```
wire [9:0]     data_low = data[0:9] ;
addr_temp[3:2] = addr[8:7] + 1'b1 ;
```

## 2.3 编译指令

- \`define 宏定义， \`undef 取消宏定义
- \`include ：将另一个 Verilog 文件内嵌进来
- \`timescale ： \`timescale 编译指令将时间单位与实际时间相关联。例如
```
`timescale 1ns/100ps    //时间单位为1ns，精度为100ps，合法
//`timescale 100ps/1ns  //不合法
```

## 2.4 连续赋值

assign 全加器，用于对 wire 型变量进行赋值。
例如：
```
wire      Cout, A, B ;
assign    Cout  = A & B ;     //实现计算A与B的功能
```
- 左侧必须是一个标量或者线型向量，而不能是寄存器类型。
- 右侧的类型没有要求，可以是标量或线型或存器向量，也可以是函数调用。
- 只要 右侧 表达式的操作数有事件发生（值的变化）时，右侧 就会立刻重新计算，同时赋值给 左侧。

## 2.5 时延

连续赋值延时语句中的延时，用于控制任意操作数发生变化到语句左端赋予新值之间的时间延时。

时延一般是不可综合的。
例如
```
//普通时延，A&B计算结果延时10个时间单位赋值给Z
wire Z, A, B ;
assign #10    Z = A & B ;
```

## 2.6 过程结构

一个模块中可以包含多个 initial 和 always 语句，但 2 种语句不能嵌套使用。这些语句在模块间并行执行，与其在模块的前后顺序没有关系。但是 initial 语句或 always 语句内部可以理解为是顺序执行的（非阻塞赋值除外）。每个 initial 语句或 always 语句都会产生一个独立的控制流，执行时间都是从 0 时刻开始。

- initial语句：initial 语句从 0 时刻开始执行，只执行一次；initial 理论上来讲是不可综合的，多用于初始化、信号检测等。

- always 语句：与 initial 语句相反，always 语句是重复执行的。always 语句块从 0 时刻开始执行其中的行为语句；当执行完最后一条语句后，便再次执行语句块中的第一条语句，如此循环反复。由于循环执行的特点，always 语句多用于仿真时钟的产生，信号行为的检测等。

## 2.7 阻塞赋值、非阻塞赋值

- 阻塞赋值：顺序执行，即下一条语句执行前，当前语句一定会执行完毕。 = 
- 并行执行语句，即下一条语句的执行和当前语句的执行是同时进行的，它不会阻塞位于同一个语句块中后面语句的执行。<=

## 2.8 时序控制

### 2.8.1 边沿触发事件控制

在 Verilog 中，事件是指某一个 reg 或 wire 型变量发生了值的变化。

#### 一般事件控制

- posedge 指信号发生边沿正向跳变
- negedge 指信号发生负向边沿跳变

#### 命名事件控制

触发信号用 -> 表示。

```
实例
event     start_receiving ;
always @( posedge clk_samp) begin
        -> start_receiving ;       //采样时钟上升沿作为时间触发时刻
end
 
always @(start_receiving) begin
    data_buf = {data_if[0], data_if[1]} ; //触发时刻，对多维数据整合
end
```

#### 敏感列表

关键字 or 连接多个事件或信号。

```
//带有低有效复位端的D触发器模型
always @(posedge clk or negedge rstn)    begin      
//always @(posedge clk , negedge rstn)    begin      
//也可以使用逗号陈列多个事件触发
    if（! rstn）begin
        q <= 1'b ;      
    end
    else begin
        q <= d ;
    end
end
```

### 2.8.1 电平敏感事件控制

使用关键字 wait 来等待某个条件为真，控制后面语句的执行。
```
实例
initial begin
    wait (start_enable) ;      //等待 start 信号
    forever begin
        //start信号使能后，在clk_samp上升沿，对数据进行整合
        @(posedge clk_samp)  ;
        data_buf = {data_if[0], data_if[1]} ;      
    end
end
```