---
title: Linux构建程序时如何判断库能否被找到
date: 2020-10-23 14:47:56
tags:
categories: Linux
---

在用CMake构建程序的时候，经常要用到find_package来查找库，那么什么样的库可以被find_package找到呢？  

* ldconfig -p | grep libjpeg  
* locate libGL.so
* pkg-config 

    pkg-config --cflags jpeg  
    pkg-config --libs jpeg  
    pkg-config --cflags "jpeg >= 1.0.0" # for version check  
    pkg-config  --modversion jpeg | awk -F. '{ printf "0x%02X%02X%02X\n",$1,$2,$3 }' #version check  
* dpkg -s packagename (deb-based distribution)
* 