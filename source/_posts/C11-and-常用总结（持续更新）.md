---
title: C++ Primer and 常用总结（持续更新）
categories: C++ 基础
date: 2019-03-27 11:06:00
tags:
- C++ Primer
---
{% note default %}
在看过了C++ Primer之后很长一段时间，对于C++的新特性的应用大为惊叹。毕竟语法经常不用总会遗忘，在学习玩新知识之后，这本书对于我来说，更像是一本字典。所以写一点小总结，后面会持续更新。
{% endnote %}

<!-- more -->

## 第一章
- 编程中如果单纯要递增，尽量使用++i

## 第二章
- 切勿混用带符号类型和无符号类型，如果表达式里都有，那么带符号类型取值为负时会出现异常结果，因为带符号数会自动转换成无符号数
- 初始化不是赋值，初始化时创建变量是赋予一个初始值，而赋值时将对象当前值擦除，用一个新值代替
- 变量能且只能被定义一次，但是可以被多次声明（建议，当你第一次使用变量的时候再定义它）
- **引用不是一个对象，相当于一个别名，这个别名不能又变成另外一个人的，所以定义的时候必须初始化，应用只能绑定在对象上**
- 指针存放某个对象的地址，获取地址，用取地址符&，对应的，解引用\*，就是那个对象
- **面对一条复杂的指针或引用声明语句时，从右向左读**
- **const，常量，就是不能改变，所以引用要是常量引用，指针要是指向常量的指针，而常量指针呢就是这个指针本身就是一个常量，所以常量指针必须初始化，即只能指向一个对象**
- 还有就是顶层const就是说本身就是常量，底层const说的是指向的对象是const
- **auto这个东西很好用，类型是通过右值推出来的**

## 第三章
- **范围for循环语句，挺方便的**
- 先介绍的string，然后是vector，vector实际上就是一个容器。是对象的集合，所有对象的类型都相同，且不像数组一样要固定大小，能高效增长，往里面添加元素是punsh_back（），不能以下标形式添加元素，因为这样很容易访问未定义行为
- 迭代器，很好用，直接举例吧
``` 
auto b = v.begin(), e = v.end();
```
要注意的是end返回的迭代器并不实际指示某个元素，所以不能对其递增和解引用，因为它是尾后指针
- **但凡是使用的迭代器的循环体，都不要向迭代器所属的容器添加元素，因为会是迭代器失效，我在实际编程中经常犯这种错误**
- 不能在范围for循环中向vector添加元素
- 其实指针也是一种迭代器
- 数组也可以用**标准库函数**begin和end，例子：int \*b = begin（数组）或者是end（数组）
- 建议：尽量使用vector和迭代器，避免使用内置数组和指针，尽量使用string

## 第四章
- 第四章讲的是运算符的一些东西，讲的比较基础，其中要注意的是优先级顺序对求值顺序的影响，其中拿不准的就用括号，然后就是注意**在表达式中改变了某个运算对象的值，表达式其他地方就不要再用这个运算对象**
- 布尔值不应该参与运算
- 溢出：当计算的结果超出该类型所能表示的范围时就会产生溢出
- **取余：除了特殊情况，m%（-n）等于m%n**
- 关系运算符比较运算对象的大小并返回布尔值
- 赋值运算符满足有结合律
- **强调，无特殊需要，尽量使用前置版本的递增递减运算符**
- 位运算符作用于整数类型的运算对象，并把运算对象看成是二进制位的集合。提供检查和设置二进制位的功能，强烈建议位运算符作用于无符号
类型
- **注意隐式类型转换**
- **尽量避免显示类型转换，有static_cast（不包含底层const都可以用）、const_cast（只用于改变const属性）、reinterpret_cast（为运算对象的位模式提供低层次的重新解释）、dynamic_cast**

## 第五章
- 第五章主要讲的是语句，要注意语句作用域（if else容易出错，所以尽量加花括号）
- 条件语句就是if 和switch，switch要注意每个case语句后面不要忘了break，不然会依次执行后面的所有的case语句，除非遇到break
- 迭代语句就是while循环和for循环，还有一个do-while（后面不要忘加分号；）
- break语句就是终止离它最近的while、do while、for或switch语句
- **continue就是终止最近的循环中的当前迭代立即开始下一次的迭代**<font color="red">在实际编程中很容易不熟练而犯错</font>
- 最后就是try语句块和异常处理（多练即可）

