---
creation date: 2023-03-19 14:02 
---
 [[2023-03-19-星期日]]  #🌲长青  #算法 #动态规划
 ## 题意
N位同学站成一排，体育老师要请其中的(N-K)位同学出列，使得剩下的K位同学排成山峰形状。
山峰形状是指这样的一种队形：设K位同学从左到右依次编号为1，2…，K，他们的身高分别为T1​，T2​，…，TK​， 则他们的身高满足T1​<...Ti−1​<Ti​>Ti+1​…>TK​(1<=i<=K)。

你的任务是，已知所有 N 位同学的身高，计算最少需要几位同学出列，可以使得剩下的同学排成山峰形状。
### 输入格式:
第一行是一个整数N(2<=N<=100)，表示同学的总数。第一行有n个整数，用空格分隔，第i个整数Ti(130<=Ti<=230)是第i位同学的身高(厘米)。
### 输出格式:
一行，这一行只包含一个整数，就是最少需要几位同学出列。
### 输入样例:
```in
8
186 186 150 200 160 130 197 220
```
### 输出样例:
```out
4
```
【数据规模】
对于50％的数据，保证有n<=20；
对于全部的数据，保证有n<=100。
## 思考
看到题目的第一眼我是想到了枚举，既然是最少需要几位同学出列，那就意味着山峰长度最长，山峰必然是有一个峰顶的，那么我去**枚举出以每个同学作为峰顶的情况**进而求得最长长度不久得解了。
但是以每个同学作为峰顶枚举，就算明确了哪个是峰顶左边是递增右边递减，其实还是很复杂的，那位同学到底该不该留下来，这就是我当时的想法，于是放弃了。
进而我采取枚举出所有队伍的情况再判断队伍是否满足题意，确实是可行的，但是复杂度直接爆炸，只能拿一半的分。

糊涂啊，太久没有做过动态规划的题目了，这不就是**最长递增子序列**吗，唉，唉，唉。~~真的太蠢了~~
我的思路是可行的，枚举每位同学作为山顶即可。唉。

不过这也说明了==枚举==，的重要性!

## 实现
一遍过！哈哈，需要注意的是右边需要的是以该点为首的最长递减子序列我们转化为该点为结尾的最长递增子序列。
```cpp
#include <algorithm>
#include <cmath>
#include <map>
#include <iostream>
#include <map>
#include <set>
#include <stack>
#include <string>
#include <vector>

using namespace std;

typedef long long LL;
typedef pair<int, int> P;
const int maxn = 105;
int a[maxn];
int dp[maxn];
int dp2[maxn];
int main() {
    cin.tie(0)->ios::sync_with_stdio(false);
    int n;
    cin >> n;
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
    }
    for (int i = 1; i <= n; i++) {
        dp[i] = 1;
        dp2[i] = 1;
    }
    for (int i = 2; i <= n; i++) {
        for (int j = 1; j < i; j++) {
            if (a[i] > a[j]) {
                dp[i] = max(dp[i], dp[j] + 1);
            }
        }
    }
    for (int i = n; i >= 1; i--) {
        for (int j = n; j > i; j--) {
            if (a[i] > a[j]) {
                dp2[i] = max(dp2[i], dp2[j] + 1);
            }
        }
    }
    int ma = -1;
    for (int i = 1; i <= n; i++) {
        int t = dp[i] + dp2[i] - 1;
        ma = max(ma, t);
    }
    cout << n - ma;

    return 0;
}

```








