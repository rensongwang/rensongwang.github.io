---
title: CSAPP：程序的机器级表示
categories:
- CSAPP
date: 2019-04-03 09:18:35
tags:
- 深入理解计算机系统
---
{% note default %}
当我们用高级语言编程时，机器屏蔽了程序的细节，即机器级的实现。越到后面其实会发现，你的基础还是一点都不牢固，很多底层实现的原理根本就不了解，每当我去探索程序的机器级表示的时候，都会有一种豁然开朗的感觉。
{% endnote %}
---
<!-- more -->

首先，我们应该要清楚，计算机在底层实际上处理不了人类的高级语言，只能将我们的程序一步一步的变成二进制指令，也就是字节序列。

### 机器级代码
前面讲到过，计算机系统使用了多种不同形式的抽象，利用抽象模型来简化细节。对于机器级编程，两种抽象。
1. **指令集体系架构（ISA）**来定义机器级程序的格式和行为。
2. 机器级程序使用的内存地址是虚拟地址，提供的内存模型看上去是一个非常大的字节数组。后面会讲到，存储器系统的实际实现是将多个硬件存储器和操作系统软件组合起来。操作系统负责管理虚拟地址空间，将虚拟地址翻译成实际处理器内存中的物理地址。

### 关于格式的注解
所有以‘.’开头的都是指导汇编器和链接器工作的指令

### 数据格式
Intel用术语“字（word）”表示16位数据类型。32位称为“双字”。64位：四字。

- P121的操作数格式表很重要。根据描述，我们要搞清楚的是，如果是单纯的寄存器%rax作为操作数的话，要操作的是里面的值。而**内存引用比如（%rax）则是根据寄存器的值（这个值是一个地址）访问某个内存位置**因为将内存看成一个很大的字节数组，我们用Mb[Addr]表示<font color = red>对存储在内存中从Addr开始的b个字节值的引用。</font>
- 传送指令的两个操作数不能都是内存地址
- P123 MOVZ:零扩展数据传送，MOVS：符号扩展
- C语言中所谓的“指针”其实就是地址。间接引用指针就是将指针放到一个寄存器中，然后再内存引用这个寄存器。
- 局部变量通常是保存在寄存器中，因为访问寄存器比访问内存要快得多。
- **当执行强制类型转换既涉及到大小变化又涉及到符号变化时，操作应该先改变大小**

#### 压入和弹出程序栈
程序栈是很重要的，在x86-64中，程序栈存放在内存中某个区域。<font color="red">**栈是向下增长**</font>，也就是说栈顶元素的地址是所有栈中元素地址中最低的。<font color="red">栈指针%rsp保存着栈顶元素的地址</font>
具体：P127
- 加载有效地址指令leaq实际上是movq的变形，从内存读数据到寄存器，但实际上根本就没有引用内存。第一个操作数看上去是一个内存引用，但该指令并不是从指定的位置读入数据，而是将有效地址写入到目的操作数。P129
- 移位操作P131
- x86-64指令集对128位数的操作提供有限的支持P133

### 控制
#### 跳转指令是如何编码的
<font color=red>跳转指令有不同的编码，最常用的是PC相对的，还有一种就是给出绝对地址。实际上，大多用的都是PC相对的，跳转指令后面跟的并不是要跳转的目的地址，也就是说不是绝对的地址，而是将目标指令的地址与紧跟在跳转指令后面的那条指令的地址之间的差作为编码</font>

- 用条件控制来实现条件分支P143
- 用条件传送来实现条件分支P145
- 循环P149

#### switch语句是怎么实现的
**通过跳转表来实现，跳转表是一个数组，表项i是一个代码段的地址，这个代码段实现当开关索引值等于i是程序应该采取的动作。程序代码用开关索引值来执行一个跳转表内的数组引用，确定跳转指令的目标**。P161

### 运行时栈
过程是软件中一种很重要的抽象。它提供了一种封装代码的方式，用一组指定的参数和一个可选的返回值实现了某种功能。
假设过程P调用过程Q，Q执行后返回到P。这些动作包括：
1. 传递参数
2. 传递数据
3. 分配和释放内存

C语言过程调用机制的一个关键特性在于使用了栈数据结构提供的后进先出的内存管理原则。P164
很重要的一点就是通过栈可以保存**运行信息**，也就是说如果我要中途调用另一个过程，我先把我要运行的下一条指令（也就是执行跳转指令后的一条指令）给push到栈中。在返回时，再pop。这是很关键的处理调用的一个体现。
- 当然除了压入返回地址，还有局部变量，被保存的寄存器和参数构造区。
- 前面讲到过，x86-64的栈向低地址增长，所以将栈指针减小到一个适当的量可以为没有指定初始值的数据在栈上分配空间，增加栈指针释放空间
- 当x86-64过程中需要的存储空间超出寄存器能够存放的大小时，就会在栈上分配空间。这个部分叫<font color="red">栈帧</font>。P165

#### 数据传送
调用一个过程，还可能包括把数据作为参数传递，从过程返回还有可能包括返回一个值。
- x86-64中，大部分过程间的数据传送是通过寄存器实现的（6个寄存器，6个参数），超出6个的部分就要通过栈来传递。假设P调用Q，有n个参数，n>6，那么P的栈帧必须容纳7到n号参数，1~6放到寄存器，7~n放到栈上。**被调用过程Q就可以通过寄存器访问参数，必要的话也可以通过栈访问**。

有些时候，局部数据必须放在内存中，常见情况：
- 寄存器不足以存放所有的本地数据
- 对一个局部变量使用地址运算符‘&’，因此必须能够为它产生一个地址
- 某些局部变量是数组或结构，必须要通过数组或结构引用才能被访问到

变长数组P181
数据对齐P189

<font color="red">**内存越界引用和缓冲区溢出是造成重大BUG的一大因素**</font>

对抗缓冲区溢出攻击的三种方法：
1. 栈随机化：使栈的位置在程序每次运行时都有变化
2. 栈破坏检测：在栈帧中任何局部缓冲区和栈状态之间设置一个哨兵
3. 限制可执行代码区域

可变长栈帧P201

---

