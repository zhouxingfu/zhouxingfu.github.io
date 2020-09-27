---
title: delete之后发生了什么
date: 2020-09-27 20:20:53
tags:
categories: C++
---
```
char* p = new char[1024];
delete p;

cout << p[0] << endl;
```

上面这段代码会崩溃吗？  

不一定，delete是释放堆空间，但实际上内存只是被回收了，p还是指向那块区域，所以我们还可以继续使用，但这块空间的value有可能已经不是我们原来的value了，因为如果这块空间被重新分配给其它变量的话，有可能就会读写这块空间，value就会被改变。
  


