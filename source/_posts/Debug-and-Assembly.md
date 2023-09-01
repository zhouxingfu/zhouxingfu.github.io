---
title: 调试与汇编
date: 2020-08-31 11:36:09
tags:
categories: assembly
---

## __<font color=0xFFFFFF>前言</font>__


首先介绍一个可以在线查看C++转汇编的网站 [www.godbolt.org](www.godbolt.org)，可以选择任意平台 任意版本的编译器，非常方便学习。 




C++是一门需要跟底层打交道的语言，这里的底层不仅仅包括STL库，还包括汇编乃至指令集。  

对上，C++要写出媲美java C#甚至python的代码，底层还要关心各种内存分配、指针，ABI兼容，出了错误，非常难以排查，不像“虚拟机语言”，只需要关注自己这一层。从这个角度看，C++语言的代码耦合度太高了。至少以下几个方面并不适合C++  

    1. 界面开发: 很多大厂的PC端用的是directUI架构，要想写好需要对C++熟练，而且要自己做各种精细控制。  
    2. 小工具开发：胶水语言不香吗？  
    3. 科学试验性质项目：比如机器学习模型训练  
其他：比如一般的程序开发，除非其它语言没有对应的库，否则都应该选用JAVA/C#/PYTHON这类语言开发，或者golang/rust。总之，C++的学习曲线太陡峭，但跟rust相比，历史包袱又很重。  
既然这样，为什么我们还要学习C++呢？就是因为目前来说没有特别好能替代的，或许再过两年，rust和go成长起来之后，C++的市场份额会进一步萎缩，慢慢地被淘汰掉吧，淘汰也不是仅仅靠喊就行的，新语言还是得加油努力喔。  

在Linux下我们用gdb进行代码调试和crash分析。在Windows平台我们有很多工具，一般是用IDA进行静态反汇编，用OD或者WinDbg进行动态分析，WinDbg也可以反汇编。  
现在写blog的平台是Linux，所以先写linux的。我们的重点放在分析crashdump上。  


## __<font color=0xFFFFFF>gdb crashdump分析</font>__  
生成dump的方式很简单就不用说了，ulimit -c unlimited，不过这里生成的是默认名称，为了方便保存且与代码对应，可以做些修改，每次生成新的名字。  

一般发布版本，要从几个方面考虑
1. 可追溯
2. 自动化  
3. 防破解  

这里，我们提到的是可追溯。每次发布版本要在git上有对应的commit，而这个commit要很容易就从SDK里获得，比如在加载时输出commit-id。  

### 分析堆栈  

<!--more-->

{% asset_img stack-frame-call.png This is an example image %}

从上面我们可知  
入栈时具体操作是

    1)调用参数从右往左压栈  
    2)返回地址入栈  
    3)跳转到子函数起始地址 EIP 
    4)子函数将父函数栈帧起始地址%rbp入栈  
    5)将%rbp的值设置为当前%rsp的值，开辟栈帧空间  

出栈的时候，我们要清理堆栈  
 
    1)movq %rbp, %rsp ; 使 %rsp 和 %rbp 指向同一位置，即子栈帧的起始处, 收回子栈帧空间
    2)popq %rbp ; 将栈中保存的父栈帧的 %rbp 的值赋值给 %rbp，并且 %rsp 上移一个位置指向父栈帧的结尾处

为了便于栈帧恢复，x86_64 架构中提供了 leave 指令来实现上述两条命令的功能。执行 leave 后，前面图中函数调用的栈帧结构如下　　

{% asset_img stack-frame-leave.png stack frame leave %}

调用 leave 后，%rsp 指向返回地址；ret 指令，从栈顶弹出数据，并跳转到此数据指向的地址处。在leave 执行后，%rsp 指向返回地址，因而 ret 的作用就是把 %rsp 上移一个位置，并跳转到返回地址执行。

所以，leave 指令用于恢复父函数的栈帧，ret 用于跳转到返回地址处，leave 和ret 配合共同完成了子函数的返回。当执行完成 ret 后，%rsp 指向的是父栈帧的结尾处，父栈帧尾部存储的调用参数由编译器自动释放。

## __<font color=0xFFFFFF>恢复被破坏的堆栈</font>__  
看知乎上回答问题的人，经常会开头来一句“先问有没有，再问是不是，再问为什么”。  

怎么判断一个堆栈被破坏了？   
很明显的，EIP EBP RETADDR这些都不是正常的内存值，比如?????或者0x00000000这类的。  

