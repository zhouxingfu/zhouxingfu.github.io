---
title: Rust Common Programming Concepts
date: 2020-09-02 21:45:45
tags:
categories: Rust
---
_"This chapter covers concepts that appear in almost every programming language and how they work in Rust. __<font color =red>Many programming languages have much in common at their core. None of the concepts presented in this chapter are unique to Rust</font>__, but we’ll discuss them in the context of Rust and explain the conventions around using these concepts."_ 

跟王垠之前在某篇blog里谈到的一样，有一个特性的集合，每个语言是里面的一个子集，C++也是如此。至于为什么C++的学习曲线这么抖，我想大概是因为它要的东西太多，什么都能做，但有的语言比它更专业，虽然功能比它少。也就是说C++是一门不够专注的语言。  

好了，C++的话题到此为止，我们看Rust。  

### __<font color=0xFFFFFF>immutable constant 区别</font>__  

|const | variable|
|:----|:----|
|在整个生命周期内value不可更改|var可随时重新定义，比如let x= 10; let mut x = 20;默认为immutable，可随时更改为mutable|
|必须注明类型|不必注明类型|
|可以在任何范围内定义，比如全局|貌似不可以？|
|const赋值时必须是常亮表达式，或者至少能找到初始值，而不能是一个function call或者运行时才能获取的值|可以？|

