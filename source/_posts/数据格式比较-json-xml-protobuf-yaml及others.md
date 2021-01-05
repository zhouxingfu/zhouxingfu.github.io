---
title: 数据格式比较 json xml protobuf yaml及others
date: 2021-01-04 11:14:06
tags:
categories: other
---

在开发过程中，我们会把自己的数据做一些定义，在程序中表示是数据结构，在本地保存成文件时，就要在文件中定义格式。  

那么数据以什么样的形式组织比较好呢？  

首先，要明确需求，也就是我们关注的点有哪些？

* 当前数据集的特点
* 有没有好的第三方库（稳定，bug少，支持特性多，漏洞少，接口友好）
* 效率（内存等，不同使用场景下也许不同库存在不同，比如A库在某些场景下优于B，B在某些场景下优于A）







## 参考资料

 [A Comparison Of Serialization Formats](https://blog.mbedded.ninja/programming/serialization-formats/a-comparison-of-serialization-formats/)