至于该怎么恢复堆栈，我觉得总体上建立在一个基础上，也就是[How to rescue a broken stack trace: Recovering the EBP](https://devblogs.microsoft.com/oldnewthing/20110309-00/?p=11263)  这里面所说的，堆栈其实就是EBP组成的linked-list，虽然因为各种原因（比如数组越界等）会破坏堆栈，但终归不会把所有层的堆栈给破坏了。  

比如在上面的例子中，ESP看上去还算正常，那么我们就看一下ESP周围数据，根据linked-list的特性，如果有一个EBP，那么EBP本身是一个地址，同时，在它的底层函数，EBP也会是一个value（call 子函数的时候，会先push EBP）。

在这里，我们回顾一下EBP附近的堆栈结构  

The structure of each stack frame is therefore  

| addr   | description |  
|  ----  | ----  |
|[ebp+n]	| Offsets greater than 4 access parameters |
|[ebp+4]	| Offset 4 is the return address |  
|[ebp+0]	| Zero offset accesses caller’s EBP |  
|[ebp-n]	| Negative offsets access locals |

在这里，如果我们找到EBP，那么附近的就是RetAddr，在gdb下用info symbol 内存地址 可以看到具体的函数。  

[[翻译]手把手教你修复被破坏的堆栈](https://bbs.pediy.com/thread-254771.htm)   
上述链接对应的资源在 {% asset_link corrupted-stack-example-resource.zip  这里下载 %}

## __<font color=0xFFFFFF>FPO产生的影响</font>__  
FPO = Frame Pointer Omission  
FPO优化是intel处理器单独必备的，它的主要原理是不再为调用的子函数进行push EBP , mov ebp esp的操作。  

[Frame pointer omission (FPO) optimization and consequences when debugging, part 1](http://www.nynaeve.net/?p=91)
[Frame pointer omission (FPO) optimization and consequences when debugging, part 2](http://www.nynaeve.net/?p=97)


[FPO](https://docs.microsoft.com/en-us/archive/blogs/larryosterman/fpo)  



## __<font color=0xFFFFFF>通用命令</font>__   


| command   | description |  
|  ----  | ----  |
| bt	| 查看堆栈 |
| frame n	| 查看堆栈中第n帧信息 |  
|list	| 查看function周围的代码 |  
|	info locals|  查看本地变量|
|print variable_name| 查看具体变量value|


## __<font color=0xFFFFFF> 寄存器建议使用规则</font>__  

{% asset_img registers.jpg %}




### __<font color=0xFF>GDB查看指定内存地址处的内容</font>__   
命令格式：x/nfu <addr>

如：

（gdb）x/1xb 0x7fffffffd708

x : examine 的缩写

n : 表示要显示的内存单元个数

f : 表示显示方式, 可取如下值
x 按十六进制格式显示变量。
d 按十进制格式显示变量。
u 按十进制格式显示无符号整型。
o 按八进制格式显示变量。
t 按二进制格式显示变量。
a 按十六进制格式显示变量。
i 指令地址格式
c 按字符格式显示变量。
f 按浮点数格式显示变量。

u表示一个地址单元的长度，与n一起表示显示的地址长度
b表示单字节，
h表示双字节，
w表示四字节，
g表示八字节


### __<font color=0xFF>查看正常函数名称</font>__   

_ZNSaIcEC1Ev@plt 在反汇编或者coredump堆栈中，我们经常会看到这类名字，该怎么知道它之前正常的名字呢？  
这涉及到name mangling技术。最开始没记起这个名词，连搜索都不好搜索。  

[How to make gdb show the original non-mangling function name on disassembly model?](https://stackoverflow.com/questions/1957228/how-to-make-gdb-show-the-original-non-mangling-function-name-on-disassembly-mode)  

c++filt是一个经常用的技术，~~但好像不是所有情况下都可以~~。昨天c++file _ZNSaIcEC1Ev@plt，命令执行完还是这个字符串，当时认为是这个命令可能不适用于所有字符串，或者哪个环节出了问题。  

c++filt _ZNSaIcEC1Ev  

__后面那个@plt到底是干嘛的？__

### __<font color=0xFF>objdump查看反汇编代码，如何从AT&T风格改为intel</font>__   

gcc默认的汇编器是GAS，语法是AT&T

objdump -d -mi386:x86-64:intel exe or lib file name


### __<font color=0xFF>FS寄存器分析</font>__   


## __<font color=0xFFFFFF>参考资料</font>__  


[x86-64 下函数调用及栈帧原理 - 冷风寒雨宿天涯的文章 - 知乎](https://zhuanlan.zhihu.com/p/27339191)

[x86_64架构下的函数调用及栈帧原理 - 看雪学院的文章 - 知乎](https://zhuanlan.zhihu.com/p/107455887)  

[函数调用过程&栈帧&调用约定](https://blog.csdn.net/zrf2112/article/details/95661316)  

[使用 gdb 恢复堆栈信息](https://www.jianshu.com/p/088fb171cd40)

[How to make gdb show the original non-mangling function name on disassembly model?](https://stackoverflow.com/questions/1957228/how-to-make-gdb-show-the-original-non-mangling-function-name-on-disassembly-mode)