---
title: gtest学习
date: 2021-01-05 10:53:44
tags:
categories: tools
---

如果让我设计一个测试框架，我会怎么弄？

我有几个疑问：  
* 是面向接口测试，还是面向功能测试？比如我要实现某个功能X，可能要调用接口A，接着B，最后C。当然可以采用test suite的概念，还是按照接口写test case，但一些test case可以组成test suite
* test case能否做到与input output无关？比如A->B A->C ， 这两个test suite，给A的输入不同，输出也不同，有人说那你test suite组合起来就好了，接口可以做到跟输入分离，可如果是单独测试A接口呢？有单独的input？ 这倒是也可以。

这么一想，问题倒是都能解决，接口测试为基本的test case，接口与输入、输出分离，比如可以有config，不是写死的状态；根据功能性，某些test case可以组成test suite，这里面接口用到的input output也是不同的。  

接下来我们看看Googletest的使用以及实现原理吧。  

## __1 大体思路__

gtest的github repository里有gtest和gmock，gmock可以认为是依托gtest实现了更高级的功能（现在还没看，后面写sample）。编译成功之后，会生成libgtest.a libgmock.a libgtest_main.a libgmock_main.a，这里libgtest_main.a 其实是包含了以下代码

    int main(int argc, char* argv[])
    {
        testing::InitGoogleTest(&argc, argv);
        return RUN_ALL_TESTS();
    }

这样我们写unit_test.cpp就可以了，然后直接编译进去，生成可执行，然后直接跑就会进入到libgtest_main.a的main函数里，跑所有的test。  


## __2 正式集成__

### __2.1 现有工程的TestSuite__  

今天看公司代码里的测试用例，全部是按照功能来写的，大概类似于

    TEST(TestSuiteName, TestCaseName)
    {
        Init();
        Start();
        Stop();
        Destroy();
    }

这样按照功能来写TestCase是可以的，这样即使例子调用的接口完全相同，因为输入的不同也可以是不同的TestCase。
