---
title: Python科学计算笔记
date: 2020-10-20 20:41:57
tags:
categories: Python
---

## __1 numpy__ 

### __1.1 如何创建array__ 

创建array的几种方式

* numpy.array() 

    An array, any object exposing the array interface, an object whose
            __array__ method returns an array, or any (nested) sequence

* numpy.asarray()  

    array_like
            Input data, in any form that can be converted to an array.  This
            includes lists, lists of tuples, tuples, tuples of tuples, tuples
            of lists and ndarrays.


* 自动生成数组 numpy.arange  numpy.logspace numpy.linspace 用于创建等差 等比 array（arange和linspace创建等差array的方式有区别）  

* numpy.full numpy.zeros numpy.ones

* numpy.fromstring numpy.frombuffer numpy.from function 


### __1.3 存取元素__  

有了创造，就要有访问，那么该怎么访问元素呢？  

* 切片  
    正常的切片方式，比如连续切片 a[3:5] 非连续切片a[[1,3,5]]


__<font color=red>一维数组和多维数组的存取</font>__  
我们可以用数组作为索引，来构造新数组  

这里有一个公式 output = method(input)  

首先我们要明确input和output是什么，然后才能找到适合的公式来套用。  

下面是一个简单的总结，其中多维我们先用二维来替代。  

|input|output|note|sample|
|:------------|:------------|:------------|:------------|
|一维|一维|从一维中取数，组成一维array | a[np.array([x1, x2, x3, x4])]|
|一维|多维|从一维中取数，组成N维array |错误的方式 a[[[x1,x2,x3,x4],[x5,x6,x7,x8]]] <br> 正确的方式 a[np.array([[1, 2, 3], [4, 5, 6]])]  |
|多维|一维|从N维中取数，组成一维array | |
|多维|多维|从N维中取数，组成N维array | |  
