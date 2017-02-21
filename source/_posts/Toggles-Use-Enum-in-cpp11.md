---
title: 强类型枚举 和 bool 传参
date: 2017-02-21 00:38:13
tags: 读后感
---

闲话不多说，以下只是我的粗略加工，在此记录。原始内容来自这篇文章：
Toggles in functions
https://akrzemi1.wordpress.com/2017/02/16/toggles-in-functions/

## 问题
作者提到：cpp 中的某些代码会使用 bool 作为参数，例如：
```cpp
process(true, false);
```

但是这样有几个不好的地方：
- 可读性问题。阅读代码的人看到这样的代码完全不知道 true、false 代表的含义是什么。
- 某些同学可能会说可以借助IDE的功能解决。但是如果是这样的代码，可能IDE也帮不了你：

```cpp
std::function<void(bool, bool)> callback = &process;
callback(???, ???); // what names to use?
```
- 如果 process 这个函数是某个基类的成员虚函数时，如果不小心的话，子类覆盖的时候很可能会让两个参数的位置发生交换。这个坑我自己是踩过的，我记得当时是深夜，查到这个问题的时候哭笑不得。

```cpp
struct Base
{
  virtual void process(bool withValidation,
                       bool withNewEngine);
};
 
struct Derived : Base
{
  void process(bool withNewEngine,
               bool withValidation) override;
};
```
- 如果你的构造函数有2个bool参数，那么explicit 也帮不了你，编译器只会报告一个警告，如果忽视的话很可能引起问题。

## 问题的原因
作者认为，人们使用bool的原因有：
- 小。size=1.
- 只有两个状态。

所以作者提出：使用 cpp11的强类型枚举，来解决这个问题。对应的关键字是： **enum class** 。 

强类型枚举的特点：
- 访问枚举值需要用“类名::”，不能在类的外部直接访问。
- int 和 强类型枚举之间 无法隐式转换。
- 可以指定枚举的底层数据类型，而不是只能用int。

## 具体办法

使用上面的方案，作者给出的代码也很简单

```cpp
enum class WithValidation : bool { False, True };
enum class WithNewEngine  : bool { False, True };

void process(WithValidation withValidation,
             WithNewEngine  withNewEngine);
```

调用的时候必须这样：

```cpp
process(WithValidation::False, WithNewEngine::True); // ok
```

但是依然有一些不方便的地方：
- 当需要读取枚举为bool值时，需要显示转换：

```cpp
if (bool(withNewEngine) || bool(withValidation))
  validate();
```

- 当 set 枚举值时，也无法直接用bool，因为无法隐式转换。

```cpp
class X
{
  WithNewEngine _withNewEngine;
 
public:
  X() 
    : _withNewEngine(WithNewEngine(true)) // : _withNewEngine(true) // fails

};
```

## 进一步解决
为了解决无法隐式转换带来的不方便，需要做一些额外工作，其实就是需要一个util class，实现 operator bool() 。
作者给出了实现：https://github.com/akrzemi1/explicit/blob/master/include/ak_toolkit/tagged_bool.hpp

这个util class 叫 tagged_bool ， 值得一提的是，tagged_bool 是作者的开源库 explicit 的一部分，这个库中有很多用于类型显示转换的小函数，用于编译期检查代码，减少类型转换带来的 bug 的可能。

```cpp
using WithValidation = tagged_bool<class WithValidation_tag>;
using WithNewEngine  = tagged_bool<class WithNewEngine_tag>;

void process(WithValidation withValidation,
             WithNewEngine  withNewEngine)
{
  if (withNewEngine || withValidation)  // ok
    validate();
 
  // ...
}
 
process(WithValidation{true}, WithNewEngine{false}); // ok
process(WithNewEngine{true}, WithValidation{false}); // fails
```


## 附：tagged_bool 实现


```cpp

// Copyright (C) 2016 Andrzej Krzemienski.
//
// Use, modification, and distribution is subject to the Boost Software
// License, Version 1.0. (See accompanying file LICENSE_1_0.txt or copy at
// http://www.boost.org/LICENSE_1_0.txt)

#ifndef AK_TOOLKIT_EXPLICIT_TAGGED_BOOL_HEADER_GUARD_
#define AK_TOOLKIT_EXPLICIT_TAGGED_BOOL_HEADER_GUARD_

namespace ak_toolkit {
namespace xplicit {
namespace tagged_bool_ns { // artificial namespace to prevent ADL into namespace xplicit

template <typename Tag>
class tagged_bool
{
    bool value;
    
    template <typename /*OtherTag*/>
        friend class tagged_bool;
    
public:
  
    constexpr explicit tagged_bool (bool v) : value {v} {}
    
    constexpr explicit tagged_bool (int) = delete;
    constexpr explicit tagged_bool (double) = delete;
    constexpr explicit tagged_bool (void*) = delete;
    
    template <typename OtherTag>
        constexpr explicit tagged_bool (tagged_bool<OtherTag> b) : value {b.value} {}
    
    constexpr explicit operator bool() const { return value; }
    
    friend constexpr bool operator==(tagged_bool l, tagged_bool r) { return l.value == r.value; }
    friend constexpr bool operator!=(tagged_bool l, tagged_bool r) { return l.value != r.value; }
};

}

using tagged_bool_ns::tagged_bool; // with this tagged_bool is in namespace xplicit but with disabled ADL

}
}

#endif
```