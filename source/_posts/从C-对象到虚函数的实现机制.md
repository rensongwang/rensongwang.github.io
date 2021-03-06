---
title: 从C++对象到虚函数的实现机制
tags:
  - 虚函数
categories:
  - 深度探索C++对象模型
date: 2019-08-03 10:45:14
photo:
---

{% note default %}
在实际的C++面向对象编程过程中，我们平时直接写个类，然后声明一个对象，然后直接用这个对象的各种函数，操纵类定义中的各种数据成员，对象底层是如何实现的？另外C++多态（这里只说虚函数）的实现机制到底是怎样的，曾经疑惑的struct和class的区别到底是什么？
{% endnote %}

<!-- more -->

## 开门见山，C++对象模型
C++对象模型其实最开始设计的有很多，但最终留下来的是：
C++类对象中内含所有的非静态数据成员，而其他比如静态或非静态的函数成员被放在对象之外。
当然还有其他东西，后面说。
这样看来，维护C++对象的局部成本相比较于struct并没有差别

## 闲谈struct和class
实际上，就关键字而言，struct和class本身并没有差异。
非要说区别，那么唯一的区别看起来也并不算是什么区别。
就比如：
```
struct {} 等同于 class {public：}
```
也就是说struct在没有进行任何说明的情况下，其中所有成员默认是public的，而class默认是private的。是不是也不算是什么差异？
**问题就在于它俩本身是否给予“类型的内部声明”以某种承诺**（这段话我目前也是理解不够透彻）。

## C++对象的额外负担：虚函数机制
C++对象以以下支持虚函数：

{% note danger %}
- 定义的每个类产生一堆指向虚函数的指针，放在表格当中，也就是说，有一张**虚函数表（vtbl）**，这个表的第一个条目是RTTI（运行时类型定义）。
- 每个类对象被安插一个指针，指向对应类的虚函数表。这个指针就是**vptr**。
{% endnote %}

## C++多态
C++以下列方法支持多态：
- 一组隐式的转化操作，比如把一个派生类指针转化成指向基类指针。Shape * p = new Circle();
- 虚函数机制
- dynamic_cast和typeid运算符

多态是什么？
由一个共同的接口来影响不同的类型的封装。
在虚函数中怎么实现？
细节就是上面所说，概括来讲就是，这个接口通常定义在抽象的基类中，也就是虚函数，它可以在执行期根据对象的真正类型来解析出到底使用哪一个函数实例。

## 为什么一定要用指针或者引用来实现虚函数的多态？

**OO程序设计并不支持对对象的直接处理**。
也就是说：
```
Bear b;
ZooAnimal za = b;
za.rotate();
```
是行不通的。
这是因为用一个派生类对象来初始化一个基类对象，派生类对象就会被切割以塞入较小的基类内存中！！！多态就没有办法呈现！

为什么指针或者是引用可以呢？
一般来说，从内存角度来讲，指针都是4字节大小，表示方法和内容（一个地址）的差异也无关紧要，**关键在于寻址出来的对象类型不同**。
见下：
```
Bear b;
ZooAnimal *pz = &b;
Bear *pb = &b;
```
pz和pb有什么不同呢？
它们所指向的都是b对象的第一个byte，但是涵盖的内容不同！pb涵盖整块b对象的内容空间，而pz只涵盖了ZooAnimal的部分。
那么这也就是造成了，**pz只能处理ZooAnimal subobject中出现的成员**。
但是，**唯一的例外就是虚函数机制**
**当我们用pz来调用虚函数的时候，在执行期，就会根据pz所指向的对象的类型来决定所调用的实例**。
这个类型信息并不是在pz里面，而是维护与link中，在对象的vptr和vptr所指的vtbl之间。











--- 

