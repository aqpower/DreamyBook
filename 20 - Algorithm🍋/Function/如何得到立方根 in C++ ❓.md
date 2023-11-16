---
title: 如何得到立方根 in C++ ❓
date: 2022-07-21 10:25:48
tags: 算法
---

众所周知，在c++，开平方根可以使用 `std::sqrt(n)` ，或者`std::pow(x,1/2.0)`  
那么开立方根呢，类比的话很显然 `std::pow(x,1/3.0)`就可以实现。

但是，屡次做数学类算法题时用pow开三次方根常常遇到很严重的精度问题导致百思不得其解，查阅一番之后发现使用pow去开立方根精度是很差的，只能保证一般结果正确。

<!--more-->

pow(x, y) from <cmath> does NOT work if x is negative and y is non-integral.

翻阅官方文档 [cppreference.com](https://en.cppreference.com/w/cpp/numeric/math/pow)
>Error handling
> - Errors are reported as specified in math_errhandling
> - If base is finite and negative and exp is finite and non-integer, a domain error occurs and a range error may occur.
> - If base is zero and exp is zero, a domain error may occur.
> - If base is zero and exp is negative, a domain error or a pole error may occur.

## 结论
解决开立方根的方法是：使用C++11引进的 `std::cbrt`，可以对负数开立方根并且精度问题也得到了解决。

## 参考文章
[[1] finding cube root in C++?](https://stackoverflow.com/questions/4269069/finding-cube-root-in-c/4269119#4269119)  
[[2] How can I obtain the cube root in C++?](https://stackoverflow.com/questions/18103769/how-can-i-obtain-the-cube-root-in-c)