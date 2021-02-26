---
title: Diagnostics_Library(Error-Handling)
date: 2020-09-02 13:24:02
tags:
categories: STL
---
## __<font color=0xFFFFFF>前言</font>__

我们都知道在高级语言中都有异常处理语法（C没有语法，但有异常处理，不过这是程序员在代码层面自己实现的），今天我们来讨论异常处理机制。  

首先，我们来看CppCon 2019上Ben Saks关于Exception的演讲。

{% youtube W6jZKibuJpU %}

<!--more-->
__video time 1.05 EH is for Synchronous Program Errors__

“异常是同步的，是指异常发生的时候，CPU立即处理本次异常，直到异常处理结束之后才能继续进行接下来的任务。例如在进行程序调试的时候，添加一个断点，就必须在断点出发生异常，CPU立即处理，先暂停其工作，否则就无法查看断点处的程序运行信息。

通俗一点的将就是：中断异步就是我可以不用立即处理，而是等执行完一条指令时候才可能处理，异常同步是指出了异常必须立马处理。”

__video time 3:11 Returning Error Indicators__

C语言中没有异常处理，所有的错误都是通过返回值的方式来告知调用者。

但是通过返回值的方式通知 变得越来越臃肿  在很多情况下，接收返回值的函数不能处理错误，只能一步步往上传。这确实把错误检测和错误处理解耦了，但代价是高昂的，通过call chain传递错误码增加了分支处理，源代码增加，程序变大，可读性也变差。 

而且，很多情况下我们会忘记检查错误码（这本身就不鲁棒）。

但什么时候用返回码，什么时候用异常呢？  

__video time 8:11 Exception Handling throw catch try__  

throw exception, the exception can be primitive values, but perferable to throw objects of class types.  

base <exception>
derived <stdexcept>  : invalid_argument out_of_range overflow_error  
other derived exception defined in <new> <typeinfo>

__video time 10:56 throw by value, catch by reference__  

```
    try{
        throw "error";
    }catch(std::exception& e){
        std::cout << e.what() << std::endl;
    }catch(int& a){
        std::cout << a << std::endl;
    }catch(std::string& msg){
        std::cout << std::string(msg) << std::endl;
    }catch(const char* msg){
        std::cout << std::string(msg) << std::endl;
    }
    catch(...){
        std::cout << "catch errors" << std::endl;
    }
```
__video time 14:59 "Unwinding" the Stack__  

没看懂，很尴尬
__video time 19:58 nocept__  

void f() noexcept;

noexcept不会在编译时起作用；如果一个noexcept实际上throw exception，那么std::terminate会被调用。  

所以，确保声明为noexcept的函数不会throw exception。  

这里有一个疑问，如果确信不会throw了，那么声明noexcept的意义在哪里？有利于编译器优化。

noexcept还可以作有条件选择，比如 void swap(Type& x, Type& y) noexcept(noexcept(x.swap(y)))，如果x.swap(y)不发生异常，那么swap(Type& x, Type& y)一定不发生异常。  

鼓励使用noexcept的情形：
* move constructor
* move assignment
* destructor
* Leaf Function 叶子函数是指在函数内部不分配栈空间，也不调用其它函数，也不存储非易失性寄存器，也不处理异常。  


__video time 23:58 Exception Safety__  

* the basic guarantee
* the strong guarantee
* the noexcept guarantee  

__video time 31:05 RAII__


还有Herb Sutter 2019 ACCU Conference上的演讲   
 KEYNOTE: De-fragmenting C++: Making exceptions more affordable and usable - Herb Sutter [ACCU 2019]

{% youtube os7cqJ5qlzo %}

Jon Kalb "Exception-Safe Code, Part I"
{% youtube W7fIy_54y-w&list=PLHTh1InhhwT7esTl1bRitiizeEnksGU7J&index=84 %}


Jon Kalb "Exception-Safe Code, Part II"
{% youtube b9xMIKb1jMk&list=PLHTh1InhhwT7esTl1bRitiizeEnksGU7J&index=83 %}

Jon Kalb "Exception-Safe Code, Part III"
{% youtube MiKxfdkMJW8&list=PLHTh1InhhwT7esTl1bRitiizeEnksGU7J&index=82 %}

在Jon Kalb的演讲里也提到了 the basic guarantee the strong guarantee the nothrow guarantee 这三个概念，无论哪种概念都要首先满足两点  
* 异常发生后，不能有内存泄漏
* 异常发生后，不允许数据结构恶化，比如 Object* p = new Object("test") 此时因为new异常，那么我们不知道此时到底分配了多少内存（对象构造到什么程度了），或者说p指向的是一块销毁的区域。

