---
layout: post
title: "从Stlport和NaN说开去"
date: 2013-06-07 02:09
comments: true
categories: 
---
##起因
上个月的时候，在项目中发生了一件莫名其妙的core dump。Client部门的同事熬夜跟踪埋伏得到的信息是使用stlport的sort对一个vector排序导致迭代器越界。其中提供了一个线索是：vector中存放的是单精度浮点数，并且有NaN。

随后，Client部门同事为了验证这个问题，得到两个结论：

* 1、	构造了一个独立的测试用例，证实了一个vector中如果有NaN的话，调用std::sort会导致迭代器越界。
* 2、	使用ms的stl没有这个问题。

当然这个问题解决起来很简单，把NaN搞定就完事了。

但是这个疑问在我心中困扰多时，stlport的实现为什么会出现这样的问题？ 这个问题究竟是不是NaN带来问题？是stlport内部忽略了这种情况还是另有蹊跷呢？

##说明
本文中参考的是vc2010的Stl实现，stlport版本是5.2.1.0。


----------
<!-- more -->

##introsort
vector的迭代器类型是随机访问迭代器，std::sort的实现中，ms的stl和stlport都差不多。使用的排序算法是“内省排序”。

所谓的“内省排序”是对快排的一种改进。关于快排有几点众所周知的问题：

* 1、	快排的分割点选择不当有可能导致快排效率低下。
* 2、	快排是递归的分治算法对于基本有序的序列或者说较小规模的数据，插入排序效率更好一些。

###media-of-3方法
传统教科书上给出的选择快排pivot的方法是使用第一个元素作为pivot，这样在实际工程中可能会导致快排的效率退化。

于是有人想出了办法：即取传入数据的首、尾、中间位置的3个数，拿中间大小的那个数做为pivot。

这么做从概率上来说让pivot更加合理。但是这不够好。

###introsort的创新之处

* 1、	定义了一个最小数据量的阀值，每次排序如果数据量较小，那么直接插入排序。这样做的好处是，万一pivot选择了一个不够好的的数，能及时止损。但是阀值也只能解决部分问题，因为不可能选择一个放之四海皆准的阀值。
* 2、	规定了快排的最大递归深度。把全部要排序的数据按照完全2叉树排列，2叉树的深度*2就是最大递归深度。乘2的目的是需要对pivot左右两边递归。最大递归深度能够弥补最小数据量的阀值的问题。即使阀值选择不当，那么也不会出现递归次数过多的问题。
* 3、	如果数据量超过阀值，使用两步排序：首先使用快排或者堆排序把数据排列得八九不离十，整个序列可以看成是一个个的子序列，每个子序列元素个数小于或等于16。且这些子序列不但内部有相当程度排序，子序列之间也是“递增”的。最后使用插入排序。不要看插入排序的理论效率不高，但是在这种情况下的效率可是相当高的。

###introsort简单描述
对于introsort的大体描述如下：

* 1、	揉合了插入排序，堆排序，快速排序。
* 2、	是一种原地排序算法。
* 3、	如果数据量小于一个阀值（例如16），那么introsort直接使用插入排序为该数据排序。流程到此终结。
* 4、	如果数据量大于阀值。introsort第一次进入的时候会计算一个最大递归深度depth。该数值控制后续递归的次数。
* 5、	每次递归调用introsort排序，如果数据量小于阀值，那么立即返回，不再继续排序。如果数据量大于阀值，但是递归深度>=最大递归深度，那么使用堆排序对本次要处理的数据排序。只有当数据量够大，且满足递归深度时使用快排算法。
* 6、	递归完成后，得到一个几乎快要完成的序列，进行一次插入排序完成排序全过程。


----------


##stlport的std::sort
说了这么多远没有直接看代码来得直接。代码就在那里每个人都能看，这里不再copy。这里记录下几个关键的地方。

###快排时怎么取分割点（pivot）
使用传统的media-of-3的方法。即取传入数据的首、尾、中间位置的3个数，拿中间大小的那个数做为pivot。这么做的好处是降低由于pivot取值不当带来的负面影响。

###最大递归深度怎么计算
函数__lg是计算最大递归深度的函数，调用的时候这么调用：

    __lg(last - first) * 2


这里通过__lg得到的是一个指数n，2^n<=数据量。其实也就是把所有数据构建一颗完全2叉树的深度。这里乘2的目的是针对左子树和右子树。

