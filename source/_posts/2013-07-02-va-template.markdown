---
layout: post
title: "从变长参数到格式化字符串4 Variadic templates"
date: 2013-07-02 00:04
comments: true
categories: 
---

<!-- more -->

##语法以及用法
可变参数模板的样子是这样的：
```cpp
template<typename T, typename... Args>
void TestVaT(T value, Args... args)
{
...
}
```
###参数包
这里```template<typename T, typename... Args>```这句话中Args表示一个“模板形参包”。一个模板形参包（template parameter pack）是一个接受零个或多个模板实参的模板形参。

模板参数包只能是模板参数的最后一个，以下代码是不合法的：
```cpp
template<typename T, typename... Args,typename U> //不合法
```
在```void TestVaT(T value, Args... args)```中，args表示一个“函数形参包”。一个函数形参包（function parameter pack）是一个接受零个或多个函数实参的函数形参。

同理，函数形参包也只能是函数的最后一个参数。
###模式和…操作符
这里…有个专有名称表示：```Meta-Operator “...”```。

和…一起出现的叫做模式。例如：
```cpp
template<typename T, typename... Args> // typename... Args为模板形参包，Args为模式
void TestVaT(T value, Args... args) // Args... args为函数形参包，args为模式
{
TestVaT(args...);
}
```
值得注意的是，需要仔细体会下…和模式的左右位置。
###Packing and Unpacking
这一段是我根据各个资料以及编译器编译的结果瞎猜的，概念和用词上可能不准确。

当声明了一个可变参数模板，以上面的TestVaT这个函数举例子。当编译器看到TestVaT这个函数的定义后，就知道这里有一个参数报。接下来如果编译器看到调用TestVaT(1,2.0f,”3.14”)时，就会发生Unpacking的操作，即根据调用来生成TestVaT的定义：
```cpp
void TestVaT<int,float,const char*>(int value,float arg_1,const char* arg_2)
{
}
```
###获取变长参数的个数
有两种方法：

 - 使用语言提供的sizeof，例如：
```cpp
template<typename T, typename... Args>
void TestVaT(T value, Args... args)
{
int sz=sizeof...(Args);
}
```
这里取```sizeof…(Args```)或者```sizeof…(args)```是等价的。另外，如果这么写```sizeof(args)```编译是通不过的：
```
error C3520: 'args' : parameter pack must be expanded in this context
```

 - 自己写一个尾递归函数
```cpp
template<typename T, typename... Args>
struct count<T, Args...> 
{
static const int value = 1 + count<Args...>::value;
};

template<>
struct count<> 
{
static const int value = 0;
};
```
##使用变长参数
遗憾的是，C++并没有提供一个让人舒服的方式来迭代变长模板参数。

方法居然是使用函数重载、模板特化以及递归的方式来取出变长参数，一次递归只能取1个……

下面的例子是官方文档中提供的一个简化的printf的例子：
```cpp
void printf(const char *s)
{
  while (*s)
  {
    if (*s == '%' && *(++s) != '%')
      throw std::runtime_error("invalid format string: missing arguments");
    std::cout << *s++;
  }
}
 
template<typename T, typename... Args>
void printf(const char* s, const T& value, Args... args)
{
  while (*s)
  {
    if (*s == '%' && *(++s) != '%')
    {
      std::cout << value;
      printf(*s ? ++s : s, args...); // 即便当 *s == 0 也会产生调用，以检测更多的类型参数。
      return;
    }
    std::cout << *s++;
  }
  throw std::logic_error("extra arguments provided to printf");
}
```
也就是说，如果有一个函数需要使用变长模板参数，那么至少还需要写2个函数，一个用来取变长参数，一个用来处理当所有参数都取出后的逻辑，例如：
```cpp
template<typename… Args>
void foo(Args… p)
{
foo_impl(p…);
}
template<typename T,typename… Args>
void foo_impl(const T& p1,Args… p)
{
}

template<typename T >
void foo_impl(const T& p1)
{
}
```
##语法糖的背后
为了搞清楚编译器生成的代码，写以下代码测试：
```cpp
template<typename T>
void TestVaT(T value)
{
	cout<<value<<endl;
}

template<typename T, typename... Args>
void TestVaT(T value, Args... args)
{
	TestVaT(value);
	TestVaT(args...);
}

int _tmain(int argc, _TCHAR* argv[])
{
	
	TestVaT(1,0.20f,'3',"hello Variadic templates",2U,argc);
	system("pause");
	return 0;
}
```
先看main函数生成的代码：
{% img /images/2/main.jpg %}