保证没有内存泄漏很简单，靠RAII的锁机制。  std::unique_lock m(&mtx)
保证第二点有一个，可以通过
std::shared_ptr<Object> ptr_obj_;
ptr_obj_.reset(new Object("test"))  
此时当new Object产生异常的时候，根本不会走到reset中，也就不会对shared_ptr产生影响。  
美中不足的是，如果是通过拷贝构造或者复制构造的方式来reset  
ptr_obj_.reset(new Object(old_object))  
如果new Object抛出异常，那么old_object到底会发生什么变化呢？此时，就不会满足strong guarantee了（要么成功，即使失败，也要像完全没调用过那样）。

那么该怎么解决这个问题呢？ _<font color=blue>copy and swap</font>_  
"copy and swap"类似于buffer的概念，在某篇博文中是这么讲述的
```
有一种通常的设计策略可以有代表性地产生强力保证，而且熟悉它是非常必要的。这个策略被称为 "copy and swap"。它的原理很简单。先做出一个你要改变的对象的拷贝，然后在这个拷贝上做出全部所需的改变。如果改变过程中的某些操作抛出了异常，最初的对象保 持不变。在所有的改变完全成功之后，将被改变的对象和最初的对象在一个不会抛出异常的操作中进行交换。 这通常通过下面的方法实现：将每一个对象中的全部数据从“真正的”对象中放入到一个单独的实现对象中，然后将一个指向实现对象的指针交给真正对象。这通常 被称为 "pimpl idiom"，Item 31 描述了它的一些细节。
```

```
struct PMImpl { // PMImpl = "PrettyMenu
　std::tr1::shared_ptr<Image> bgImage; // Impl."; see below for
　int imageChanges; // why it’s a struct
};

class PrettyMenu {
　...

private:
　Mutex mutex;
　std::tr1::shared_ptr<PMImpl> pImpl;
};

void PrettyMenu::changeBackground(std::istream& imgSrc)
{
　using std::swap; // see Item 25

　Lock ml(&mutex); // acquire the mutex

　std::tr1::shared_ptr<PMImpl> // copy obj. data
　pNew(new PMImpl(*pImpl));

　pNew->bgImage.reset(new Image(imgSrc)); // modify the copy
　++pNew->imageChanges;

　swap(pImpl, pNew); // swap the new
　// data into place

} // release the mutex
```

 　在这个例子中，我选择将 PMImpl 做成一个结构体，而不是类，因为通过让 pImpl 是 private 就可以确保 PrettyMenu 数据的封装。将 PMImpl 做成一个类虽然有些不那么方便，却没有增加什么好处。（这也会使有面向对象洁癖者走投无路。）如果你愿意，PMImpl 可以嵌套在 PrettyMenu 内部，像这样的打包问题与我们这里所关心的写异常安全的代码的问题没有什么关系。

　　 copy-and-swap 策略是一种全面改变或丝毫不变一个对象的状态的极好的方法，但是，在通常情况下，它不能保证全部函数都是强力异常安全的。因为如果在changeBackground中我们调用了其他函数比如func()，如果func()不能保证strong guarantee，那么changeBackground即使我们写的再好，也不能保证strong guarantee，俗称“一颗老鼠屎，坏了一锅粥”，就是这样的道理。

有一篇讲述copy-and-swap的很好的文章 [【C++深入探索】Copy-and-swap idiom详解和实现安全自我赋值](https://blog.csdn.net/xiajun07061225/article/details/7926722)

[C++ 拷贝构造函数和赋值构造函数](https://blog.csdn.net/qq_36553031/article/details/89057433)

其实copy-and-swap的思想确实很好，但也有一个疑问，就是这样会不会造成性能的损失？


__疑问：但是basic guarantee跟strong guarantee相比，缺了什么呢？__

## __<font color=0xFFFFFF>参考资料</font>__
[Error handling](https://en.cppreference.com/w/cpp/error)
[中断是异步的，异常是同步的](https://blog.csdn.net/weixin_37817539/article/details/102421184)
[C++箴言：争取异常安全的代码](https://blog.csdn.net/weixin_34082789/article/details/85429805)
[【C++深入探索】Copy-and-swap idiom详解和实现安全自我赋值](https://blog.csdn.net/xiajun07061225/article/details/7926722)
[C++箴言：争取异常安全的代码](https://blog.csdn.net/weixin_34082789/article/details/85429805)