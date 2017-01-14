---
layout: post
title: "从变长参数到格式化字符串3 miniformat赏析"
date: 2013-06-25 02:18
comments: true
categories: 
---

crt提供的printXXX系列的函数很好用，效率也很高，但是就是有一个毛病：

* 1、	类型不安全。也就是说如果你写了%s但是实际传给格式化字符串的数据是一个整型的话，这个时候编译器不会给你任何警告，但是运行期得到的结果是未定义的。通常情况下最好的结果是core dump了。
* 2、	编译器不会检查变长参数和实际需要参数数量是否一致。当然某些函数在运行期会检测。

<!-- more -->


##miniformat用法
miniformat是一个小巧的针对格式化字符串的库，作者有一篇原文：A Fast, Type-safe & Minimal String Formatting Library, miniformat，刚兴趣的可以找来一读。miniformat的特性也就是和前面作者写的标题一样：

> * 1、	快
> * 2、	类型安全

miniformat的使用方法和printXXX的语法类似，不过使用的是%0，%1这样的方式，也支持宽度控制。我认为这样的方式更加友好。例如：
```cpp
mini::format(str, "String: %1 Int: %0, Float: %(.3)2\n", 100, "JJ", 3.141592);
```
##浅析miniformat的实现
###如何做到类型安全

类型问题在C++中自然是template解决，但是参数数量是不固定的。这里使用了暴力方法，利用C++中的函数重载来解决，提供了9个重载的版本，也就是说最大支持有9个参数。基本能满足日常需要。以下代码是将宏展开后的结果
```cpp
template <typename String,class P1 >
void miniformat::format(String& outputText, const char *formatText, P1 p1)
{…}

template <typename String,class P1,class P2 >
void miniformat::format(String& outputText, const char *formatText, P1 p1,P2 p2)
{…}


template <typename String,class P1,class P2,class P3>
void miniformat::format(String& outputText, const char *formatText, P1 p1,P2 p2,P3 p3)
{…}

…

```
###格式化字符串的流程

* 1、	计算大概所需的长度。计算格式化完成后的字符串buffer大概需要的长度。
* 2、	分析模式。遍历传入的模式字符串，获取格式化模式。
* 3、	将参数转化为字符串。取一个参数，转化为字符串，放入结果中。

写成伪代码差不多是这个样子
```
len=CalcResLen()
foreach(c in str)
{
	if(模式匹配(c) )
		将参数转化为字符串
}
```
###步骤1：计算大概所需的长度
如果在进行格式化之前能确定最终生成的字符串长度，那么对运行期效率是有好处的。
获取长度的方法很简单：最终长度=模式字符串的长度+所有的参数的长度（约等于）。例如： 
```cpp
mini::format(str, "hello %s!",”Cpp”);
```
这里```最终长度=strlen(“hello %s!”)+strlen(“Cpp”)```

