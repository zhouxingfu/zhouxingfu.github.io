---
title: Rust(7) Managing Growing Projects with Packages Crates and Modules
date: 2020-09-11 16:46:45
tags:
categories: Rust
---

## __<font color=0xFFFFFF>Package Crate Module</font>__  
Rust中也有包的概念，其实跟我接触的Python差不多，有人说跟go挺像的也。

Package里可以有0或者1个lib crate，可以有若干个binary crate。  

_<font color=red> 代码1</font>_
```
#[cfg(test)]
mod hh {
    #[test]
    pub fn  it_works() {
        assert_eq!(2 + 2, 4);
    }
}
pub fn hehe() {
    crate::hh::it_works();
}
```
_<font color=red> 代码2</font>_
```
mod hh {
    pub fn  it_works() {
        assert_eq!(2 + 2, 4);
    }
}
pub fn hehe() {
    crate::hh::it_works();
}
```

<!--more-->
我们先看看上面两段代码，代码1编译报错
```
error[E0433]: failed to resolve: maybe a missing crate `hh`?
  --> src/lib.rs:11:12
   |
11 |     crate::hh::it_works();
   |            ^^ maybe a missing crate `hh`?

error: aborting due to previous error

For more information about this error, try `rustc --explain E0433`.
error: could not compile `my_lib`.
```
代码2编译就是正常的。  

唯一的区别是缺少 #[cfg(test)]，这个代表什么意思呢？表明这是一个测试mod，里面的函数是测试函数，在cargo build的时候会被忽略，只有cargo test才会被编译，所以代码1在编译的时候会提示找不到hh。  


|特性|Rust|备注|
|:----|:----|:----|
|rs文件有能否有多个mod|可以||
|mod或者fn如何被外界访问|rs文件中最高层的mod是默认public，除此之外所有的mod和其他结构都是private||
|在外部如何使用|用use，类似于Python import||


### __<font color=0xFFFFFF>拆分模块到不同的文件</font>__  

这一点类似于Java，com.sensetime.stmobile.humanaction这种形式的，每一层都是文件夹。  

    src   
        -- lib.rs ()
        -- front_of_house  (文件夹)
            -- hosting.rs

下面我们列举出所有代码  

_<font color=red>src/lib.rs</font>_
```
mod front_of_house;
pub use crate::front_of_house::hosting;
pub fn eat_at_restaurant(){
    hosting::add_waitlist();
    hosting::add_waitlist();
    hosting::add_waitlist();
}
```

_<font color=red>src/front_of_house/hosting.rs</font>_ 
```
pub fn add_to_waitlist(){}
```  
可编译的时候还是报错，为什么呢？就是在src目录下还需要一个front_of_house.rs  
```
pub mod hosting;
```
这是跟Java不一样的地方，也就是除了目录之外，我必须还要声明一个文件夹对应的文件，里面列举了我实现的mod。  


