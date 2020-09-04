---
title: const in c++
date: 2020-09-02 22:11:04
tags:
categories: C++
---

在C++中，const用来表示常量、不变的涵义。  

我们先看下面的例子。

```
int func_1() {return 6;}
int func_2(int x){
    int temp = x;
    return temp;
}
string func_3(){
    return "hello";
}
string func_4(){
    string temp = "hello";
    return temp;
}
typedef struct HaHa{
    int a;
    float b;
    char c;
}*pHaHa;
HaHa func_5()
{
    return {1, 1.0f, 'a'};
}
const HaHa func_6(){
    return {1, 1.0f, 'a'};
}
func_1() = 1;              //error   
func_2() = 2;              //error
func_3() = "3";            //error
func_4() = "4";            //right
func_5() = {2, 2.0f, 'b'}; //right
func_6() = {2, 2.0f, 'b'}; //error: passing ‘const haha’ as ‘this’ argument of ‘haha& haha::operator=(haha&&)’ discards qualifiers [-fpermissive]
```

目前已知的规则，有如下几个

|规则|
|:----|
|const修饰变量，value不可修改|
|指针使用const，指针自身不可修改 int * const p 修饰的是指针； const int *p 修饰的是变量； const int* const p; 二者皆被修改 |
|const 修饰函数参数，参数在函数内不可修改|
|const指针所指向的内容不可修改 void func(const char* var)|
|参数为引用，为了增加效率同时防止修改 void func(const Type& var)|

现在我们单独说一说 __<font color=red>const修饰函数返回值</font>__ 的情况。  

如果返回值为某个对象的const或者某个对象的引用const，则返回值具有const属性，则返回实例只能访问A中的public protect数据成员和const成员函数，并且 __<font color=red>不允许对其进行赋值操作</font>__ 。  

上面的例子中，返回值为int的都是右值，看汇编中是直接把结果放到%rax中，而寄存器我们都知道是没有地址的。

区分左值和右值的一种简单方法是看能否对该值取地址&。  

但为什么返回值是string的时候就可以了呢？

在[C++：函数返回值与临时变量](https://blog.csdn.net/qq_22660775/article/details/89854545) 介绍了，一般函数返回值是非引用类型的时候，函数会创建临时对象，函数返回的就是这个临时对象，如果有=，那么就用临时变量初始化=左边的参数。  

但返回值为非引用int这种类型的时候，是没有构造临时对象的，为什么呢？满足什么条件的返回值是不会构造临时对象的呢？不能直接用寄存器表示，必须放在内存中的value，就必须构造临时对象；可以直接放在寄存器中的value，就不会构造临时对象。  

可以像上面这么理解吗？  

现在回到另外一个问题，为什么const 临时对象就不可以呢？ 明明临时对象就是具有const属性，我们const = const不可以吗？  

问题还是出在到底这个类型的运算符重载=到底是怎么实现的？  

现在问题是上面提到的不允许对某个对象的const或const引用进行赋值操作？ 因为如果返回值是const，那么重载运算符=的返回值也要是const，这样会导致我们限定死了返回值不可被修改，它不能访问非const成员和变量。而一般情况下=的重载返回值都没有const限定符，因为这样返回对象就只能访问const成员和变量。  


