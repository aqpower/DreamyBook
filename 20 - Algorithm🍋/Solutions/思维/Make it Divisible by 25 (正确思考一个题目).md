---
title: Make it Divisible by 25 (正确思考一个题目)
date: 2022-06-25 16:38:54
tags: 算法
---
题目大意是给你你一个数，你可以执行无数次删除任意一位数的操作，问最少需要执行多少次后该数能够被25整除。
<!--more-->

[TOC]

# B - Make it Divisible by 25
##  Problems
It is given a positive integer *n*. In 11 move, one can select any single digit and remove it (i.e. one selects some position in the number and removes the digit located at this position). The operation cannot be performed if only one digit remains. If the resulting number contains leading zeroes, they are automatically removed.

E.g. if one removes from the number 3292532925 the 33-rd digit, the resulting number will be 32253225. If one removes from the number 2009905020099050 the first digit, the resulting number will be 9905099050 (the 22 zeroes going next to the first digit are automatically removed).

What is the minimum number of steps to get a number such that it is divisible by 2525 and **positive**? It is guaranteed that, for each *n* occurring in the input, the answer exists. It is guaranteed that the number n has no leading zeros.
##  Input
The first line contains one integer *t* (1≤t≤10^4) — the number of test cases. Then *t* test cases follow.

Each test case consists of one line containing one integer *n* (25≤n≤10^18). It is guaranteed that, for each *n* occurring in the input, the answer exists. It is guaranteed that the number *n* has no leading zeros.
##  Output
For each test case output on a separate line an integer *k* (k≥0) — the minimum number of steps to get a number such that it is divisible by 25 and positive.
##  Sample 1
###  input
```
5                                                        
0 0 0
10 75 15
13 13 17
1000 0 0
0 1000000000 0 
```
###  output
```
1 1 1
66 0 61
5 5 0
0 1001 1001
1000000001 0 1000000001
```
## Solution
我一开始的想法从最后一位数开始去除，先看是不是0或5不是就直接丢掉，再看这些数字的前一位数符合对应要求吗，再反正搞得很没有条理，思考方向也不正确，这样怎么就能得到最少次数呢？还蛮自我的，没有一套完整的逻辑，我自己都证明不了或者想到其正确性。

正解看到题目是求被25整除的数，那么很好理解，举几个例子吧，25，50，75，100，125，150，175，200......等等，可以很容易观察到他们以`25`，`50`，`75`，`00`其中之一作为*最后两位数*，而我们要求的是形成这样的数的最小去除次数，那我们不妨先枚举出能形成的这些数，再取最小去除次数即可。
问题来到了怎么枚举这些数，很简单啊，数位长度很小，所以二重循环遍历找就行了，题目得解。
附ac代码：
```c++
#include <bits/stdc++.h>
using namespace std;

int main(){
    int t;
    cin >> t;
    while(t--){
        string s;
        cin >> s;
        int mi = 1e9, si = s.size() - 1;
        for (int i = 1; i < s.size();i++){
            if(s[i] == '5'){
                for (int j = i - 1; j >= 0;j--){
                    if(s[j] == '2' || s[j] == '7'){
                        int now = i - j - 1 + si - i;
                        mi = min(mi, now);
                        break;
                    }    
                }
            } else if(s[i] == '0'){
                for (int j = i - 1; j >= 0;j--){
                    if(s[j] == '5' || s[j] == '0'){
                        int now = i - j - 1 + si - i;
                        mi = min(mi, now);
                        break;
                    }    
                }
            }
        }
        cout << mi << '\n';
    }
    return 0;
}
···
```
## 总结
总结就是务必仔细思考理性分析不骄不躁！！！