编译器实际上生成了一个```TestVaT(int,float,char,char const*,uint,int)```的一个函数代码：

 {% img /images/2/1.jpg %}

当消耗完一个参数后，编译器调用了另一个函数：```TestVaT(float,char,char const*,uint,int)```

 {% img /images/2/2.jpg %}

又消耗了一个参数，现在调用的是```TestVaT(char,char const*,uint,int)```

 {% img /images/2/3.jpg %}

又消耗了一个参数，现在调用的是```TestVaT(char const*,uint,int)```

 {% img /images/2/4.jpg %}

最后来到```void TestVaT(T value)```这个函数，此时函数调用栈如下图所示：

 {% img /images/2/stack.jpg %}

到此，也大概明白，编译器辛苦劳动的结果了。


##利用Variadic templates格式化字符串
使用variadic templates的方法，对格式化字符串这件事情来说，能带来的好处是类型安全。

但是我认为，对于格式化字符串来说，前面一篇文章中提到的miniformat使用的方式是最合适的方式。miniformat中提供的好处是快，灵活，类型安全。其中快的原因是：

 1. 内存操作少。即miniformat在格式化字符串之前对需要的内存大小做了计算，虽然比实际用的可能稍微大了点。
 2. 针对格式化字符串的特点做了算法优化。

如果使用variadic templates的方法，在以下几点上限制较大：

 1. 如果要计算所有格式化字符串需要的长度，那么就需要使用2
    pass的一个计算，即为了获取长度要使用递归把变长参数遍历访问一次，然后具体格式化的时候又要遍历访问一次。
 2. 还是变长参数的访问限制问题。就是没法随机访问变长参数。那么如果想在格式化字符串中使用%0 %1 这样的方式就变得很困难了。

这里本着练手的目的，我把miniformat的代码拿variadic templates改了一下，只不过使用%0 %1的时候，每个数字只能顺序使用，也只能使用一次了。

代码放在这里：https://github.com/sssa2000/va_miniformat


##Tinyformat库
tinyformat库也是一个格式化字符串的库，感兴趣的可以取github上关注它的代码。这个库的代码写的也很简洁，值得一读。

tinyformat库中使用了两种风格的格式化方法，varidaic templates的方法和朴素的函数重载法。在tinyformat的说明中，发现它真的不支持%0 %1这样的使用方式，看来这十有八九就是由于它使用了varidaic templates的方式。

这里只简单提一下tinyformat中使用varidaic templates的代码，在使用之前，需要首先把宏TINYFORMAT_USE_VARIADIC_TEMPLATES定义一下，要不使用的还是传统的方式实现。
```cpp
int _tmain(int argc, _TCHAR* argv[])
{
std::string str=tinyformat::format("abc =%d,bcd=%s",123,"afsda");
system("pause");
return 0;
}
tinyformat::format的代码如下：

template<typename... Args>
std::string format(const char* fmt, const Args&... args)
{
    std::ostringstream oss;
    format(oss, fmt, args...);
    return oss.str();
}
```
可以看出这里用的是stream。oh my god。接着往下看：
```cpp
template<typename... Args>
void format(std::ostream& out, const char* fmt, const Args&... args)
{
    detail::FormatIterator fmtIter(out, fmt);
    format(fmtIter, args...);
}
```
这里，使用了一个叫做FormatIterator的类把解析format string以及所有的控制输出宽度、精度的逻辑都封装了，这让代码变得很Clean。

接下来两个函数就是遍历变长参数的标准做法了：
```cpp
// Private format function on top of which the public interface is implemented
inline void format(FormatIterator& fmtIter)
{
    fmtIter.finish();
}
 
// First, the simple definition for C++0x:
template<typename T1, typename... Args>
void format(FormatIterator& fmtIter, const T1& value1, const Args&... args)
{
    fmtIter.accept(value1);
    format(fmtIter, args...); //这是递归
}
```
##参考
http://en.wikipedia.org/wiki/Variadic_Templates
http://www.cnblogs.com/zenny-chen/archive/2013/02/03/2890917.html
http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2006/n2080.pdf