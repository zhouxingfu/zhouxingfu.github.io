---
title: PE ELF文件格式浅析
date: 2020-10-09 17:25:15
tags:
categories: other
---

# __1 前言__  
 
在Windows平台，我们的exe dll 都是PE格式，在Linux平台是ELF格式（x86/arm等）。  

那种格式可以被OS识别甚至加载、启动都与OS息息相关。  

我一直有个疑问，就是为什么Windows 64位系统上可以跑32位应用，app可以同时跑在macos 32/64上？  
这两个，一个是OS问题，一个是硬件平台指令集问题。现在Windows arm上也可以跑x86应用。那么导致这些结果的原因是什么呢？

首先说Apple，使用的是一种叫做Fat Binary的技术，也就是我在编译程序的时候里面其实有包含了两套代码，同时支持x86/64，苹果现在宣布今后将把macos从x86迁移到arm平台，那么是要还是用Fat Binary技术吗？Fat Binary的优点和缺点都显而易见，那么还有其它方式吗？  


我们知道，Windows系统，32位应用可以在64位系统上运行，64位程序不能在32位系统上运行，这是为什么？  

__<font color=0xFF>ToDoList</font>__  

<!--more-->

1. Windows下 32位应用在64位系统上运行的原理，编译好的x86应用可以在Windows arm平台运行吗？是需要在编译的时候指定平台呢，还是可以直接通过OS内部的翻译器解决这个问题？x86_64 应用能运行在arm64平台上吗，实现的原理又是什么呢？ x86应用在64位系统上只能运行在user mode，所以驱动或者依赖于驱动的x86应用是没办法运行在64位系统上的，还有就是32位应用无法调用64位DLL，反之亦然，这是为什么呢？

2. Windows arm是如何实现的？现在指定了CPU和GPU，也就是说如果要换其他家arm芯片，就要重新适配是么？类似于苹果Mac arm也只能用自己的arm芯片？为什么x86 32位程序可以跑在Windows arm上？微软说这10.1之后就可以推送新的surface proc x II(arm平台)，支持64位x86应用跑在上面了，这又是怎么实现的呢？

3. Windows arm系统如何实现驱动开发？打个比方，比如杀毒软件该如何适配呢？是需要重新写一遍驱动呢，还是可以直接在上面跑，跟x86 x64应用的待遇一样？ 

4. 我们知道在Windows x86/x64平台，需要的驱动不同，也就是32位系统需要32位driver，64位系统需要64位driver，当然，我们也知道，32位软件可以在64位系统上跑（通过WOW机制），但驱动不可以，但我们看安全卫士 这类软件只有一个版本，没有根据操作系统选择，那么应该是32位程序，那就是说32位程序可以与64位driver sys通信，但32位程序无法调用64位DLL，这样的理解对吗？ 

5. 64位程序不能调用32位DLL，32位driver sys，但可以调用32位COM（通过进程间COM的方式）

6. 从2011年微软发布支持arm平台的Windows RT开始，已经接近十年了，Apple也将在11月发布第一款Mac arm。“第一次发生在上世纪九十年代中叶，Macintosh从Motorola的68k指令集迁移到了IBM的PowerPC指令集上，那段时间乔布斯并不在苹果。用乔布斯在WWDC 2005上面的话来说，这次切换为苹果奠定了未来十年的基础（指90年代中叶到2005年左右）。第二次是软件层面上的大换血，从Mac OS 9迁移到了具有BSD血统的Mac OS X上，这也是乔布斯一手主导的一次切换，让Mac的操作系统变得现代化，并打好了在未来二十年内发展的基础。第三次则是我们所熟知的从PowerPC架构的处理器迁移到Intel处理器上面，乔布斯在2005年的WWDC大会上面宣告了这次迁移。到今天为止，所有的Mac都已经换上了Intel的CPU，在系统层面上，macOS也早已完全抛弃掉了对PowerPC指令集、甚至于32位x86指令集（IA-32）的支持，在macOS 11之前，它只支持使用x86-64指令集的处理器。”那么桌面PC的未来甚至服务器的未来就是arm了吗？  arm server实现了云端同架构，但有没有这么一个问题，就是同样的应用在这个arm芯片上可以，在另外一个arm芯片上不可以？如果是android这样系统的话，应该没问题，因为大家的app在上面开发都是可以用的，就算底层的程序，应该问题也不大。至于为什么微软和Apple迁移那么难，是因为OS跟指令集绑定的太深了，而Linux因为开源的问题，所以很早就有人着手解决这个问题了。  


上面有6个问题，是我昨天想起来记下的，其实里面的问题里已经有了一些答案，现在结合我知道的，先讲一些实现原理，最后回答上面的问题。  



# __2 操作系统与指令集配合以及一些实例__  

