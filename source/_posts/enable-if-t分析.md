---
title: enable_if_t分析
date: 2021-02-26 05:45:03
tags:
categories: C++
---

__To be continued__

在看VS版本的thread库源码时，发现了这么一段


```C++
// STRUCT TEMPLATE enable_if
template <bool _Test, class _Ty = void>
struct enable_if {}; // no member "type" when !_Test

template <class _Ty>
struct enable_if<true, _Ty> { // type is _Ty for _Test
    using type = _Ty;
};

template <bool _Test, class _Ty = void>
using enable_if_t = typename enable_if<_Test, _Ty>::type;



class thread { // class for observing and managing threads

...

public:
    template <class _Fn, class... _Args, enable_if_t<!is_same_v<_Remove_cvref_t<_Fn>, thread>, int> = 0>
    explicit thread(_Fn&& _Fx, _Args&&... _Ax) {
        using _Tuple                 = tuple<decay_t<_Fn>, decay_t<_Args>...>;
        auto _Decay_copied           = _STD make_unique<_Tuple>(_STD forward<_Fn>(_Fx), _STD forward<_Args>(_Ax)...);
        constexpr auto _Invoker_proc = _Get_invoke<_Tuple>(make_index_sequence<1 + sizeof...(_Args)>{});

#pragma warning(push)
#pragma warning(disable : 5039) // pointer or reference to potentially throwing function passed to
                                // extern C function under -EHc. Undefined behavior may occur
                                // if this function throws an exception. (/Wall)
        _Thr._Hnd =
            reinterpret_cast<void*>(_CSTD _beginthreadex(nullptr, 0, _Invoker_proc, _Decay_copied.get(), 0, &_Thr._Id));
#pragma warning(pop)

        if (_Thr._Hnd) { // ownership transferred to the thread
            (void) _Decay_copied.release();
        } else { // failed to start thread
            _Thr._Id = 0;
            _Throw_Cpp_error(_RESOURCE_UNAVAILABLE_TRY_AGAIN);
        }
    }

...

}
```

在这里我们看到thread构造函数的模板参数有三个，_Fn和不定参数模板_Args，以后最后的__<font color=red>enable_if_t</font>__。

enable_if_t 是 enable_if在_Test为true的情况下 才会存在别名type，而此时type=第二个参数类型。

那么 _is_same_v<_Remove_cvref_t<_Fn>, thread> _必须为false

```C++

template <class _Ty>
using _Remove_cvref_t = remove_cv_t<remove_reference_t<_Ty>>;

template <class, class>
_INLINE_VAR constexpr bool is_same_v = false; // determine whether arguments are the same type
template <class _Ty>
_INLINE_VAR constexpr bool is_same_v<_Ty, _Ty> = true;
```

_Remove_cvref_t 是取出类型本身的引用（包括左值 右值）属性、const、volatile属性。  

也就是说，对thread构造中enable_if_t中 _Fn和thread必须是不同类型，也就是_Fn不能是thread类型，那_Fn是其他任何类型都可以了？ 不是的，必须是Callable&&类型，因为必须支持std::invoke，这些在其它博文中再探讨。  



回到本文主题 enable_if_t的作用是什么，有没有其它方式实现，我们该在什么场合，如何使用它？  


首先，我们要介绍一个概念 [SFINAE](https://en.cppreference.com/w/cpp/language/sfinae)  

在函数模板的重载决议中应用此规则：当将模板形参替换为显式指定的类型或推导的类型失败时，从重载集中丢弃这个特化，而非导致编译失败。

此特性被用于模板元编程。




