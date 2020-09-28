---
title: utf8 格式源代码跨平台编译
date: 2020-09-28 17:00:42
tags:
categories: [other]
---

在Windows平台，如果不指定bom头的话，是不清楚当前是什么编码格式的，如果我们在代码中使用了中文，系统根本不知道是utf-8 还是unicode-16，而对Linux来说，默认就是utf-8，所以写中文没关系。

现在VS已经可以支持utf-8 without BOM的源码编译了，VS2015 update2之后就支持附加编译选项/utf-8，支持直接输出中文。  [/utf-8 (Set Source and Executable character sets to UTF-8)](https://docs.microsoft.com/en-us/cpp/build/reference/utf-8-set-source-and-executable-character-sets-to-utf-8?redirectedfrom=MSDN&view=vs-2019)  

