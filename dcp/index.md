# 目录

1 第一部分导读 (Introduction to The Part 1)

* 1.1 第一部分导读

* 1.2 自主学习的建议

* 1.3 动机与问题

* 1.4 总结

2 数字电路的基础元件(Basic Circuit in Digital Circuit)

* 2.1 前言

* 2.2 基础锁存器与触发器

* 2.3 逻辑表达式及其化简

* 2.4 物理电路中的数宇电路

3 数字电路的基础知识(Basic In Digital Circuit)

* 3.1 前言

* 3.2 进制符号(Symbol of Number)

* 3.3 数制(Number System)、数码 （Digit）、基数(Radix)、位权(Weight)

  * 3.3.1 位权与进制转换 (Radix Transfer)

  * 3.3.2 进制的正式定义

* 3.4 二进制数(Binary)的运算

* 3.5 二进制小数

* 3.6 有限位数 (Finite Number System)与比特(Bit)

* 3.7 位运算(Bit Operation)

* 3.8 有符号数 (Signed Number)

* 3.9 原码（Sign Magnitude）、反码（IScomplement）、补码(2's complement)

4 基础运算器(Basic Operator)

* 4.1 前言

* 4.2 加法器

  * 4.2.1 半加器(HA、Half Adder)、全加器(FA， Full Adder)与逐位进位加法器(RCA, Ripple-Carry Adder)

  * 4.2.2 超前进位加法器 (CLA, Carry Lookahead Adder)

  * 4.2.3 进位旁路加法器 (CBA, Carry Bypass Adder)

* 4.3 乘法器

  * 4.3.1 基于加法器的阵列乘法器 (Array Multiplier)

  * 4.3.2 基于进位保留加法器 (CSA, Carry Save Adder) 的阵列乘法器

  * 4.3.3 加法树，在运算器中运用结合律

* 4.4 减法器(Subtractor)与加减混合器(Adder Subtractor)

* 4.5 除法器

    * 4.5.1 恢复余数阵列除法器 (Restoring Array Divider)

    * 4.5.2不恢复余数阵列除法器 (Non-restoring Array Divider)

    * 4.5.3 除法慢于乘法

* 4.6 十进制加法器 (Decimal Adder)

    * 4.6.1 BCD码简介

    * 4.6.2 8421BCD 码加法器

* 4.7 进制转换 (Radix Transfer)

    * 4.7.1 特殊情况直接转换 (Direct Conversion in Special Case)

    * 4.7.2 位权相加法 (Weighted Sum)

    * 4.7.3 短除法 (Short Division)

    * 4.7.4 Double-Dabble 算法

* 4.8 移位器

    * 4.8.1 逻辑移位、算数移位与循环位移

    * 4.8.2 桶型移位器

5 存储器 (Memory)

* 5.1 前言

* 5.2 奇存器(Register)、寄存器组(Register set)与寻址器 (Addresser)

    * 5.2.1 寄存器 （Register）与寄存器组 (Register Set)

    * 5.2.2 译码器(Decader)

* 5.3 随机存取存储器与只读存储器

    * 5.3.1 随机存取存储器 (RAM, Random Access Memory) 与只读存储器 (ROM, Read Only Memory)的简单介绍

    * 5.3.2 大规模存储器的组织

* 5.4 FIFO与FILO

    * 5.4.1 FIFO

    * 5.4.2 FILO

* 5.5 查找表与内容可寻址单元

    * 5.5.1 查找表 (LUT, Look Up Table)

    * 5.5.2 内容可寻址单元 (CAM, Content Addressable memory)

* 5.6 带特殊功能的寄存器

    * 5.6.1 计数器 (Counter)

    * 5.6.2 移位寄存器 (Shift Register)

    * 5.6.3 线性反馈移位寄存器 (LFSR, Linear Feedback Shift Register)

6 时序电路设计

* 6.1 前言

* 6.2 时序电路基础

    * 6.2.1 时钟 （CLK, Clock）、同步 (SYNC, Synchronization)与异步 (ASYNC, Asynchronous)

    * 6.2.2 节拍发生器 (Sequence Timer)

* 6.3 串行运算器

    * 6.3.1 串行运算器的结构

    * 6.3.2 串行运算器的优势与劣势

* 6.4 有限状态机

    * 6.4.1 状态转移图(STD， State Transition Diagram)与有限状态机 (FSM, Finite State Machine)

    * 6.4.2 FSM的电路实现与应用.

* 6.5 时序电路的描述

    * 6.5.1 时序电路的状态转移图、状态转移表、时序图

    * 6.5.2 用方程描述时序电路

    * 6.5.3 时序电路的分析与优化

7 数据交换 (Data Transfer)

* 7.1前言

* 7.2 接口标准 (nterface Standard)与接口协议 (nterface Protoco)

* 7.3 交换节点

    * 7.3.1 交换节点的使用场景

    * 7.3.2 网络(Network)与图论(Graph Theory)，一些术语

    * 7.3.3 交换节点的功能与结构。

* 7.4 交换协议

    * 7.4.1 仲裁协设

8 第二部分导读

* 8.1 第二部分导读

* 8.2 计算机体系结构中的重要思想

9 认识计算机与 CPU与 RISC-V

* 9.1 前言

* 9.2 计算机的工作流程

    * 9.2.1 简化的计算机模型与工作流程

    * 9.2.2 计算机的工作流程拓展

* 9.3 RV321指令集

    * 9.3.1 RV321 的用户执行环境

    * 9.3.2 RV321 的指令格式

    * 9.3.3 RV321 指令集

    * 9.3.4 RV321 的伪指令(Pseudo Instruction)

* 9.4 认识 RV321 汇编

    * 9.4.1 汇编语言(Assembly Language)概述

    * 9.4.2 从高级语言到机器语言

* 9.5 内存中的数据

    * 9.5.1 大小端序

    * 9.5.2 数据对齐

    * 9.5.3 内存空间的划分

    * 9.5.4 内存空间的管理

10 一个简单的 RV321 处理器

* 10.1 前言

* 10.2一个单周期 RV321 处理器的内部抽象结构

    * 10.2.1 抽象的内部架构视图

    * 10.2.2 抽象的执行过程

* 10.3 CPU的更多细节

    * 10.3.1 CPU与计算机上的数据交换

    * 10.3.2 计算机总线的时序控制

    * 10.3.3 计算机中的1/0接口与外设

11 引用声明

* 11.1 图片

    * 11.1.1 普通图片

    * 11.1.2 具体专业性图片

* 11.2 表格

* 11.3 内容

12 随后编写的文章总体简介