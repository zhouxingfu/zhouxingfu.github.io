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


__<font color=green>String和&str的区别</font>__  

&str是字符串的引用，String是基于堆创建的，可增长的。比如let s = "hello world"，s的类型就是&str，右边称为字符串字面量literal，程序编译成二进制文件后，这个字符串会被保存在文件内部，所以s是特定位置字符串的引用，这就是为什么s是&str类型。  

&str由于保存在二进制文件内，所以&str类型不存在生命周期的概念，它是整个程序生命周期static内都能访问的。

String是我们最常使用的字符串类型，本质上是vector，具备跟vetor类似的方法。

也就是说，&str类型变量没有lifetime，所以下面的代码会报错  

```
struct User 
{
    username: &str,
    email: &str,
    sign_in_count: u64,
    active: bool,
}

fn main() {
    let user1 = User {
        email: "someone@example.com",
        username: "someusername123",
        active: true,
        sign_in_count: 1,
    };
}

```

## __<font color=0xFFFFFF>使用示例</font>__  

### __<font color=blue>非结构体用法</font>__  

```
fn main() {
    let width1 = 30;
    let height1 = 50;

    println!(
        "The area of the rectangle is {} square pixels.",
        area(width1, height1)
    );
}

fn area(width: u32, height: u32) -> u32 {
    width * height
}

```


### __<font color=blue>简化用法</font>__  
可以在传入参数时临时构造结构体，但这样有一个问题，就是不知道结构体中成员的意义。

```
fn main() {
    let rect1 = (30, 50);

    println!(
        "The area of the rectangle is {} square pixels.",
        area(rect1)
    );
}

fn area(dimensions: (u32, u32)) -> u32 {
    dimensions.0 * dimensions.1
}

```

### __<font color=blue>常规用法</font>__    
定义新的结构体，在传参时把结构体作为参数，不管是mut还是immutable。这种使用方式跟C++非常类似。

```
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        area(&rect1)
    );
}

fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```

### __<font color=blue>用Derived Traits打印struct内容</font>__    

__<font color=red>错误写法</font>__  
```
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {}", rect1);
}
```

__<font color=red>正确写法</font>__  

```
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {:?}", rect1);
}
```