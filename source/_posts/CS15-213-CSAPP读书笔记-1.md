---
title: 'CS15-213:CSAPP读书笔记(1)'
date: 2022-04-26 21:48:25
tags:
categories: [CSAPP]
---

# 为什么要学习这本书？

[Linux C++ 服务器端这条线怎么走？一年半能做出什么？ - 陈硕的回答 - 知乎](https://www.zhihu.com/question/22608820/answer/21968467)

[如何阅读《深入理解计算机系统》这本书？ - whereisKathy的回答 - 知乎](https://www.zhihu.com/question/20402534/answer/824172785)

[MIT6.828 - Operating System Engineering - 操作系统公开课 - whereisKathy的文章 - 知乎](https://zhuanlan.zhihu.com/p/145984455)


CS15213 参考资料 
[CMU 15213/15513 CSAPP 深入理解计算机系统 Lecture 01 Course Overview 中英字幕](https://www.youtube.com/watch?v=ScMxnXq6fbI&list=PLcQU3vbfgCc9sVAiHf5761UUApjZ3ZD3x) 
[CS15213 官网](http://www.cs.cmu.edu/~213/)

MIT 6.828 参考资料 
[Learning Material](https://github.com/yinfredyue/MIT6.828)   
[labs](https://github.com/yinfredyue/MIT6.828-lab)  
[xv6](https://github.com/yinfredyue/MIT6.828-xv6-public)

[CSAPP](https://hansimov.gitbook.io/csapp/)


# 学习路线是什么

先学习《深入理解计算机系统》，然后学习《操作系统》。


# 日程安排 

6.30日之前尽可能完成，因为过了这天之后有重要的事情要发生。



# 学习方法

看国外公开课，看国外教材，做课后作业，使用Google，多思考，多动手，多讨论。

直到今天才发现，过河摸TM什么石头啊，高速公路都建好了，开车过去就行了，少自欺欺人。


# 课程总览


## Chapter 2 信息的表示与存储  

### __Q1 我们都知道CPU处理的是补码，那么它是怎么知道结果是正还是负的？另外不是还有一些指令是区分正负的吗？标志寄存器里存储的是结果的正负值吗？__ 

[3 万字 51 张图教你 CPU、内存、操作系统硬核知识！](https://baijiahao.baidu.com/s?id=1660827036098411381&wfr=spider&for=pc)  
__上面这个链接里的文章当个科普就好，最起码里面关于标志寄存器的介绍就是错误的，标志寄存器里不存储数据是正数 负数。[标志寄存器](https://baike.baidu.com/item/%E6%A0%87%E5%BF%97%E5%AF%84%E5%AD%98%E5%99%A8/5757541?fr=aladdin)__  


我目前的理解是：
1. 在程序中读取了一个变量，假设是有符号int类型，系统或者解释器会先翻译成补码
2. 运算之后的结果也是补码。如果一个signed和unsigned运算，二者会先有一个规则，就是统一按照unsigned还是signed来转换成补码，然后再运算，也就是说CPU在拿到数据的时候，只知道这是补码，至于类型什么的都是在传给CPU之前就处理好了。
3. 结果是补码，如果我们想把结果赋值给一个变量（假设这个变量是个栈中的临时变量），此时会把补码按照最终要赋值给的类型来做转换。 


### __Q2 char数据左移8位的结果为什么有可能不是0？__  

在这里我曾经百思不得其解，但现在我突然意识到一个问题，那就是虽然C++是高级语言，但仍要了解底层是如何实现的，因为在可 __正常__ 范围内，抽象层和底层的预期一致，我们不用关心