### __unguarded_partition
这个函数是负责快排算法中根据pivot将数据分割为左右两个部分的函数。stlport中这个函数和教科书上的算法不太一样。

    template <class RandomAccessIterator, class T>  
	RandomAccessIterator __unguarded_partition(
	RandomAccessIterator first,
	RandomAccessIterator last,
	T pivot) {
	  while (true) {  
	    while (*first < pivot) ++first; 
	    --last; 
	    while (pivot < *last) --last; 
	    if (!(first < last)) return first; 
	    iter_swap(first, last); 
	    ++first; 
	  }  


传统教科书上是从数据从头开始向尾部遍历，找到小于pivot的数据就让pivot的位置往后移位。

这里使用了2个迭代器，一个从头向尾移动，一个从尾向头移动。移动过程中会交换迭代器的值。这样做直到两个迭代器发生交叠，此时证明已经完成分割。

这样做的好处是能够减少swap数据的次数。当然本文最开始所叙述的问题就出在这个函数，这个函数的前缀unguarded就告诉我们，这个函数没有判断迭代器是否越界。

###__final_insertion_sort

	template <class _RandomAccessIter, class _Compare>
	void __final_insertion_sort(_RandomAccessIter __first,
	                            _RandomAccessIter __last, _Compare __comp) {
	  if (__last - __first > __stl_threshold) {
	    __insertion_sort(__first, __first + __stl_threshold, _STLP_VALUE_TYPE(__first,_RandomAccessIter), __comp);
	    __unguarded_insertion_sort(__first + __stl_threshold, __last, __comp);
	  }
	  else
	    __insertion_sort(__first, __last, _STLP_VALUE_TYPE(__first,_RandomAccessIter), __comp);
	}

__final_insertion_sort也是按照阀值来分的，这里__stl_threshold=16。如果要排的数量大于16，那么把前16个分为一组排，后面分到另一组排。对于这样做的好处，我实在没想明白。


----------


##重现Bug
根据前面的代码分析，可以推测问题可能出现在__unguarded_partition函数。该函数通过比较容器中的元素和pivot的值决定是不是要移动迭代器。也就是说只要operator <返回true，那么迭代器就会一直移动，没有任何阻碍。

为了验证这个推测。写了个小程序验证。在使用Stlport的情况下，崩溃如期而至的出现了，原因是迭代器越界。

    bool compare_v(int left,int right)
    {
    	return true;
    }
    void test_sort()
    {
    	const int size=17; //这里如果小于16就不会使用introsort
    	vector<int> v;
    	v.resize(size);
    	std::generate_n(v.begin(),size,[&]()->int{return std::rand();});
    	std::sort(v.begin(),v.end(),compare_v);
    	std::for_each(v.begin(),v.end(),[&](int v){cout<<v<<endl;});
    }

但是当把size的值改小到16的话就没有问题。因为如果小于等于16的话走的不是introsort而是直接使用插入排序。

值得一提的是，如果使用了_STLP_DEBUG宏的话，STLPORT会在运行期抛出一个异常。如果当时查bug的时候能够打开这个宏的话应该会简单很多。

再来看下Ms的STL表现怎么样。在Debug下，使用STL报出了一个assert。在Release下程序正常运行结束。关于Ms的STL内部实现后文再谈。

到此对Bug的探索得到如下成果：

 - 1、	该bug的真正原因是由于要排序的值没有遵守STL的“严格弱序”原则。而NaN这样的数的特性正好就是这样，无论拿什么数和NaN比较都会得到true。
 - 2、	要触发这个问题容器中的元素的个数必须大于16。 


----------


##谈严格弱序
严格弱序是STL中一个重要的概念，STL的实现中大量的基于这个概念。

在《Effective STL》一书中有的条款19、条款21比较详细的谈了这个问题，但是定义给的不是很清晰。

在《The C++ Standard Library》一书中对这个概念给出了清晰的界定： 

- 1、	必须是“反对称的（antisymmetric）”。意思是对operator<而言，如果x < y，那么y < x就必须返回false。
- 2、	必须是“可传递的（transitive）”。意思是对operator <而言，如果x < y，且y < z，那么x < z也必须返回true。 
- 3、	必须是“非自反的（irreflexive）”。意思是对operator <而言，x < x永远不成立。

凡是涉及到大小比较的地方都必须要遵守严格弱序的准则，例如排序的关联容器，set、map、multiset、multimap、用于排序的仿函数等等。一旦违反这个准则，得到的结果将是未定义的。


----------


##ms的std::sort
看完了stlport的实现，再看下ms的stl的实现。

从代码上来看ms的stl明显比stlport使用if、for要多。导致ms的stl的sort实现的长度要多一些。使用的算法同样是introsort。为了方便和前文对照，采用了基本和前文类似的结构来组织。

###取巧的_Unchecked
首先ms的stl的sort算法直接使用的是原始指针，这点很取巧。

    template<class _RanIt> inline
    	void sort(_RanIt _First, _RanIt _Last)
    	{	// order [_First, _Last), using operator<
    	_DEBUG_RANGE(_First, _Last);
    	_Sort(_Unchecked(_First), _Unchecked(_Last), _Last - _First);
    	}

上面的_Unchecked()函数就是把迭代器中的原始指针取出来。

然后，ms的stl中定义了_ISORT_MAX=32。这个值和stlport中的__stl_threshold的作用一致。也就是说ms的stl实现中，容器数量大于32才会使用introsort。所以这里需要修改下测试程序，将size设置为33。
###取分割点
接下来继续看，MsStl中快排取Pivot的算法同样是Media-of-3。不过实现稍微有点不同：

    template<class _RanIt,
    	class _Pr> inline
    	void _Median(_RanIt _First, _RanIt _Mid, _RanIt _Last, _Pr _Pred)
    	{	// sort median element to middle
    	if (40 < _Last - _First)
    		{	// median of nine
    		size_t _Step = (_Last - _First + 1) / 8;
    		_Med3(_First, _First + _Step, _First + 2 * _Step, _Pred);
    		_Med3(_Mid - _Step, _Mid, _Mid + _Step, _Pred);
    		_Med3(_Last - 2 * _Step, _Last - _Step, _Last, _Pred);
    		_Med3(_First + _Step, _Mid, _Last - _Step, _Pred);
    		}
    	else
    		_Med3(_First, _Mid, _Last, _Pred);
    	}
    
    template<class _RanIt,
    	class _Pr> inline
    	void _Med3(_RanIt _First, _RanIt _Mid, _RanIt _Last, _Pr _Pred)
    	{	// sort median of three elements to middle
    	if (_DEBUG_LT_PRED(_Pred, *_Mid, *_First))
    		_STD iter_swap(_Mid, _First);
    	if (_DEBUG_LT_PRED(_Pred, *_Last, *_Mid))
    		_STD iter_swap(_Last, _Mid);
    	if (_DEBUG_LT_PRED(_Pred, *_Mid, *_First))
    		_STD iter_swap(_Mid, _First);
    	}

也就是说当要处理的序列元素大于40时，和小于40时的逻辑不太一样。虽然不知道这个算法后面的理论依据，不过从代码可读性来看，我更喜欢MsStl。
###计算递归的深度
接下来看怎么计算递归的深度。MsStl使用的也是类似的算法，不过MsStl不用移位而是用除法。

    for (; _ISORT_MAX < (_Count = _Last - _First) && 0 < _Ideal; )
    	{	// divide and conquer by quicksort
    	_STD pair<_RanIt, _RanIt> _Mid =
    		_Unguarded_partition(_First, _Last, _Pred);
    	_Ideal /= 2, _Ideal += _Ideal / 2;	// allow 1.5 log2(N) divisions

_Ideal是通过函数参数传进来的。
###_Unguarded_partition
接下来是关键的_Unguarded_partition函数。在Stlport中，这个函数也就十行左右。在MsStl中这个函数的实现有差不多60行。看到这么多if判断，大概也能猜到为什么MsStl能够顺利的运行了。

    template<class _RanIt,
    	class _Pr> inline
    	_STD pair<_RanIt, _RanIt>
    		_Unguarded_partition(_RanIt _First, _RanIt _Last, _Pr _Pred)
    	{	// partition [_First, _Last), using _Pred
    	_RanIt _Mid = _First + (_Last - _First) / 2;
    	_Median(_First, _Mid, _Last - 1, _Pred);
    	_RanIt _Pfirst = _Mid;
    	_RanIt _Plast = _Pfirst + 1;
    
    	while (_First < _Pfirst
    		&& !_DEBUG_LT_PRED(_Pred, *(_Pfirst - 1), *_Pfirst)
    		&& !_Pred(*_Pfirst, *(_Pfirst - 1)))
    		--_Pfirst;
    	while (_Plast < _Last
    		&& !_DEBUG_LT_PRED(_Pred, *_Plast, *_Pfirst)
    		&& !_Pred(*_Pfirst, *_Plast))
    		++_Plast;
    
    	_RanIt _Gfirst = _Plast;
    	_RanIt _Glast = _Pfirst;
    
    	for (; ; )
    		{	// partition
    		for (; _Gfirst < _Last; ++_Gfirst)
    			if (_DEBUG_LT_PRED(_Pred, *_Pfirst, *_Gfirst))
    				;
    			else if (_Pred(*_Gfirst, *_Pfirst))
    				break;
    			else
    				_STD iter_swap(_Plast++, _Gfirst);
    		for (; _First < _Glast; --_Glast)
    			if (_DEBUG_LT_PRED(_Pred, *(_Glast - 1), *_Pfirst))
    				;
    			else if (_Pred(*_Pfirst, *(_Glast - 1)))
    				break;
    			else
    				_STD iter_swap(--_Pfirst, _Glast - 1);
    		if (_Glast == _First && _Gfirst == _Last)
    			return (_STD pair<_RanIt, _RanIt>(_Pfirst, _Plast));
    
    		if (_Glast == _First)
    			{	// no room at bottom, rotate pivot upward
    			if (_Plast != _Gfirst)
    				_STD iter_swap(_Pfirst, _Plast);
    			++_Plast;
    			_STD iter_swap(_Pfirst++, _Gfirst++);
    			}
    		else if (_Gfirst == _Last)
    			{	// no room at top, rotate pivot downward
    			if (--_Glast != --_Pfirst)
    				_STD iter_swap(_Glast, _Pfirst);
    			_STD iter_swap(_Pfirst, --_Plast);
    			}
    		else
    			_STD iter_swap(_Gfirst++, --_Glast);
    		}
    	}

其中关键的两行代码我拿红色标记了，确实是做了边界判断。
###_Insertion_sort
MsStl中的_Insertion_sort朴实无华，除了使用了Move语义外，没什么新鲜的地方，算法实现和教科书上几乎一样。相比之下stlport实现插入排序简直复杂得无法直视。

    template<class _BidIt,
    	class _Pr,
    	class _Ty> inline
    	void _Insertion_sort1(_BidIt _First, _BidIt _Last, _Pr _Pred, _Ty *)
    	{	// insertion sort [_First, _Last), using _Pred
    	if (_First != _Last)
    		for (_BidIt _Next = _First; ++_Next != _Last; )
    			{	// order next element
    			_BidIt _Next1 = _Next;
    			_Ty _Val = _Move(*_Next);
    
    			if (_DEBUG_LT_PRED(_Pred, _Val, *_First))
    				{	// found new earliest element, move to front
    				_Move_backward(_First, _Next, ++_Next1);
    				*_First = _Move(_Val);
    				}
    			else
    				{	// look for insertion point after first
    				for (_BidIt _First1 = _Next1;
    					_DEBUG_LT_PRED(_Pred, _Val, *--_First1);
    					_Next1 = _First1)
    					*_Next1 = _Move(*_First1);	// move hole down
    				*_Next1 = _Move(_Val);	// insert element in hole
    				}
    			}
    	}


----------


##小结
到此，基本上对这个bug的原因疑问都搞清楚了。

在此期间对着《算法导论》这本书重新复习了插入排序、堆排序、快速排序。收获颇多。

对照阅读stlport和MsStl的实现，对每天使用的库有了更多了解。两者孰优孰劣不想评价。MsStl的实现中，对严格弱序的检测基本上是凡事出现了迭代器的地方都有这么一句_DEBUG_LT_PRED(…)，这等无微不至的关怀体现出崇高的国际主义精神。

stlport的代码实际上和最初sgi实现的版本差别不大，这点在《STL源码剖析》中得到了印证。就本文中涉及到的代码来说，stlport的代码短小，可读性我认为不如MsStl。当然也有可能是我再读MsStl之前对整个算法已经有一定了解了，导致的这种感觉。


----------


##参考

> 《Introspective Sorting and Selection Algorithms》 

> 《STL源码剖析》 

> 《The C++ Standard Library》 

> 《Effective STL》 

> 《算法导论》 

> Stlport源码 

> Ms Stl源码

