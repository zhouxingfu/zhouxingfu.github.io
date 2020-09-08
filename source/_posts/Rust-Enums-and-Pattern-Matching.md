---
title: Rust Enums and Pattern Matching
date: 2020-09-08 22:02:24
tags:
categories: Rust
---
正常情况下，我们声明enum的格式如下
```
fn main() 
{
    enum IpAddrKind {
        V4,
        V6,
    }

    struct IpAddr {
        kind: IpAddrKind,
        address: String,
    }

    let home = IpAddr {
        kind: IpAddrKind::V4,
        address: String::from("127.0.0.1"),
    };

    let loopback = IpAddr {
        kind: IpAddrKind::V6,
        address: String::from("::1"),
    };
}
```
这种写法本质上跟C的语法一样，但在Rust中还有一种更加简洁的写法
```
fn main() {
    enum IpAddr {
        V4(String),
        V6(String),
    }

    let home = IpAddr::V4(String::from("127.0.0.1"));

    let loopback = IpAddr::V6(String::from("::1"));
}
```
在上面，我们可以直接把enum绑定到一个类型上，也就是enum中的element对应的必须是某个类型的value。  

但是上面的code中，home和loopback是什么类型，我们该怎么访问或者使用它呢？  

在[How do you access enum values in Rust?](https://stackoverflow.com/questions/9109872/how-do-you-access-enum-values-in-rust)通过match，可以得到内部的数据。

let (x, y) = Shape::Circle(Point { x: 0.0, y: 0.0 }, 10.0);
```
struct Point {
    x: f64,
    y: f64,
}

enum Shape {
    Circle(Point, f64),
    Rectangle(Point, Point),
}

fn main() {
    let my_shape = Shape::Circle(Point { x: 0.0, y: 0.0 }, 10.0);

    match my_shape {
        Shape::Circle(_, value) => println!("value: {}", value),
        _ => println!("Something else"),
    }
}
```

在这里，我们会想到在开始介绍Rust的时候，有一个特性<font color=red>destructuring解构</font>，那么我们可以通过destructuring来解析数据吗？比如，像下面这么写
```
let my_shape = Shape::Circle(Point { x: 0.0, y: 0.0 }, 10.0);
let (x, y) = my_shape; //error, cause my_shape is enum, but is not a tuple
```

