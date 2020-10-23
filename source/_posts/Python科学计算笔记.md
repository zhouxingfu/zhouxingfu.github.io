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

<!--more-->

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
|一维|一维|从一维中取数，组成一维array | ~~a[np.array([x1, x2, x3, x4])]~~|
|一维|多维|从一维中取数，组成N维array |~~错误的方式 a[[[x1,x2,x3,x4],[x5,x6,x7,x8]]]~~ <br>~~正确的方式 a[np.array([[1, 2, 3], [4, 5, 6]])]~~|
|多维|一维|从N维中取数，组成一维array | |
|多维|多维|从N维中取数，组成N维array | |  


本来想着列个表就能简单的说清楚，但现在看，还是有些复杂的，下面就展开来说吧。  

下面有几种写法，让我们看一看 

<font color=red>input : 一维数组    output : 一维数组和二维数组</font>

    a = np.array(1, 10, 1)
    print(a[1, 3, 5]) # 错误，因为a是一维数组，但提供了3维索引
    print(a[[1, 3, 5], [2, 4, 6]]) # 错误，跟上面一样，提供了两个index
    x = [[1, 3, 5], [2, 4, 6]] 
    print(a[x]) # 错误，IndexError: too many indices for array: array is 1-dimensional, but 2 were indexed
    # 上述语句还有warning  FutureWarning: Using a non-tuple sequence for multidimensional indexing is deprecated; use `arr[tuple(seq)]` instead of `arr[seq]`. In the future this will be interpreted as an array index, `arr[np.array(seq)]`, which will result either in an error or a different result.

    y = np.array([[1, 3, 5], [2, 4, 6]])
    print(numpy.array(y)) # 正确

从上面的例子中，我们可以得到以下几个简单的结论 

* input为一维数组，索引必须也是一维的，不管输出是怎么样，要确保能访问到input中的元素，下标必须为一维，所以a[[1, 3, 5]]是正确的，a[1, 3, 5]是错误的


但这里有一个问题，就是a[x]和a[np.array(x)]有什么区别？  
通过help(x)可以得知一个是numpy.ndarray，一个是list。个人猜测，list是不确定大小的，也就是我们拿到一个list，里面的元素大小也不一定相同，所以需要转成ndarray，明确是一个正常的数组。否则，就会报上面的IndexError和Warning。  


<font color=red>input : 二维数组    output : 一维数组和二维数组</font> 

    a = np.arange(1, 10, 1).reshape(-1,1) + np.arange(10, 60, 5)
    x = [[1, 3, 5], [2, 4, 6]] 
    print(a[x])  # 正确，但会报warning，输出一个1*3的数组array
    m = ([1,2,3], [4,5,6])
    print(a[m])  # 正确，无warning，下标是有两个元素的元组，输出结果是1*3的数组array
    print(a(np.array(x))) # 正确，但输出是一个三维数组
    y = [[1,2], [3,4]]
    z = [[5,6], [7,8]]
    print(a[y,z]) #正确

上面我们看到，print(a[x])会报warning， _<font color=red>FutureWarning: Using a non-tuple sequence for multidimensional indexing is deprecated; use `arr[tuple(seq)]` instead of `arr[seq]`. In the future this will be interpreted as an array index, `arr[np.array(seq)]`, which will result either in an error or a different result.</font>_。  
也就是说，对于多维数组的index操作，我们需要传入一个tuple sequence，传入non-tuple sequence的方法已经被废弃了。  

至于print(a(np.array(x)))输出一个三维数组，可以这样理解，首先np.array(x)得到的是一个array，但只有一个元素，在书中有这么一句话 _<font color=red>当所有轴都用形状仙童的整数数组作为下标时，得到的数组和下标数组的形状相同。</font>_ 但当我们传入的是只有一个元素的tuple的时候（不管这个元素内部是多么复杂）， 当我们没有指定第一轴的下标的时候，我们用:来代替。此时，我们要重新明确一下a[m,n]中代表的涵义，m是0轴坐标，n是1轴坐标，既然n为:，那么所有的列数据都被占用。  


__<font color=green size=5>总结</font>__  
说实话，这块理解还是挺难的，而且实际使用中有些内容已经有差别了，比如被废弃的方法，还是要时常温故，多加体会。  


__关于存取元素时是否与原数据共用__  

中心思想，是抓住问题的核心，如果我们可以仅仅通过修改stride shape就能得到一份新数据，这时候肯定是不用拷贝最合算，如果没办法通过修改stride shape得到数据，那么就只能拷贝了。  两个简单的例子， a[::2, :]是无需拷贝的，只需要把元素的stride*2就可以“得到”我们想要的数据；a[[1,4,7,9]]是需要拷贝的，此时我们不能通过简单修改stride就得到我们想要的数据。  


### __1.4 frompyfunc__  

感觉类似于函数指针typedef，不过问题是看上去它只有一个可以遍历的arg.  

    def calc_test(a, b, c, d):
        print(a + c + b + d)


    def create():
        a = np.arange(1, 10, 1) # 正确
        a = 10 # 正确
        b = np.arange(10, 100, 10)
        c = np.arange(100, 1000, 100)
        d = np.arange(1000, 10000, 1000)
        calc_test_func = np.frompyfunc(calc_test, 4, 1)
        print(calc_test_func(a, b, c, d))

上面这段代码，经过几次测试，得出的规律是，frompyfunc的几个特点

    m = np.arange(1, 10, 5) + np.arange(10, 100, 10) # 错误 ValueError: operands could not be broadcast together with shapes (2,) (9,) 


如果两个或者多个数组size不一致，那么这时候就出发挥 广播机制  