## 第六章
- 尽量使用常量引用
- 标准库begin和end（不是迭代器）
- main 处理命令行选项（P197）
- 含有可变形参的函数（P197），initializer_list对象中永远是常量对象，用的时候直接花括号
- 不要返回局部对象的引用或指针
- 使用位置返回类型：auto func（int i） -> int （*）[10]；
- 重载函数P206
- 函数匹配，最佳匹配，最佳匹配都不行，二义性
- 最后，函数指针：vector<int(*)(int, int)> vec;**函数指针要多看且多实际运用，现阶段用的不多**

## 第七章
- 首先类基本是的都知道，主要是一些以前没学过的东西
- 其实成员函数通过一个名为this的额外的隐式参数来访问调用它的那个对象
- 常量成员函数（就是在参数列表后面加上const），表示this是一个指向常量的的指针，也就是说常量成员函数不能改变调用它的对象的值
（有一个例外，就是可变数据成员）
- return *this就是返回调用该函数的对象
- = default ： 要求编译器生成默认构造函数
- class 和 struct的唯一区别是struct定义在第一个访问说明符之前的成员都是public，而class是private
- 可变数据成员：mutable
- 名字查找：P254
- P258建议：使用构造函数初始值
- **成员初始化的顺序是声明的顺序**
- 隐式类类型转换P264：能通过（一个实参调用）的构造函数定义了一条从构造函数的参数类型向类类型隐式转换的规则，如果不想隐式转换，就在构造函数（只有一个实参）前面加上关键字explicit，且explicit构造函数只能用于直接初始化
- 类的静态成员：类的一些成员直接与类本身相关，而不是与类的各个对象保持关联，就是说与类关联在一起，就像一个集体有一个集体属性，而不是而不是每个对象P271：静态成员的优势

## 第八章
- 第八章呢就是具体C++的IO库，也就是输入输出流，像我们一般写程序，都要加的iostream头文件，主要用的是cin，cout之类的，然后我们要使用文件操作的话，那么就是包括fstream头文件，还有就是string流sstream
- 刷新输出缓冲区，有endl（输出内容和一个换行，然后刷新缓冲区）、flush（输出内容，然后刷新缓冲区）和ends（输出内容和一个空字符，然后刷新缓冲区）
- **值得注意的是，如果程序崩溃，输出缓冲区不会被刷新，所以要注意输出缓冲区的刷新**
- 使用文件的话，一般就是创建一个文件流对象，并将这个对象和文件关联起来，通过文件流对象实现对文件的操作
- 文件流有输入文件流（ifstream）和输出文件流（ofstream），输入呢，不是说表面上认为向这个文件进行输入，这里很容易混淆，在计算机- 输入输出具体的主管对象是具体的设备。所以这里的输入文件流指的是从文件中向具体设备进行输入，输出呢，就是向文件进行输出，也就是写文件，便于记忆，输入文件流就是读文件，输出文件流就是写文件
- 在创建文件流的时候，如果提供了文件名，则open自动被调用
- **默认情况下，以out文件模式打开文件会丢弃已有数据，就是说文件的内容会被丢弃（所以用app）**

## 第九章
- 第九章讲的是顺序容器，在前面已经接触过容器了比如string和vector，特别好用，当然顺序容器还有很多，有双端队列deque
- deque：双端队列。支持快速访问。在头尾位置插入/删除速度很快
- list：双向链表。只支持双向顺序访问
- forword_list：单项链表。只支持单向顺序访问
- array：固定大小数组
- 通常用vector是最好的选择，除非有其他的更好的理由
- 对于容器的操作以及一些容器的特定操作和限制P295及后面
- **Insert函数将元素插入到迭代器所指定的位置之前，返回指向新元素的迭代器**
- **erase函数删除迭代器所指位置，返回指向下一个元素的迭代器**
- <font color="red">容器操作可能使迭代器失效，所以保证重新定位好迭代器</font>
- **vector对象是如何增长的：当不得不获取新的内存空间时，vector和string的实现通常会分配比新的空间需求更大的内存空间。这里注意不得不P319**
- 修改string的操作：P323
- 容器适配器：stack、queue和priority_queue P329 栈是在其他的顺序容器上建立，不难理解，因为栈本身就是一种操作受限制的顺
序表

<center><font size="12">未完待续</font></center>

---

