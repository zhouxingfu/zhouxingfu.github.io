---
title: Rust Enums and Pattern Matching
date: 2020-09-08 22:02:24
tags:
categories: Rust
---

## __<font color=0xFFFFFF>介绍</font>__
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

```
enum Message 
{
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
fn main() {}

```
* Quit has no data associated with it at all.
* Move includes an anonymous struct inside it.
* Write includes a single String.
* ChangeColor includes three i32 values.


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

## __<font color=0xFFFFFF>Option\<T>和null reference</font>__  


_<font color=green>
我称之为我的十亿美元错误……当时，我正在设计第一个全面的类型系统，用于面向对象语言中的引用功能。我的目标是确保所有对引用的使用都是绝对安全的，由编译器自动执行检查。但是我无法拒绝定义一个 Null 引用的诱惑，因为它实在太容易实现了。这导致了无数错误、漏洞和系统崩溃。在过去的四十年里，这些问题可能已经造成了十亿美元的损失。 ——托尼·霍尔，ALGOL W 的发明者。</font>_


我所理解的NULL，比如在C++创建一个对象指针，或者Java中创建一个对象引用，都会用到null。C++中的指针，Java中的引用，在我看来都是一回事，都是指向某块内存，这块内存是某个对象类型的。  

当我们在操作对象的时候，指针或者引用有可能无效，不论是主动的还是被动的，这都会导致问题，使用null可以快速地让我们标记一个不可用的对象或者对象引用。但很显然，并没有编译器自动检查这回事，我们要在自己的代码中强行检查，否则我们无法判断当前使用的指针或者引用是否有效  
C++   
```
if(nullptr == p){}
```
JAVA
```
if(str==null || str.equals("")){}
```
C#
```
if(string.IsNullOrEmpty(str)){}
```
为了安全性，我们要对代码中所有用到的地方加上对象是否为null的判断，null可以表示任何类型的空对象，但我们又不能确保依赖的第三方是否能做到这样，所以在C++和java中，我们能看到很多错误都是null引起的。  

这里有两篇关于null的介绍，可以仔细看一看。   
[NULL-计算机科学上最糟糕的失误](https://www.cnblogs.com/math/p/null.html)  
[Java中有关Null的9件事](https://blog.csdn.net/IT_ORACLE/article/details/54691671)  
[Null：一个价值 10 亿美元的错误，真的很后悔设计了它](https://zhuanlan.zhihu.com/p/157621373)  


__<font color=gray>Rust的解决方法</font>__

可不用null，该怎么表示空引用呢？  

Rust采用
```
enum Option<T>{
    Some(T),
    None,
}
```
这里我们用了泛型，也就是每一个类型，我们都有一个对应的null，比如
```
fn main() {
    let some_number = Some(5);
    let some_string = Some("a string");

    let absent_number: Option<i32> = None;
}
```
_注意：None和Some可以直接调用，而不用通过Option::，这是什么原因呢？_

这么做的好处是，Option<T>和T不是同一种类型，所以不能进行各种操作，必须得把Option<T>转成T才可以。但这样的意义是什么呢？我们的引用在编译阶段就能检查。  

## __<font color=0xFFFFFF>match</font>__  
match类似于switch case  

```
#[derive(Debug)]
enum UsState {
    Alabama,
    Alaska,
    // --snip--
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);
            25
        }
    }
}

fn main() {
    value_in_cents(Coin::Quarter(UsState::Alaska));
}
```  

__<font color=red>if let 语法</font>__  

if let 与 if的区别在哪里呢？
