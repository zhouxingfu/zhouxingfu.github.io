---
title: CMake入门
date: 2021-03-20 05:20:39
tags:
categories: CMake
---

现在的构建工具有很多，CMake是其中的佼佼者，虽然很多人说CMake编译速度太慢，但架不住兼容性好。  

我了解的构建工具有bazel、ninja等，但暂时都没用过，先掌握CMake吧。  


## __从一个简单的项目开始__   


__<font color=orange>一个最简单的例子</font>__  

```CMakeLists.txt
cmake_minimum_required(VERSION 3.0)
project(myApp)  
add_executable(myTarget sample.cpp)
```

只有3行，但也构建了CMakeLists.txt的骨架。  

* 指定cmake支持的最小版本
* 指定项目名称
* 指定目标名称及其依赖  

__<font size = 4 color=red>注意事项</font>__  
1. _关于targetname和projectname_   
targetname和projectname不要相同，projectname直接在CMakeLists.txt中指定，




## __那么如果我们要依赖库呢__

我们先添加一个预先生成好的库，CMakeLists.txt更新为 

```CMakeLists.txt
cmake_minimum_required(VERSION 3.0)
project(myApp)  
add_executable(myTarget sample.cpp)
target_link_libraries(myTarget PUBLIC ui PRIVATE algorithm INTERNAL framework)
```

