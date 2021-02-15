---
title: Input/Output library
date: 2020-08-30 13:27:34
tags:
categories: STL
---

## __<font color=0xFFFFFF>前言</font>__  

现代语言都有抽象出来的IO操作库。  
在写C++代码的时候，发现很多人还是喜欢用C语言的那一套fopen/fread/fwrite/fclose，从效果上说没有区别，但从代码风格上看就比较诡异了。  
尤其是fopen这套操作得到的是内存流，而我们的C++代码里经常要用到Stream流（因为很多STL库或者C++11风格三方库要求输入Stream流，虽然有一些也保留了指针接口，但大趋势是C++11风格）。  
为了后续兼容性和代码风格上的统一，C++ IO是我们C++工程的首选。  

{% asset_img input_output_hierarchy.png STL IO HIERARCHY %}   


## __<font color=0xFFFFFF>关于文件IO与标准IO</font>__

{% asset_img posix_and_c.png difference between posix and sys call %}   

文件IO是syscall，而标准IO是库接口。文件IO是没有缓存的，标准IO有三种形式：全缓存，行缓存，无缓存。 

<font color=green>
NOTE:
这里有两个需要注意的点  
1. 文件IO也是有缓存的，只不过标准IO是用户态缓存，文件IO是内核态缓存，标准IO的好处是可以减少访问CPU的次数。  
2. 文件IO也叫低级别IO，所以不要以为fread fwrite就是文件IO，这里其实有混淆，感觉起名字不严谨。  
</font>

|操作|标准ＩＯ|文件ＩＯ(低级IO)|
|:------|:------|:------|
|打开 | fopen,freopen,fdopen| open|
|关闭|fclose|close|
|读|getc,fgetc,getchar fgets,gets,fread | read |
|写|putc,fputc,putchar fputs,puts,fwrite | write|




## __<font color=0xFFFFFF>参考资料</font>__  
[Input/output library](https://en.cppreference.com/w/cpp/io)  

