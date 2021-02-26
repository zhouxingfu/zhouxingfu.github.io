---
title: Python中mutable与immutable
date: 2020-10-20 14:40:54
tags:
categories: Python
---

在Python的数据类型中，根据是否可改变，分为mutable与immutable。   


那么跟C/C++相比，所谓的不可修改是什么意思？  

* 不能进行单个元素的赋值操作
* 整体的赋值操作，相当于把变量名绑定了一个新的内存区域  

下面我们针对tuple来写几个例子。  

__<font color=red>不能进行单个元素的赋值操作</font>__  

    a = (1, 2)  
    a[0] = 2 #error

上面这句话会报 TypeError: 'tuple' object does not support item assignment.  

__<font color=red>对变量进行整体赋值</font>__  
```PYTHON
    a = (1, 2)  
    id(a) #140036094436208
    a = -2, -1 # right (-2, -1)
    id(a) #140036072923616
    a += (-1, 4) # right (-2, -1, -1, 4)
    id(a) # 140036094653072
```
    
上面的例子中，我们可以整体赋值，甚至可以用+=，那为什么=不能用，+=可以了。  

先拿a = (-2， -1)来说，(-2, -1)相当于生成一个临时对象，绑定到a上。

同样+=相当于 a = a + (-1, 4)，a + (-1, 4)组成新的临时对象绑定到a上。  

通过id(a)我们可以看到内存的变化（这不是真正的内存地址，但可以看做与内存一致的一个概念）

也就是说，__<font color=green>Python所谓的immutable，是说对象目前所绑定的id所指向的内容上不可修改，如果id改了，那么内容自然是可以被修改的。</font>__  


__修改shape会改变对象的内容或者地址吗__ 

我们上面修改了变量的shape，此时变量的内容是没有改变的，我们打印id(a)的值是没有变化的。  