除了字符串类型，能够出现在参数的类型也无非就是:%d、%f等有限的几个。如果某个参数被推导为字符串类型，那么就使用strlen获取该参数所需要的长度；如果被推断为其他的类型直接取长度=KGranulity，这里KGranulity=32。仔细想想，32确实也够了。
```cpp
template <typename T>
int miniformat::detail::size_enough(T value)
{
	return kGranulity;
}

template <>
inline int miniformat::detail::size_enough(const char *value)
{
    return strlen(value);
}
```
根据前面所说的，针对不同参数个数有不同的重载函数，所以这里各个重载函数中求长度的代码也各不相同。但是通过宏运算包装了以后用起来倒是十分的统一。这种利用宏简化代码的技巧在miniformat的代码中被多次用到
```cpp
#define _FORMAT_GET_NEW_LENGTH_0		strlen(formatText)
#define _FORMAT_GET_NEW_LENGTH_1		_FORMAT_GET_NEW_LENGTH_0 + detail::size_enough(p1)
#define _FORMAT_GET_NEW_LENGTH_2		_FORMAT_GET_NEW_LENGTH_1 + detail::size_enough(p2)
#define _FORMAT_GET_NEW_LENGTH_3		_FORMAT_GET_NEW_LENGTH_2 + detail::size_enough(p3)
#define _FORMAT_GET_NEW_LENGTH_4		_FORMAT_GET_NEW_LENGTH_3 + detail::size_enough(p4)
#define _FORMAT_GET_NEW_LENGTH_5		_FORMAT_GET_NEW_LENGTH_4 + detail::size_enough(p5)
#define _FORMAT_GET_NEW_LENGTH_6		_FORMAT_GET_NEW_LENGTH_5 + detail::size_enough(p6)
#define _FORMAT_GET_NEW_LENGTH_7		_FORMAT_GET_NEW_LENGTH_6 + detail::size_enough(p7)
#define _FORMAT_GET_NEW_LENGTH_8		_FORMAT_GET_NEW_LENGTH_7 + detail::size_enough(p8)
#define _FORMAT_GET_NEW_LENGTH_9		_FORMAT_GET_NEW_LENGTH_8 + detail::size_enough(p9)
```
###步骤2：分析模式
miniformat中用于分析模式的代码很短，比printXXX精简多了。从代码可以看出，miniformat中一共支持以下几种模式：
> %n，其中n取值范围```[0,9]```

> %(w)n，其中w和n的取值范围均为```[0,9]```。w表示最大宽度。

> %(.p)n，其中p和n的取值范围均为```[0,9]```。p表示小数点后输出多少位。

> %(w.p)n，其中w、p和n的取值范围均为```[0,9]```

分析模式本身的代码并没什么值得一说的地方，无非就是遍历字符串，挨个字符分析。
###步骤3：将参数转化为字符串
当分析模式的过程中得到一个匹配的模式后，接下来马上就要取一个参数，将该参数转化为字符串。这个功能实现在render函数中。

可以从代码中的render函数的原型中看出，一共支持的参数类型有6种：
```cpp
template <typename String>
int render(String& wstr, int currentLength, int32_t value, int width, int precision);

template <typename String>
int render(String& wstr, int currentLength,  uint32_t value, int width, int precision);

template <typename String>
int render(String& wstr, int currentLength,  int64_t value, int width, int precision);

template <typename String>
int render(String& wstr, int currentLength,  uint64_t value, int width, int precision);

template <typename String>
int render(String& wstr, int currentLength,  double value, int width, int precision);

template <typename String>
int render(String& wstr, int currentLength,  const char *value, int width, int precision);
```
render函数也就是这个库中最核心的代码了。该函数的实现借鉴了Andrei Alexandrescu大神在一个ppt：```《Three Optimization Tips for C++》```中提到的方法。在这个ppt中详细介绍了一种将整型转化为字符串的快速方法。
它为什么快
Andrei Alexandrescu大神总是在C++领域提出一些让人耳目一新的东西。首先应该先看下传统的_itoa的实现。

###_Itoa实现赏析
_itoa的实现核心代码均在xtox函数中(\vc\crt\src\xtoa.c)，为了阅读方便，下面是我去掉了该函数中几个_SECURE_ITOA宏以后的代码。
```cpp
static void __stdcall xtox
        (
        unsigned long val,
        TCHAR *buf,
        unsigned radix,
        int is_neg
        )
{
        TCHAR *p;                /* pointer to traverse string */
        TCHAR *firstdig;         /* pointer to first digit */
        TCHAR temp;              /* temp char */
        unsigned digval;         /* value of digit */

        p = buf;

        if (is_neg) 
        {
            /* negative, so output '-' and negate */
            *p++ = _T('-');
            val = (unsigned long)(-(long)val);
        }

        firstdig = p;           /* save pointer to first digit */

        do {
            digval = (unsigned) (val % radix);
            val /= radix;       /* get next digit */

            /* convert to ascii and store */
            if (digval > 9)
                *p++ = (TCHAR) (digval - 10 + _T('a'));  /* a letter */
            else
                *p++ = (TCHAR) (digval + _T('0'));       /* a digit */
        } while (val > 0);

        /* We now have the digit of the number in the buffer, but in reverse
           order.  Thus we reverse them now. */

        *p-- = _T('\0');            /* terminate string; p points to last digit */

        do {
            temp = *p;
            *p = *firstdig;
            *firstdig = temp;   /* swap *p and *firstdig */
            --p;
            ++firstdig;         /* advance to next two digits */
        } while (firstdig < p); /* repeat until halfway */
}
```
可以看到该函数的主要逻辑是：

