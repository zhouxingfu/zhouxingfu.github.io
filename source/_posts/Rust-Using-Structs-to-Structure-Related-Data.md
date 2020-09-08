---
title: Rust Using Structs to Structure Related Data
date: 2020-09-08 11:20:49
tags:
categories: Rust
---
Rust没有class的概念，有struct对应于其他类型的class，以及C/C++中的struct概念。

|特性|Rust struct| C++ Class| 
|:----|:----|:----|
|声明形式|与C语言struct类似，struct Rectangle{width:u32, height:u32,}|class {} 或者struct {} Test;| 
|method|类似于非静态函数，声明形式跟Python中class接近，会有一个self参数，不过这个self参数可以是self或者&self，取决于想要borrow一个mutable或者immutable|self与C++中隐藏的this指针类似| 
|Associated Function|没有self参数，一般用作构造函数，比如String::from()就属于这种性质|不知道除了构造函数之外，还可以用作static成员函数吗？|
