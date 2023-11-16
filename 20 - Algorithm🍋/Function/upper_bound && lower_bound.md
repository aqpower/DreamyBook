[cppreference.com](https://en.cppreference.com/w/cpp/algorithm/upper_bound)
C++ STL 提供了两个特别好用的函数：lower_bound () 和 uppper_bound ()。假设 a 是一个数组，n 是数组长度，lower_bound (a, a+n, x) 返回数组 a[0]~a[n-1]中，**大于等于**x 的数中，最小的数的指针。由于指针可以通过加减算偏移量，所以我们再减去 a (数组名会被隐式转换成指针)，就得到了相应的下标。注意的是 `lower_bound是“大于等于”，upper_bound是“大于”`。
# 上界 && 下界
## 功能
都是针对已经排好的序列进行二分查找


![](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/uppp.png)
`ForwardIter lower_bound (ForwardIter first, ForwardIter last, const _Tp& val) `
**lower_bound**算法返回一个非递减序列 $[$ first, last) 中的第一个**大于等于**值 val 的位置。

`ForwardIter upper_bound(ForwardIter first, ForwardIter last, const _Tp& val)` 
**upper_bound**算法返回一个非递减序列 $[$ first, last) 中第一个**大于** val 的位置。
1. 通过返回的地址减去起始地址 begin, 得到找到数字在数组中的下标。
2. 如果查找的数不存在，返回该数应该在的位置 first or last。
3. 支持传入一个自定义比较函数作为比较器
```
template <class ForwardIterator, class T, class Compare>
ForwardIterator lower_bound (ForwardIterator first, ForwardIterator last, const T& val, Compare comp);
```

## 源码实现
```C++
template <class ForwardIterator, class T>
ForwardIterator upper_bound (ForwardIterator first, ForwardIterator last, const T& val)
{
    ForwardIterator it;
    iterator_traits<ForwardIterator>::difference_type count, step;
    count = std::distance(first,last);
    while (count>0)
    {
        it = first; step=count/2; std::advance (it,step);
        if (!(val<*it))  // 或者 if (!comp(val,*it)), 对应第二种语法格式
            { first=++it; count-=step+1;  }
        else count=step;
    }
    return first;
}
```
