---
title: Rust Understanding Ownership
date: 2020-09-05 17:25:09
tags:
categories: Rust
---

所有程序在运行的时候必须管理内存。有一些语言有垃圾回收机制，比如Java Python，另外一些语言必须自己管理内存，比如C++。  

Rust选择了第三条路：ownership。  

Rust文档上说  
```
All data stored on the stack must have a known, fixed size. Data with an unknown size at compile time or a size that might change must be stored on the heap instead.
```
也就是一个数据，如果要存储在栈上，那么在编译的时候就必须确定大小，并且大小不能更改？  
这句话和我之前的认知是矛盾的
```
int main(int argc, char* argv[])
{
    int a;
    cin >> a;
    int x[a];
}
```
在编译时，我们并不知道x所占空间大小。  

Rust ownership的设计思想是，我所有的variable name都绑定到stack上的一个空间，但这个空间里面不存储data，只存储pointer，指向heap。  

ownership规则  
* 每一个value都有一个叫做owner的variable。
* 同一个时刻只能有一个owner。
* 当owner离开了作用于，data将会被dropped释放。  

上面说stack里面只存储指针，不存储数据，其实是不准确的，因为有些叫Stack-Only Data，比如integers。

如果一个类型实现了Copy接口，那么就不会存在Drop接口；反之亦然。  

像是integers这种类型在编译的时候能够知道需要空间大小，所以实际的value非常快；同时，对这些类型来说，深拷贝和浅拷贝没有任何区别。


|场景|  ownership转移 | 解决思路/效果|
|:----|:----|:----|
|作为函数形参传递|此时，ownership就已经转移到调用的子函数了，子函数执行完毕，变量就被drop了，此时，变量不可用。|将形参返回，此时ownership会重新被转移回来|
|返回形参不是一个好方式，如果有很多形参，这导致整个函数的臃肿。如果用&的方式传递|子函数内不再拥有形参的ownership|可以放心地在子函数内使用形参，不用再返回形参|
|&不占用ownership，那么可以有多个&吗？如果声明为&mut，是不是就可以修改形参了？|rust在设计上避免data race，要避免data race，所以可以同时读，读和写的作用域不能重叠，我们在C++中可以加锁控制，但在rust里就是本质上排除掉这种行为。|请参见 __如何避免data race__|
||||



## __<font color=greeb>如何避免data race</font>__  

首先，要明确出发data race的三个必要条件
1. 同时有两个或以上的pointer可以访问同一个数据
2. 至少有一个pointer正在写数据
3. 没有一个机制来同步访问数据  


Rust为了解决这个问题，直接在编译阶段就报错。  

有以下几种情况  
1. <font color=red>同一个作用域内</font>只要有一个&mut  

在这里，首先要理解同一个作用域的概念。  

```
let mut x = String::from("hello")
{
    let y = &mut x;
}
let mut& z = x;
```
在上面的例子中，跟C/C++的作用域的概念类似，离开了代码块，我们就可以重新borrow了。

```
fn main() {
    let mut s = String::from("hello");

    let r1 = &s; // no problem
    let r2 = &s; // no problem
    let r3 = &mut s; // BIG PROBLEM

    println!("{}, {}, and {}", r1, r2, r3);
}
```
上面的例子中，r3和r1 r2的作用域重叠，如果我们这些改写
```
fn main() {
    let mut s = String::from("hello");

    let r1 = &s; // no problem
    let r2 = &s; // no problem
    println!("{} and {}", r1, r2);
    // r1 and r2 are no longer used after this point

    let r3 = &mut s; // no problem
    println!("{}", r3);
}
```
就是没有问题的。Rust程序是怎么判断作用域的呢？在compile的时候，rust程序会看到，在let r3 = &mut s;之后已经没有再使用r1 r2了，这时候Rust就认为r1 r2的作用域在r3之前已经结束了。  


## __<font color=greeb>如何避免dangling pointer悬停指针（野指针）</font>__    

我们知道如果两个指针指向同一个memory，如果我们free一次，那么此时另外一个指针就是成为野指针。  

Rust为了解决这个问题，在编译的时候就会给予提示。  

不光是上面说的情况，只要是指针指向的memory已经被drop掉了，那么指针就会编程野指针，下面的代码，在C++ compile的时候不会报错，但运行的时候会crash，Rust是在compile的时候做了强检查。  

```
fn main() {
    let reference_to_nothing = dangle();
}

fn dangle() -> &String {
    let s = String::from("hello");

    &s // Here, s goes out of scope, and is dropped. Its memory goes away.
}
```

该怎么解决这个问题呢？可以照着下面改
```
fn main() {
    let string = no_dangle();
}

fn no_dangle() -> String {
    let s = String::from("hello");

    s //Ownership is moved out, and nothing is deallocated.
}
```

现在总结下reference的两个使用规则  
1. 在任何时候，可以有一个mut reference和若干个immutable reference。 
2. reference必须一直有效。  



## __<font color=greeb>切片类型slice type</font>__  

slice type是另一个没有ownership的数据类型。