---
title: Rust - How to Create and Run a Project
date: 2020-09-02 15:09:59
tags:
categories: Rust
---

听说Rust是一门立志于取代C++的语言，特意来学习，目前我并不知道它的优势是什么，但对toml lock文件格式我并不陌生。  

现在开始吧。  

## __<font color=0xFFFFFF>新建工程</font>__

1. 自动化方法 
   |command   |usage|
   |:----|:----|
   __<font color=blue>cargo new</font>__   | create a new project with init files, .toml, etc   |
    __<font color=blue>cargo build --release (default debug)</font>__  |compile program, generate binary file  |
    __<font color=blue>cargo run</font>__  |compile, generate and run binary file | 
    __<font color=blue>cargo check</font>__ | compile files |
    

2. 手工方法  
   创建cargo new生成的目录结构及配置文件。  

<!--more-->  

## __<font color=0xFFFFFF>Cargo包管理机制</font>__  

.toml文件格式  

    [dependencies]  
    rand = "0.5.5"

由toml文件可以生成lock文件，可以看做toml文件的结果缓存。  

如果运行cargo build，那么会下载依赖库。cargo update会更新依赖库，但只会更新一个minor newer version，不是big newer version update。 要想big version update，就要修改toml文件。 

这里有几个问题（留待后续看cargo再解决）  

1. 下载的依赖库是源码还是binary？如果是binary的话，如果binary不能支持开发平台，该怎么办？cargo支持自己搭建类似于crato.io的server吗？或者兼容crato.io和自己的远程编译server?
2. 如何指定编译平台？cargo 自动识别吗？如果是编译库的话，我们要指定一些特殊选项，该怎么配置？  
3. cargo update更新的规则是什么？为什么这么设计？