* 1、处理负数

* 2、不断的对要转换的数字对10求模，得到每一位。

* 3、反转结果。因为第二步中得到的数字是颠倒的。

###miniformat中的方法
itoa中有以下几点是可以改进的：

* 1、	对10求模一次只能处理1位，可以考虑对100求模。
* 2、	反转结果应该是可以优化的。

于是Andrei Alexandrescu提出的方法是：

* 1、	对于小于100的数字，直接使用查表法。
* 2、	每次对100求模，一次处理两位，并且每处理两位就把结果放到字符串末尾，不需要反转结果。

在miniformat中这个实现如下。这里还处理了输出宽度、负数等问题。
```cpp
template <typename String>
int miniformat::detail::render(String& wstr, int currentLength, int32_t value, int width, int)
{
    static const char digits[201] =
        "0001020304050607080910111213141516171819"
        "2021222324252627282930313233343536373839"
        "4041424344454647484950515253545556575859"
        "6061626364656667686970717273747576777879"
        "8081828384858687888990919293949596979899";

	// Take care of sign.
	unsigned int uvalue = (value < 0) ? -value : value;

	int next = currentLength;
    const int length = digits10(uvalue);

	// Handle the 'width' parameter.
	int spaceCnt = width - length - (value < 0);
	if(spaceCnt > 0)
    {
		currentLength = string_adaptor::append(wstr, currentLength, spaceCnt, ' ');
        next += spaceCnt;
    }

    if(value < 0)
    {
        currentLength = string_adaptor::append(wstr, currentLength, 1, '-');
        ++next;
    }

    currentLength = string_adaptor::append(wstr, currentLength, length, '0');
    next += length-1;

    while(uvalue >= 100)
    {
        const uint32_t i = (uvalue % 100) * 2;
        uvalue /= 100;
        *string_adaptor::at(wstr, next) = digits[i+1];
        *string_adaptor::at(wstr, next-1) = digits[i];
        next -= 2;
    }

    // Handle last 1-2 digits.
    if(uvalue < 10)
    {
        *string_adaptor::at(wstr, next) = '0'+static_cast<char>(uvalue);
    }
    else
    {
        const uint32_t i = static_cast<uint32_t>(uvalue) * 2;
        *string_adaptor::at(wstr, next) = digits[i+1];
        *string_adaptor::at(wstr, next-1) = digits[i];
    }

    return currentLength;
}
```
###miniformat中浮点数转化为字符串的方法
miniformat只提供了double的格式化函数，我想这想法是来自于Andrei Alexandrescu大神在ppt中所说的：当代CPU处理double和float几乎一样快了。

如果不考虑输出宽度和小数点后位数的逻辑，转化浮点书为字符串的方法也比较简单：

* 1、	将浮点数拆分成两个数，一个是小数点以前的数，一个是小数点以后的数字
* 2、	小数点以前的数转化为整型，将该整型转化为字符串
* 3、	小数点以后的数字乘以10的n次方，n=输出的精度，然后转化为整型。将该整型转化为字符串。
* 4、	另外需要对NaN和负数做一定的处理。

