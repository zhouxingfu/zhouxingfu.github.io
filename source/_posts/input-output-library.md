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

<!--more-->

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

## __状态标志符__  

以VC中实现为例
```
template <class _Dummy>
class _Iosb { // define templatized bitmask/enumerated types, instantiate on demand
public:
    enum _Dummy_enum { _Dummy_enum_val = 1 }; // don't ask, TRANSITION, ABI
    enum _Fmtflags { // constants for formatting options
        _Fmtmask = 0xffff,
        _Fmtzero = 0
    };

    static constexpr _Fmtflags skipws     = static_cast<_Fmtflags>(0x0001);
    static constexpr _Fmtflags unitbuf    = static_cast<_Fmtflags>(0x0002);
    static constexpr _Fmtflags uppercase  = static_cast<_Fmtflags>(0x0004);
    static constexpr _Fmtflags showbase   = static_cast<_Fmtflags>(0x0008);
    static constexpr _Fmtflags showpoint  = static_cast<_Fmtflags>(0x0010);
    static constexpr _Fmtflags showpos    = static_cast<_Fmtflags>(0x0020);
    static constexpr _Fmtflags left       = static_cast<_Fmtflags>(0x0040);
    static constexpr _Fmtflags right      = static_cast<_Fmtflags>(0x0080);
    static constexpr _Fmtflags internal   = static_cast<_Fmtflags>(0x0100);
    static constexpr _Fmtflags dec        = static_cast<_Fmtflags>(0x0200);
    static constexpr _Fmtflags oct        = static_cast<_Fmtflags>(0x0400);
    static constexpr _Fmtflags hex        = static_cast<_Fmtflags>(0x0800);
    static constexpr _Fmtflags scientific = static_cast<_Fmtflags>(0x1000);
    static constexpr _Fmtflags fixed      = static_cast<_Fmtflags>(0x2000);

    static constexpr _Fmtflags hexfloat = static_cast<_Fmtflags>(0x3000); // added with TR1 (not in C++11)

    static constexpr _Fmtflags boolalpha   = static_cast<_Fmtflags>(0x4000);
    static constexpr _Fmtflags _Stdio      = static_cast<_Fmtflags>(0x8000);
    static constexpr _Fmtflags adjustfield = static_cast<_Fmtflags>(0x01C0); // left | right | internal
    static constexpr _Fmtflags basefield   = static_cast<_Fmtflags>(0x0E00); // dec | oct | hex
    static constexpr _Fmtflags floatfield  = static_cast<_Fmtflags>(0x3000); // scientific | fixed

    enum _Iostate { // constants for stream states
        _Statmask = 0x17
    };

    static constexpr _Iostate goodbit = static_cast<_Iostate>(0x0);
    static constexpr _Iostate eofbit  = static_cast<_Iostate>(0x1);
    static constexpr _Iostate failbit = static_cast<_Iostate>(0x2);
    static constexpr _Iostate badbit  = static_cast<_Iostate>(0x4);

    enum _Openmode { // constants for file opening options
        _Openmask = 0xff
    };

    static constexpr _Openmode in         = static_cast<_Openmode>(0x01);
    static constexpr _Openmode out        = static_cast<_Openmode>(0x02);
    static constexpr _Openmode ate        = static_cast<_Openmode>(0x04);
    static constexpr _Openmode app        = static_cast<_Openmode>(0x08);
    static constexpr _Openmode trunc      = static_cast<_Openmode>(0x10);
    static constexpr _Openmode _Nocreate  = static_cast<_Openmode>(0x40);
    static constexpr _Openmode _Noreplace = static_cast<_Openmode>(0x80);
    static constexpr _Openmode binary     = static_cast<_Openmode>(0x20);

    enum _Seekdir { // constants for file positioning options
        _Seekbeg,
        _Seekcur,
        _Seekend
    };

    static constexpr _Seekdir beg = _Seekbeg;
    static constexpr _Seekdir cur = _Seekcur;
    static constexpr _Seekdir end = _Seekend;

    enum { // TRANSITION, ABI
        _Openprot = _SH_DENYNO
    };

    static constexpr int _Default_open_prot = _SH_DENYNO; // constant for default file opening protection
};
```
在_Iosb中的实现为例，里面有四个关于文件的状态定义

* _Fmtflags = format flags
* _Iostate = io state   当前iosbase的状态位（通过调用iosbase::rdstate()随时可以与这些预定义好的常量对比）
* _Openmode = open mode  以什么样的方式打开文件（读取或写入，包括文件本身的性质，以及我们会如何使用它）
* _Seekdir = seek dir 从哪个地方开始访问（更加智能便捷，不用每次都把position 0当作start index）

_<font color=red>注意</font>:为什么\_Fmtflags enum只有一个成员，而\_Seekdir enum就定义完全了呢？_  

## __文件操作__  

### __文件读__ 

我所理解的读取一个文件的正常思路
* 确定要访问的初始位置
* 确定当前距离文件结尾的数据长度
* read读取（按照需要读取，只要是<= left_length就可以）

如果我们想要读取文本一行呢？
我们可以调用basic_istream::getline()，当然也有全局性的函数getline(std::basic_istream&, , )，效果是一致的（其实我一直认为这种实现本质上是一样的，为什么要有两个接口？还一个global，一个类接口）


### __文件写__
文件写的思路
* 确定写的方式（追加，清空，还是截断truncate）
* 确定要访问的初始位置
* write写入  

写入文本一行没有writeline，因为没有必要，直接write之后，再写入一个endl就可以了。
其实读取的道理也是一样，也是一个个字符读取，然后遇到endl就把buffer中的数据作为一行输出。  

## __stringstream__

stringstream有什么用途呢？如果是内存操作，我们直接用string是不是就可以了呢？ 

stringstream不仅仅是string，更重要的是stream的特性。  

比如ss >> 100; stringstream重载的<< >>。

比如 str=  " hello world "; string word;如果要想按照单词来读，那么可以通过ss >> word 来操作。

在我看来，stringstream就是在内存中操作io（这是与文件IO的本质区别），本质上还是io 流，只不过在内存中是以string来作为对外沟通的数据结构。  







## __<font color=0xFFFFFF>参考资料</font>__  
[Input/output library](https://en.cppreference.com/w/cpp/io)  

