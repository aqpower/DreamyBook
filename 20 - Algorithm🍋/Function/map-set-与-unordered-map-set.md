---
title: map / set 与 unordered_map /set
date: 2022-07-17 15:17:43
tags: 算法
---
前排总结：
有序/稳定/在意内存 -> set/map
无序/快速查找添加删除/不担心高内存 -> unordered_set/map
map 与 set 都是经常用到的 STL 容器，此文用来分析对比总结以便以后能够更好的运用它们。
<!--more-->

- [map 与 set](#map-与-set)
- [unordered_map 与 unordered_set](#unordered_map-与-unordered_set)
- [对比](#对比)
  - [以map&unordered_map为例：](##以map&unordered_map为例：)
- [参考链接](#参考链接)
- [例题](#例题)

# map 与 set

1. map 和 set是依据红黑树来实现的。红黑树虽然本质上是一棵二叉查找树，但它在二叉查找树的基础上增加了着色和相关的性质使得红黑树相对平衡，属于平衡二叉树，从而保证了红黑树的查找、插入、删除的时间复杂度最坏为O(log n)。

2. map是按照operator < 比较判断元素是否相同，以及比较元素的大小，然后选择合适的位置插入到树中。所以，如果对map进行中序遍历的话，输出的结果是有序的，从外部操作来看只需要遍历map即可得到有序序列

# unordered_map 与 unordered_set

unordered翻译成`“无序的”`，但其实并不是完全无序，而是运用了开散列的存储方式。它们的查找速度是常数级的，比map和set要更快。

unordered_set & unordered_map内部实现均基于哈希表（采用除留余数法）
> 哈希表——根据关键码值而进行直接访问的数据结构，通过相应的哈希函数(也称散列函数)处理关键字得到相应的关键码值，关键码值对应着一个特定位置，用该位置来存取相应的信息，这样就能以较快的速度获取关键字的信息。会存在不可避免的冲突问题。解决冲突的方法常见的有：开发地址法、再散列法、链地址法(也称拉链法)。而unordered set/map内部解决冲突采用的是链地址法，当用冲突发生时把具有同一关键码的数据组成一个链表。

以unordered_set为例，在一个unordered_set内部，元素不会按任何顺序排序，而是通过元素值的hash值将元素分组放置到各个槽(Bucker，也可以译为“桶”），这样就能通过元素值快速访问各个对应的元素（均摊耗时为O（1））
`当我只要访问而不需要顺序，想用空间弥补时间时，哈希表是很好的选择。`

# 对比

**在使用上主要是有无序/稳定否的差别**

## 以map&unordered_map为例：
unordered_map的用法和map是一样的，提供了 insert，size，count等操作，并且里面的元素也是以pair类型来存贮的。其底层实现是完全不同的，但是就外部使用来说却是一致的。

>map 是 STL 的一个关联容器，它提供一对一（第一个为 key，每个 key 只能在 map 中出现一次，第二个为 value）的数据处理能力。map 内部自建一颗红黑树（一种非严格意义上的平衡二叉树），所以在 map 内部所有的数据都是有序的，且 map 的查询、插入、删除操作的时间复杂度都是 O (logN)。在使用时，map 的 key 需要定义 operator < 。
>
>unordered_map和map类似，都是存储的 key-value 的值，可以通过key快速索引到value。不同的是unordered_map不会根据key的大小进行排序，存储时是根据key的hash值判断元素是否相同，即unordered_map内部元素是无序的。unordered_map的key需要定义hash_value函数并且重载operator == 。

两者均按键查找

**运行效率方面**：unordered_map最高，而map效率较低但提供了稳定效率和有序的序列。

**占用内存方面**：map内存占用略低，unordered_map内存占用略高,而且是线性成比例的。

因而可以实现用空间换时间的优化操作。

但是并不是unordered_map查询时间一定比map短，因为实际情况中还要考虑到数据量，而且unordered_map的hash函数的构造速度也没那么快，所以不能一概而论，应该具体情况具体分析。哈希表的建立也比较耗费时间。其实log n 比 1也就大了最多几十倍，哈希map比map快那么一点但是如果key值（string类型）过长，有时候就会比map慢很多。

set与unordered_set类比于map与哈希map ，原理相同，但我更倾向用map因为之前碰到一题被set卡了。

# 参考链接

[[1] 枚举与优化之UNORDERED_SET/MAP](https://www.freesion.com/article/89351076997/)  
[[2] C++ STL 之 unordered_set 介绍](https://blog.csdn.net/u010956473/article/details/77100750)  
[[3] map/unordered_map原理和使用整理](https://blog.csdn.net/Blues1021/article/details/45054159)  
[[4] 枚举与优化套路](https://blog.csdn.net/weixin_39778570/article/details/80677688)

# 例题

链接：https://ac.nowcoder.com/acm/contest/37344/J
来源：牛客网

给定一个序列，你可以在序列中任取 4 个数 a,b,c,d（可以重复选取），问是否有一种选择满足：a + b + c = d。  
如果存在一种情况满足，则输出"Yes"（不加引号），否则输出"No"（不加引号）。

解题时测得使用map与set均会超时。

```C++
#include <bits/stdc++.h>
using namespace std;
int a[1005];
int main(){
    ios::sync_with_stdio(false);
    int n;
    cin >> n;
    for (int i = 0; i < n;i++){
        cin >> a[i];
    }
    unordered_map<int, int> ad, su;
    for (int i = 0; i < n;i++){
        for (int j = 0; j < n;j++){
            ad[a[i] + a[j]] = 1;
            su[a[i] - a[j]] = 1;
        }
    }
    for (auto i = ad.begin(); i != ad.end();i++){
        if(su[(*i).first]){
            cout << "Yes" << '\n';
            return 0;
        }
    }
    cout << "No" << '\n';
    return 0;
}
```