---
title: IO Operation in STL
date: 2020-08-30 13:27:34
tags:
categories: STL
---
现代语言都有抽象出来的IO操作库。  
在写C++代码的时候，发现很多人还是喜欢用C语言的那一套fopen/fread/fwrite/fclose，从效果上说没有区别，但从代码风格上看就比较诡异了。  
尤其是fopen这套操作得到的是内存流，而我们的C++代码里经常要用到Stream流（因为很多STL库或者C++11风格三方库要求输入Stream流，虽然有一些也保留了指针接口，但大趋势是C++11风格）。  
为了后续兼容性和代码风格上的统一，C++ IO是我们C++工程的首选。  

{% asset_img io_relationship.jpg STL IO HIERARCHY %}   




__参考资料__  
[C++标准I/O库：iostream, fstream, sstringstream](https://blog.csdn.net/anonymalias/article/details/27714359)  