不过这里让我不理解的是，将整型转化为字符串的时并没有使用前面说的方法，而是使用了和_itoa几乎一样的算法。
```cpp
template <typename String>
int miniformat::detail::render(String& wstr, int currentLength, double value, int width, int precision)
{
	int beginIndex = currentLength;
	/* Hacky test for NaN
	 * under -fast-math this won't work, but then you also won't
	 * have correct nan values anyways.  The alternative is
	 * to link with libmath (bad) or hack IEEE double bits (bad)
	 */
	if(!(value == value))		
	{
		// Handle the 'width' parameter.
		int spaceCnt = width - 3;
		if(spaceCnt > 0)
			currentLength = string_adaptor::append(wstr, currentLength, spaceCnt, ' ');

		currentLength = string_adaptor::append(wstr, currentLength, 1, 'n'); 
		currentLength = string_adaptor::append(wstr, currentLength, 1, 'a'); 
		currentLength = string_adaptor::append(wstr, currentLength, 1, 'n');
		return currentLength;
	}

	/* if input is larger than thres_max, revert to exponential */
	const double thres_max = (double)(0x7FFFFFFF);

	double diff = 0.0;

	if(precision < 0) 
	{
		precision = 0;
	} 
	else if(precision > 9) 
	{
		/* precision of >= 10 can lead to overflow errors */
		precision = 9;
	}

	/* we'll work in positive values and deal with the
		negative sign issue later */
	int neg = 0;
	if(value < 0) 
	{
		neg = 1;
		value = -value;
	}

	int whole = (int)value;
	double tmp = (value - whole) * pow10[precision];
	uint32_t frac = (uint32_t)(tmp);
	diff = tmp - frac;

	if(diff > 0.5) 
	{
		++frac;
		/* handle rollover, e.g.  case 0.99 with precision 1 is 1.0  */
		if(frac >= pow10[precision]) 
		{
			frac = 0;
			++whole;
		}
	}
	else if(diff == 0.5 && ((frac == 0) || (frac & 1)))
	{
		/* if halfway, round up if odd, OR
			if last digit is 0.  That last part is strange */
		++frac;
	}

	/* for very large numbers switch back to native sprintf for exponentials.
		anyone want to write code to replace this? */
	/*
		 normal printf behavior is to print EVERY whole number digit
		which can be 100s of characters overflowing your buffers == bad
	*/
	if(value > thres_max)
	{
		const int kSomeEnoughSpace = 128;
		char buffer[kSomeEnoughSpace+1];
#if _MSC_VER
        sprintf_s(buffer, kSomeEnoughSpace, "%*e", width, neg ? -value : value);
#else
        snprintf(buffer, kSomeEnoughSpace, "%*e", width, neg ? -value : value);
#endif
		buffer[kSomeEnoughSpace] = 0;
		currentLength = string_adaptor::append(wstr, currentLength, buffer);
		return currentLength;
	}

	if(precision == 0)
	{
		diff = value - whole;
		if(diff > 0.5) 
		{
			/* greater than 0.5, round up, e.g. 1.6 -> 2 */
			++whole;
		} 
		else if(diff == 0.5 && (whole & 1)) 
		{
			/* exactly 0.5 and ODD, then round up */
			/* 1.5 -> 2, but 2.5 -> 2 */
			++whole;
		}
	} 
	else 
	{
		int count = precision;
		// now do fractional part, as an unsigned number
		do 
		{
			--count;
			currentLength = string_adaptor::append(wstr, currentLength, 1, (char)(48 + (frac % 10)));
		} 
		while((frac /= 10));
		// add extra 0s
		currentLength = string_adaptor::append(wstr, currentLength, count, '0');
		// add decimal
		currentLength = string_adaptor::append(wstr, currentLength, 1, '.');
	}

	// do whole part.
	// Take care of sign.
	// Conversion. Number is reversed.
	do 
	{
		currentLength = string_adaptor::append(wstr, currentLength, 1, (char)(48 + (whole % 10))); 
	}
	while((whole /= 10));
	if(neg) 
	{
		currentLength = string_adaptor::append(wstr, currentLength, 1, '-');
	}
    
	// Handle the 'width' parameter.
	int spaceCnt = width - (currentLength-beginIndex);
	if(spaceCnt > 0)
		currentLength = string_adaptor::append(wstr, currentLength, spaceCnt, ' ');

	int endIndex = currentLength-1;
	strreverse(string_adaptor::at(wstr, beginIndex), string_adaptor::at(wstr, endIndex));

    return currentLength;
}
```