指令集和程序通过操作系统交流，一般情况下，编译好的可执行程序里面是固定的指令集，比如我们在VS中指定了平台(x86/x64)，那么就会生成32/64位程序，虽然目前AMD的x64和x86是兼容的，但最开始Intel IA64和x86并不兼容，选择了某个平台，我们也就选择了将程序中的代码编译成某个指令集。操作系统根据自己定义好的格式，解析可执行文件，把指令发送给CPU执行。  
[单片机 指令集 和 操作系统的关系](https://blog.csdn.net/zhongjin616/article/details/18765301)  

操作系统可以支持多个指令集平台吗？我觉得从技术上是可以实现的。    
1. 比如从引导程序开始就做成Fat Binary这种形式，先判断当前是什么CPU架构，然后我的OS安装盘里就选择哪种平台版本的安装程序，但最终安装的操作系统里，是包含多个CPU平台支持的
2. 在运行可执行程序的时候，如果可执行程序本身是Fat Binary，那么就选择支持的指令集直接运行；如果不是，那么通过操作系统自带的翻译器翻译指令。  

但是这样有一个问题啊，就是操作系统和可执行程序都会非常臃肿。  
所以，上面提到的操作系统做成Fat Binary是不现实的，不同的架构，同一个系统安装文件，这得包含多少冗余啊。至于第二点，Apple的实现方式是Fat Binary，但Mac OS本身也没有Fat Binary。
```
NeXT的Mach内核所支持的Mach-O二进制文件格式引入了一种叫fat binary的特性，说白了就是在一个平台架构上分别交叉编译所有平台的二进制格式文件，然后把每个文件都打包成一个文件。Universal Binary就是指同时打包Intel平台和PowerPC平台的二进制文件。Mac OS X 10.4最终支持四个平台的BSD系统调用——32位Power PC、64位PowerPC、32位 x86和64位x86_64。作为最终用户，无须搞清这些区别，因为使用Universal Binary技术，买回来的软件直接会解出相应平台程序的二进制文件并执行。这是苹果很成功的一步——不像Windows系统中要用不同的路径（\Windows\System、\Windows\System32、\Windows\System64）分别存放不同架构的二进制库，并且用户还需在32位版和64位版之间犹豫不决。
```

苹果这么做的好处是，操作系统内部不需要实现类似于Windows emulator的机制，但坏处是应用体积会变得大，毕竟里面包含了很多平台的二进制程序。  

## __2.1 微软的实现思路__

### __2.1.1 如何实现64位系统上加载32位程序__  

```
intel X86架构CPU可能实现了多个指令集x86，x86-64，MMX，SSE，SSE2，SSE3，SSSE3 ，而这些指令集中的指令让cpu完成的动作都比较复杂，所以也称为CISC

AMD amd64架构的cpu 兼容了x86指令集还拓增了3D-Now!指令集，用于加强对3D显示的支持。

ARM ARMv1~ARMv7架构的cpu实现了Thumb指令集和ARM指令集。这些指令集中的一条指令让cpu完成的动作都比较简单，所以也称为RISC指令集
```
但32/64指令长度也不一样啊，一个是32位，一个是64位，这些该怎么办？  

[How Windows 64-bit Supports 32-bit Applications](https://www.techsupportalert.com/content/how-windows7-vista64-support-32bit-applications.htm)  
[Win10 ARM版如何运行x86程序？IT之家带你一文读懂](http://baijiahao.baidu.com/s?id=1567917475253638&wfr=spider&for=pc)  
[为什么同一个exe文件能在不同指令集CPU上运行？](https://www.zhihu.com/question/334122973)  
[如何看待微软的CHPE（arm运行x86）？](https://www.zhihu.com/question/52873651)  
[Running 32-bit Applications](https://docs.microsoft.com/en-us/windows/win32/winprog64/running-32-bit-applications)   

上面的链接详细介绍了  
1. Windows 64位系统如何跑32位应用？ 通过一个中间层 32位指令 <--> 转换器 <--> 64位指令。

{%asset_img 64call32.jpeg %}


2. Windows arm如何支持运行x86应用。通过emulator，比如  

{%asset_img arm64callx8632.jpeg %}

下面这些就是Windows提供的包含emulator的库

* Wow64.dll provides the core emulation infrastructure and the thunks for the Ntoskrnl.exe entry-point functions.
* Wow64Win.dll provides thunks for the Win32k.sys entry-point functions.
* (x64 only) Wow64Cpu.dll provides support for running x86 programs on x64.
* (Intel Itanium only) IA32Exec.bin contains the x86 software emulator.
* (Intel Itanium only) Wowia32x.dll provides the interface between IA32Exec.bin and WOW64.
* (ARM64 only) xtajit.dll contains the x86 software emulator.
* (ARM64 only) wowarmw.dll provides support for running ARM32 programs on ARM64.


10月底微软是释放出支持x64应用的Windows ARM系统。  

现在的制约因素是效率不行，因为要通过中间转码的方式，但为什么Mac ARM 就能这么厉害呢？ 有一个想法是Windows的前向兼容太多，导致系统做的比较臃肿，而Mac OS历来就是喜新厌旧，隔个几年就不支持之前的设备或者某些接口是常规操作。  


现在回答上面的问题  

1. 32位无法调用64位DLL，但可以使用64位driver sys。
通过上面的原理，我们知道64位系统跑32位程序是通过指令集32/64转换实现的，32位调用64位DLL该怎么办，难道我们要标记一下哪些需要进行指令集转换，哪些不需要吗？可是在实际使用过程中，exe调用DLL导出的function，本身就是一套流水线下来的，我们没办法在执行指令的过程中区分哪些指令要转64，哪些不用转64。  
至于driver sys，可以理解为进程间通信，相当于一个32位进程，一个64位进程，通过某些手段进行通信，这样也能解释64位程序如何调用32位DLL，那就是通过进程间COM，既然是不同的进程，那么要不要进行指令转，就是进程相关的了，相互独立的进程间不影响。

2. emulator是运行在用户态的，即user mode。

3. 驱动程序如果想适配Windows ARM，需要指定平台，重新编译，不能通过emulator。也就是说x86驱动不能直接在Windows ARM上跑，需要重新编译。这里有一个问题，就是x86驱动也只能编译为arm32 driver，arm32 driver可以在arm64平台上跑吗？当然不可以了，参见Windows 64系统不支持32位驱动。[使用 WDK 生成 ARM64 驱动程序](https://docs.microsoft.com/zh-cn/windows-hardware/drivers/develop/building-arm64-drivers)   

好，上面的6个问题基本上回答完了。在这里，有一个共识，就是arm平台将继续发扬光大，继续无往不利，而WinTel组合也将慢慢成为历史。以后芯片的种类、平台、定制化都将越来越丰富。  

