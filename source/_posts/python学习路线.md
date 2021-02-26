---
title: Python学习路线
date: 2020-10-10 20:33:26
tags:
categories: Python
---

# __1 前言__
编程语言是特性的合集，比如for while，很多语言都有，lambda表达式也是，语言也在不断发展，协程的概念出来之后，很多语言也就着手修订新的标准（特性或者标准库）。如果从这个角度来理解，作为一个开发者，我们所掌握的其实是 算法 + 架构 + 语言特性，算法是可以完全抽离的，架构从宏观的角度上看也可以抽象出来，但因为不同语言生态差异，同一种架构用不同语言开发的成本千差万别，比如后端业务逻辑我们用C++的成本远远大于Java。至于语言特性，作为一个大的set，每一种语言从中挑选若干特性组合起来。至于为什么每个语言的写法不同，但即使两种语言有完全相同的特性，因为底层机制的不同，性能和写法差异也很大，比如Python这种胶水语言就比C++写的方便多了，还有Java C#也明显比C++写起来快，它们底层的虚拟机屏蔽了系统的细节，比如内存管理。

下面开始正经的学习一下Python。

声明一下本篇为自己参考[Python 100天](https://github.com/jackfrued/Python-100-Days/blob/master/Day01-15/12.%E5%AD%97%E7%AC%A6%E4%B8%B2%E5%92%8C%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F.md) 做的学习笔记。

<!--more-->


# __2 我所理解的语言特性__

语言是特性的集合。

## __2.1 运算符__

标配。转自Python-100-Days
| 运算符                                                       | 描述                           |
| ------------------------------------------------------------ | ------------------------------ |
| `[]` `[:]`                                                   | 下标，切片                     |
| `**`                                                         | 指数                           |
| `~` `+` `-`                                                  | 按位取反, 正负号               |
| `*` `/` `%` `//`                                             | 乘，除，模，整除               |
| `+` `-`                                                      | 加，减                         |
| `>>` `<<`                                                    | 右移，左移                     |
| `&`                                                          | 按位与                         |
| `^` `|`                                                      | 按位异或，按位或               |
| `<=` `<` `>` `>=`                                            | 小于等于，小于，大于，大于等于 |
| `==` `!=`                                                    | 等于，不等于                   |
| `is`  `is not`                                               | 身份运算符                     |
| `in` `not in`                                                | 成员运算符                     |
| `not` `or` `and`                                             | 逻辑运算符                     |
| `=` `+=` `-=` `*=` `/=` `%=` `//=` `**=` `&=` `|=` `^=` `>>=` `<<=` | （复合）赋值运算符             |

is、is not、in、not in 

在Python中is是用来表示内存地址（引用）的一致性；in是值判断，同时我们要注意嵌套内容是无法判断的，比如 4 in [1, [1,4]]就是False，[1,4] in [1, [1,4]] 是True。

## __2.2 分支及循环__  
if for while就是我们所说的分支和循环，在C++中，我们会用上面的运算符来作为分支的判断条件，在Python中稍有不同。  

* 如果我们明确知道循环的次数，或者要对一个容器进行迭代，那么建议使用for-in结构
* 如果要构造不知道具体循环次数的循环结构，我们推荐使用while循环。while循环通过一个能够产生或转换出bool值的表达式来控制循环，表达式的值为True则继续循环；表达式的值为False则结束循环。

用jupyter写test case的时候，下意识用了 i++ i--结果发现Python中没有这个语法，后来想了下Python中并没有这样的语法，同时也不明白为什么C/C++中要有自增自减运算符，是为了提升效率吗？ 不得而知。  


## __2.3 函数 类__  

### __2.3.1 函数__

先说函数，函数包含函数名称，参数，以及返回值，在Python中不显式指明返回值类型，所以我们关注的重点是参数。

```
关于函数返回值，Python最开始是不会直接声明返回值类型的，一般都要看API docstring，如果我们用到了一个文档很差的库，只能看源码了。当然，不直接声明返回值类型主要也是因为有些function的返回值类型并不确定，比如我们可以返回int，也可以返回list，tuple，但return type并不确定的时候，函数声明中也就没必要写了。后来Python也发现，有的时候还是需要return type的，或者说我们写的大部分函数的return type都是明确的，在声明的时候明确return type也能让使用者更加方便。

下面这种写法就是明确了return type，不过这个是3.5之后才支持的

def greeting(name: str) -> str:
  return 'Hello, {}'.format(name)
```

接着谈参数，Python的参数大概有两种，一种叫position argument，一个类似于keyword argument，这两个都好理解，一个是根据位置，一个是根据参数名，那我们在传参的时候到底是根据position还是根据keyword呢？

我这边有一个简单的想法就是，无论我们以何种方式传参，都不能让函数调用的时候产生歧义，最终要跟函数声明一致，另外Python还是支持动态参数，类似于C++中的argc和argv。


## __2.4 字符串和常用数据结构__  

### __2.4.1 字符串__  

要说起字符串，那真的是五味杂陈，使用频率特别高，操作特别多，而且还涉及到不同的编码格式，我能想到的  
* 切片
* 

切片的话，需要知道start和end，一般有find_first_of find_last_of

还有什么呢？

leetcode上那些字符串算法其实在char数组上也适用，所以所谓的字符串算法跟字符串关系不大。

### __2.4.2 列表__  

列表也可以切片，跟string不同的是，string是immutable，list是mutable。  

### __2.4.3 [生成式与生成器](https://docs.python.org/3/tutorial/classes.html#generators)__ 

[x for x in range(1, 10)] 是生成式  
(x for x in range(1, 10)) 是生成器  

__<font color=green size=4>yield构造生成器</font>__  

如何理解yield呢，我的理解 yield = return_pause 

这里的意思是，yield构成的是一个generator，next的时候开始执行程序，直到遇到yield就把yield后面的value返回，同时整个程序pause，如果再次调用next，那么从当前pause的地方开始继续执行，直到遇到下一个yield语句。  

下面是一个例子  
```PYTHON
  def fib(n):  
      a, b = 0, 1  
      for _ in range(n):  
          a, b = b, a+b  
          yield a  
          yield "hello "  

  def main():  
      for x in fib(5):  
          print(x)  

  if __name__ == '__main__':  
      main()  
```

在上面的例子中，generator的返回值甚至可以不是同一种类型，感觉这种写法有两个好处 
1. 能够写更复杂的generator，比(x for x in range(100))这种复杂多了。  
2. 不用非得保存成list或者tuple，然后for 遍历，像上面的fib例子，如果我们的正常写法，有可能是fib的结果保存成一个list，然后返回，最后我们for遍历这个list，但我们直接用yield就省略了中间环节，直接输出我们想要的结果。  

### __2.4.4 元组__   

元组可以储存不同乐行的变量，不过元组是immutable。既然我们有了list，为什么还需要tuple？  

下面转自Python-100-days 

```
这里有一个非常值得探讨的问题，我们已经有了列表这种数据结构，为什么还需要元组这样的类型呢？

元组中的元素是无法修改的，事实上我们在项目中尤其是多线程环境（后面会讲到）中可能更喜欢使用的是那些不变对象（一方面因为对象状态不能修改，所以可以避免由此引起的不必要的程序错误，简单的说就是一个不变的对象要比可变的对象更加容易维护；另一方面因为没有任何一个线程能够修改不变对象的内部状态，一个不变对象自动就是线程安全的，这样就可以省掉处理同步化的开销。一个不变对象可以方便的被共享访问）。所以结论就是：如果不需要对元素进行添加、删除、修改的时候，可以考虑使用元组，当然如果一个方法要返回多个值，使用元组也是不错的选择。
元组在创建时间和占用的空间上面都优于列表。我们可以使用sys模块的getsizeof函数来检查存储同样的元素的元组和列表各自占用了多少内存空间，这个很容易做到。我们也可以在ipython中使用魔法指令%timeit来分析创建同样内容的元组和列表所花费的时间，下图是我的macOS系统上测试的结果。
```  

```
# 定义元组
t = ('骆昊', 38, True, '四川成都')
print(t)
# 获取元组中的元素
print(t[0])
print(t[3])
# 遍历元组中的值
for member in t:
    print(member)
# 重新给元组赋值
# t[0] = '王大锤'  # TypeError
# 变量t重新引用了新的元组原来的元组将被垃圾回收
t = ('王大锤', 20, True, '云南昆明')
print(t)
# 将元组转换成列表
person = list(t)
print(person)
# 列表是可以修改它的元素的
person[0] = '李小龙'
person[1] = 25
print(person)
# 将列表转换成元组
fruits_list = ['apple', 'banana', 'orange']
fruits_tuple = tuple(fruits_list)
print(fruits_tuple)
```


### __2.4.5 set__  

在这里没有multiset，不允许有重复元素。  
```
# 创建集合的字面量语法
set1 = {1, 2, 3, 3, 3, 2}
print(set1)
print('Length =', len(set1))
# 创建集合的构造器语法(面向对象部分会进行详细讲解)
set2 = set(range(1, 10))
set3 = set((1, 2, 3, 3, 2, 1))
print(set2, set3)
# 创建集合的推导式语法(推导式也可以用于推导集合)
set4 = {num for num in range(1, 100) if num % 3 == 0 or num % 5 == 0}
print(set4)
```  

向集合添加元素和从集合删除元素。
```
set1.add(4)
set1.add(5)
set2.update([11, 12])
set2.discard(5)
if 4 in set2:
    set2.remove(4)
print(set1, set2)
print(set3.pop())
print(set3)
```
集合的成员、交集、并集、差集等运算。
```
# 集合的交集、并集、差集、对称差运算
print(set1 & set2)
# print(set1.intersection(set2))
print(set1 | set2)
# print(set1.union(set2))
print(set1 - set2)
# print(set1.difference(set2))
print(set1 ^ set2)
# print(set1.symmetric_difference(set2))
# 判断子集和超集
print(set2 <= set1)
# print(set2.issubset(set1))
print(set3 <= set1)
# print(set3.issubset(set1))
print(set1 >= set2)
# print(set1.issuperset(set2))
print(set1 >= set3)
# print(set1.issuperset(set3))
```
    说明： Python中允许通过一些特殊的方法来为某种类型或数据结构自定义运算符（后面的章节中会讲到），上面的代码中我们对集合进行运算的时候可以调用集合对象的方法，也可以直接使用对应的运算符，例如&运算符跟intersection方法的作用就是一样的，但是使用运算符让代码更加直观。



### __2.4.6 字典__  

字典类似于C++中的map。

```
# 创建字典的字面量语法
scores = {'骆昊': 95, '白元芳': 78, '狄仁杰': 82}
print(scores)
# 创建字典的构造器语法
items1 = dict(one=1, two=2, three=3, four=4)
# 通过zip函数将两个序列压成字典
items2 = dict(zip(['a', 'b', 'c'], '123'))
# 创建字典的推导式语法
items3 = {num: num ** 2 for num in range(1, 10)}
print(items1, items2, items3)
# 通过键可以获取字典中对应的值
print(scores['骆昊'])
print(scores['狄仁杰'])
# 对字典中所有键值对进行遍历
for key in scores:
    print(f'{key}: {scores[key]}')
# 更新字典中的元素
scores['白元芳'] = 65
scores['诸葛王朗'] = 71
scores.update(冷面=67, 方启鹤=85)
print(scores)
if '武则天' in scores:
    print(scores['武则天'])
print(scores.get('武则天'))
# get方法也是通过键获取对应的值但是可以设置默认值
print(scores.get('武则天', 60))
# 删除字典中的元素
print(scores.popitem())
print(scores.popitem())
print(scores.pop('骆昊', 100))
# 清空字典
scores.clear()
print(scores)
```

### __2.4.6 数据结构总结__   

按照是否可以被修改  
```
immutable: string tuple
mutable : list set dict
```
不同的创建方式 
```
list []  
tuple () 
set{}
dict{:, :}

当然这都是通过字面量来构造，每种类型也有自己的构造方式，比如set(), 比如dict()，创建方式并不固定，没有什么特殊的规律，都只是规则，规则不需要死记硬背，懂基本的构造方式，其他的方式，写代码熟练了也就知道了，长时间不用也会忘记。 还是要懂得输入输出，我们的大脑不是为了记住这些教条，而是为了形成模式，可以应对各种输入。  
```

## __2.5 类__  

跟C++类似，这里也有访问权限的问题，私有和公有，不过Python中对这块的限定没有那么严格，这也是我所认为的Python适合做对工程化要求没有那么严格的事情。  

_name : 表示私有变量  
__name : 表示私有成员  

其实即使加上了前缀，也可以访问，Python在内部只是把前缀换了个名字。  

如果确实要限制访问权限，那么就用修改器和访问器。  

```
class Person(object):

    def __init__(self, name, age):
        self._name = name
        self._age = age

    # 访问器 - getter方法
    @property
    def name(self):
        return self._name

    # 访问器 - getter方法
    @property
    def age(self):
        return self._age

    # 修改器 - setter方法
    @age.setter
    def age(self, age):
        self._age = age

    def play(self):
        if self._age <= 16:
            print('%s正在玩飞行棋.' % self._name)
        else:
            print('%s正在玩斗地主.' % self._name)


def main():
    person = Person('王大锤', 12)
    person.play()
    person.age = 22
    person.play()
    # person.name = '白元芳'  # AttributeError: can't set attribute


if __name__ == '__main__':
    main()
```

### __2.5.1\_\_slots\_\_魔法__   

Python是动态语言，他们说动态语言可以动态绑定属性和方法，可以把属性和方法动态绑定到类或者实例。  

绑定规则如下
* 绑定属性 ： 可以直接绑定属性到类和类实例
* 绑定方法 ：  1. 绑定到实例 instance.function = MethodType(function, instance)  2. class.function = MethodType(function, class) 或者class.function = function



__<font color=green>绑定到实例</font>__
```
>>> class Student(object):
...     pass
...
>>> stu1 = Student();
>>> stu1.name = 'Tom'
>>> print(stu1.name)
Tom
>>> print(dir(stu1))
['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribut
e__', '__hash__', '__init__', '__module__', '__new__', '__reduce__', '__reduce_e
x__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '_
_weakref__', 'name']
>>>
>>> def set_age(self, age):
...     self.age = age
...
>>> set_age(stu1, 20)
>>> print(stu1.age)
20
>>> print(dir(stu1))
['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribut
e__', '__hash__', '__init__', '__module__', '__new__', '__reduce__', '__reduce_e
x__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '_
_weakref__', 'age', 'name']
>>> #并没有绑定方法到实例上
...
>>> from types import MethodType
>>> stu1.set_age = MethodType(set_age, stu1)
>>> stu1.set_age(33)
>>> print(stu1.age)
33
>>> print(dir(stu1))
['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribut
e__', '__hash__', '__init__', '__module__', '__new__', '__reduce__', '__reduce_e
x__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '_
_weakref__', 'age', 'name', 'set_age']
>>> #绑定的属性和方法只属于stu1的，对于其他实例不起作用
...
>>> stu2 = Student()
>>> print(dir(stu2))
['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribut
e__', '__hash__', '__init__', '__module__', '__new__', '__reduce__', '__reduce_e
x__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '_
_weakref__']

```
__<font color=green>绑定到类</font>__  

```
>>> class Student(object):
...     pass
...
>>> def set_name(self, name):
...     self.name = name
...
>>> from types import MethodType
>>> Student.set_name = MethodType(set_name, Student)
>>> print(dir(Student))
['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribut
e__', '__hash__', '__init__', '__module__', '__new__', '__reduce__', '__reduce_e
x__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '_
_weakref__', 'set_name']
>>> stu1 = Student()
>>> stu1.set_name('Rose')
>>> stu2 = Student()
>>> stu2.set_name('Jack')
>>> print(stu1.name)
Jack
>>> print(stu2.name)
Jack
>>> ########
>>> class Student(object):
...     pass
...
>>> def set_name(self, name):
...     self.name = name
...
>>> Student.set_name = set_name
>>> print(dir(Student))
['__class__', '__delattr__', '__dict__', '__doc__', '__format__', '__getattribut
e__', '__hash__', '__init__', '__module__', '__new__', '__reduce__', '__reduce_e
x__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '_
_weakref__', 'set_name']
>>> stu1 = Student()
>>> stu1.set_name('Rose')
>>> stu2 = Student()
>>> stu2.set_name('Jack')
>>> print(stu1.name)
Rose
>>> print(stu2.name)
Jack
```

如果不想绑定那么多属性，那么可以通过__slots__来限定只允许添加某些属性，但这里有一个<font color=red>__漏洞，我们可以通过动态添加方法，而方法中添加属性来迂回这个限制，所以我不明白这么操作的意义是什么__</font>。  

### __2.5.2 staticmethod classmethod__   

Python中也有静态方法，这个很好理解，就是跟实例没什么关系，classmethod则相当于C++中的拷贝构造函数。  


### __2.5.3 类之间的关系__   

is-a : 继承  
has-a : 属性
use-a： 方法参数

继承很好理解，就是重写overwrite，如果想明确基类为抽象类的话，可以通过ABCMeta和abstractclass来实现。

### __2.5.4 奇怪的lambda表达式__  

在C++中我们写lambda [=](int x, int y)  int { return x + y;}

在python中竟然有这样的定义，看着也不是很正常啊，这里的return type是一个lambda。在C++中我们是不可以这么写的。  

```
def make_incrementor(n):
    return lambda x: x + n

f = make_incrementor(42)
print(f(0))  
print(f(1))
```

# __3 界面开发__  

有tkinter pygame，对我来说，最起码目前无意义，没有做界面开发的需求。  

# __4 文件和异常__    

任何一门编程语言，关于文件操作都会关注这几个方面

_<font color=red>以何种方式打开</font>_  
这个的打开方式是指： 打开什么样的文件（文本还是二进制）、做什么样的操作（读、写、还是追加）  

| 操作模式 | 具体含义                         |
| -------- | -------------------------------- |
| `'r'`    | 读取 （默认）                    |
| `'w'`    | 写入（会先截断之前的内容）       |
| `'x'`    | 写入，如果文件已经存在会产生异常 |
| `'a'`    | 追加，将内容写入到已有文件的末尾 |
| `'b'`    | 二进制模式                       |
| `'t'`    | 文本模式（默认）                 |
| `'+'`    | 更新（既可以读又可以写）         |


_<font color=red>要调用的接口</font>_  


## __4.1 文本文件__  

    f = open()  
    f.read()  
    f.close()  

或者 

    f = open()  
    for line in f:  
        print(line)


又或者    

    f = open()  
    lines = f.readlines()  //此时lines是列表  

写文本文件把read换成write就好。  

## __4.2 二进制文件__  

跟文本文件类型，只不过read和write的操作对象是byte  

## __4.3 JSON文件__  

JSON的数据类型和Python的数据类型是很容易找到对应关系的，如下面两张表所示。

| JSON                | Python       |
| ------------------- | ------------ |
| object              | dict         |
| array               | list         |
| string              | str          |
| number (int / real) | int / float  |
| true / false        | True / False |
| null                | None         |

| Python                                 | JSON         |
| -------------------------------------- | ------------ |
| dict                                   | object       |
| list, tuple                            | array        |
| str                                    | string       |
| int, float, int- & float-derived Enums | number       |
| True / False                           | true / false |
| None                                   | null         |  

* dump - 将Python对象按照JSON格式序列化到文件中
* dumps - 将Python对象处理成JSON格式的字符串
* load - 将文件中的JSON数据反序列化成对象
* loads - 将字符串的内容反序列化成Python对象  



# __5 正则表达式__    

个人认为正则表达式分为两部分  

1. 判断是否适合用正则，如果适合，制定正则规则  
2. 用语言规则实现  

所以，我觉得没有必要记住这些语法，到用的时候再看就是了。  

# __6 进程和线程__  

与其它语言相比，理念都是一样的。  

问题是我们是否要学习Python进程和线程中的基本概念？还是直接用Python提供的库？  

