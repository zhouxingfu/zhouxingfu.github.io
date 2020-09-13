---
title: Rust(3) Common Programming Concepts
date: 2020-09-02 21:45:45
tags:
categories: Rust
---
_"This chapter covers concepts that appear in almost every programming language and how they work in Rust. __<font color =red>Many programming languages have much in common at their core. None of the concepts presented in this chapter are unique to Rust</font>__, but we’ll discuss them in the context of Rust and explain the conventions around using these concepts."_ 

跟王垠之前在某篇blog里谈到的一样，有一个特性的集合，每个语言是里面的一个子集，C++也是如此。至于为什么C++的学习曲线这么抖，我想大概是因为它要的东西太多，什么都能做，但有的语言比它更专业，虽然功能比它少。也就是说C++是一门不够专注的语言。  

好了，C++的话题到此为止，我们看Rust。  

## __<font color=0xFFFFFF>immutable constant 区别</font>__  

|const | variable|
|:----|:----|
|在整个生命周期内value不可更改|var可随时重新定义，比如let x= 10; let mut x = 20;默认为immutable，可随时更改为mutable|
|必须注明类型|不必注明类型|
|可以在任何范围内定义，比如全局|貌似不可以？|
|const赋值时必须是常亮表达式，或者至少能找到初始值，而不能是一个function call或者运行时才能获取的值|可以？|

## __<font color=0xFFFFFF>shadowing和mut 区别</font>__  

```
let mut x = 10;
x = 100;
和
let x = 10;
let x= x + 100;
的区别
```

|shadowing | mut|
|:----|:----|
|通过let，我们会改变暂时改变x的状态为mutable，等操作结束，x重新变为immutable|mut表示variable的value是可以被修改的|
|shadowing只是重用了variable的name，第二次或者后面let的时候可以更改它的类型|mut不可以，没有let的情况下，前面是什么类型，后面赋值的时候也必须是相同类型|

## __<font color=0xFFFFFF>数据类型</font>__ 


|length |signed|unsigned|
|:----|:----|:----|
|8-bit|i8|u8|
|16-bit|i16|u16|
|32-bit|i32|u32|
|64-bit|i64|u64|
|128-bit|i128|u128|
|arch 类似于c++ size_t，取决于当前程序是64bit or 32bit|isize|usize|


|Number literals	|Example|
|:----|:----|
|Decimal	|98_222|
|Hex	|0xff|
|Octal	|0o77|
|Binary	|0b1111_0000|
|Byte (u8 only)|	b'A'|

<!--more-->
从上面看跟C++一模一样，或者说跟其它很多语言的定义是一样的，语言总是相同的，印证了最开始的那句话，语言是特性的集合。  

_<font color=gray>这里注意到一个自己之前你忽略的问题，那就是float的表示范围。之前的错觉是float跟int32，double跟int64的表示范围一样，只不过中间多了很多小数表示。现在想来，大错特错，如果表示范围一致，那多出来的小数是哪些bit表示的呢。  
float：1bit（符号位）+8bits（指数位+23bits（尾数位）
double：1bit（符号位）+ 11bits（指数位）+ 52bits（尾数位）</font>_

Rust的字符类型都是Unicode编码，怎么在不同的编码之间切换呢？  


Rust有复合类型，tuple和array。  

    __趣味：python数据类型__

    这里突然想到一个问题，就是在Python中，也有tuple，而且tuple是immutable的，那么在Python中为什么有些类型是mutable，有些是immutable，这么设计的原因是什么？  

    不可变数据类型：当该数据类型的对应变量的值发生了改变，那么它对应的内存地址也会发生改变，就称不可变数据类型，包括：int（整型）、string（字符串）、tuple（元组）。  

    可变数据类型的定义为：当该数据类型的对应变量的值发生了改变，那么它对应的内存地址不发生改变，就称可变数据类型。包括：set（集合）、list（列表）、dict（字典）

    [Mutable vs Immutable Objects in Python](https://medium.com/@meghamohan/mutable-and-immutable-side-of-python-c2145cf72747#:~:text=Simple%20put%2C%20a%20mutable%20object,Custom%20classes%20are%20generally%20mutable.)  


Rust中的tuple也可以包含各种数据类型，比如

    let tp = (10, 10.0, 'a', "hello");  

__destructuring解构__   

    let tup: (i32, f64, u8) = (500, 6.4, 1);  
    let (x, y, z) = tup;  

还可以根据下标索引来访问tuple中的元素  

    let x: (i32, f64, u8) = (500, 6.4, 1);
    let five_hundred = x.0;
    let six_point_four = x.1;
    let one = x.2;  

array声明方式 

    let a:[i32;5] = [1,2,3,4,5];  
    let a = [3;5];  //same with let a = [3, 3, 3, 3, 3];  

使用方式也是通过下标索引。  

现在总结一下tuple和array

|tuple|array|
|:----|:----|
|immutable， 即使let mut，也仅仅只是可以修改变量的value，但不能修改变量的类型，比如tuple是（i32, float, character, string)类型，即使我们是按照let mut声明的，那么我们可以修改它的value，比如原先是(5, 5.0, 'a', "hello")，现在可以赋值为(6, 6.0, 'b', "world")，但每个element的类型是确定的，不可以被修改，不能用其它类型的值来赋值|immutable，因为array是同一类型的，所以没有这个问题，唯一的问题是array的size不可更改，即使let mut，也不可以修改array的size|
|可以下标访问|可以下标访问|
|可以解构|没有这个必要|


## __<font color=0xFFFFFF>与C++的Function比较</font>__  

|Rust Function | C++ Function|
|:----|:----|
|没有此要求，只要在scope内被看到|调用的Function必须在Caller之前被声明或者定义 |
|let不返回，所以 _let x = (let y = 6); //error  但是 let y = 100; let x = (y = 0) //成功|int x = (int y=0)是不可以的，但可以写成int x = (y = 0)|
|返回值是不带;的表达式|返回值必须是return语句|
