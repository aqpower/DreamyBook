---
creation date: 2023-03-18 15:08 
---
 [[2023-03-18-星期六]]  [[最短路径]] #🌲长青  #算法 #图论

## 题目
本题是一部战争大片 —— 你需要从己方大本营出发，一路攻城略地杀到敌方大本营。首先时间就是生命，所以你必须选择合适的路径，以最快的速度占领敌方大本营。当这样的路径不唯一时，要求选择可以沿途解放最多城镇的路径。若这样的路径也不唯一，则选择可以有效杀伤最多敌军的路径。
### 输入格式：
输入第一行给出 2 个正整数 N（2 ≤ N ≤ 200，城镇总数）和 K（城镇间道路条数），以及己方大本营和敌方大本营的代号。随后 N-1 行，每行给出除了己方大本营外的一个城镇的代号和驻守的敌军数量，其间以空格分隔。再后面有 K 行，每行按格式`城镇1 城镇2 距离`给出两个城镇之间道路的长度。这里设每个城镇（包括双方大本营）的代号是由 3 个大写英文字母组成的字符串。
### 输出格式：
按照题目要求找到最合适的进攻路径（题目保证速度最快、解放最多、杀伤最强的路径是唯一的），并在第一行按照格式`己方大本营->城镇1->...->敌方大本营`输出。第二行顺序输出最快进攻路径的条数、最短进攻距离、歼敌总数，其间以 1 个空格分隔，行首尾不得有多余空格。

### 输入样例：

```in
10 12 PAT DBY
DBY 100
PTA 20
PDS 90
PMS 40
TAP 50
ATP 200
LNN 80
LAO 30
LON 70
PAT PTA 10
PAT PMS 10
PAT ATP 20
PAT LNN 10
LNN LAO 10
LAO LON 10
LON DBY 10
PMS TAP 10
TAP DBY 10
DBY PDS 10
PDS PTA 10
DBY ATP 10
```

### 输出样例：

```out
PAT->PTA->PDS->DBY
3 30 210
```

## AC 代码
```cpp
#include <algorithm>
#include <cmath>
#include <functional>
#include <map>
#include <iostream>
#include <map>
#include <queue>
#include <set>
#include <stack>
#include <string>
#include <vector>

using namespace std;

typedef long long LL;
typedef pair<int, int> P;
const int maxn = 205;

map<string, int> si;
map<int, string> is;
int n, k;
string home, tar;
int w[maxn];
vector<P> adj[maxn];
vector<int> pre[maxn];

bool vis[maxn];
int d[maxn];
void dijkstra(int s) {
    for (int i = 0; i < n; i++) {
        d[i] = 1e9;
    }
    // !!!
    priority_queue<P, vector<P>, greater<P>> qu;
    d[s] = 0;
    qu.push(P{0, s});
    while (!qu.empty()) {
        P top = qu.top();
        int u = top.second;
        qu.pop();
        if (vis[top.second]) {
            continue;
        }
        vis[u] = true;
        for (auto to : adj[u]) {
            int v = to.first;
            int w = to.second;
            if (!vis[v] &&  d[u] + w < d[v]) {
                d[v] = d[u] + w;
                pre[v].clear();
                pre[v].push_back(u);
                qu.push(P{d[v], v});
            } else if (d[u] + w == d[v]) {
                pre[v].push_back(u);
            }
        }
    }
}

int numpath = 0;
vector<int> nv, anspath;
int maxnum = -1, maxatt = -1;
void dfs(int now) {
    if (now == si[home]) {
        numpath++;
        int nowarr = 0, numcity = 0;
        for (int i = 0; i < nv.size(); i++) {
            nowarr += w[nv[i]];
            numcity++;
        }
        if (numcity > maxnum) {
            maxnum = numcity;
            maxatt = nowarr;
            anspath = nv;
        } else if (numcity == maxnum) {
            if (nowarr > maxatt) {
                maxatt = nowarr;
                anspath = nv;
            }
        }
        return;
    }
    for (int i = 0; i < pre[now].size(); i++) {
        nv.push_back(pre[now][i]);
        dfs(pre[now][i]);
        nv.pop_back();
    }
}

int main() {
    cin.tie(0)->ios::sync_with_stdio(false);
    cin >> n >> k >> home >> tar;
    si[home] = 0;
    is[0] = home;
    string t;
    int tnum;
    for (int i = 1; i < n; i++) {
        cin >> t >> tnum;
        si[t] = i;
        is[i] = t;
        w[i] = tnum;
    }
    string a, b;
    for (int i = 0; i < k; i++) {
        cin >> a >> b >> tnum;
        adj[si[a]].push_back(P{si[b], tnum});
        adj[si[b]].push_back(P{si[a], tnum});
    }
    dijkstra(0);
    nv.push_back(si[tar]);
    dfs(si[tar]);
    for (int i = anspath.size() - 1; i >= 0; i--) {
        cout << is[anspath[i]];
        if(i) cout << "->";
    }
    cout << '\n';
    cout << numpath << ' ' << d[si[tar]] << ' ' << maxatt;
    
    return 0;
}

```

## 实践出真知
变量命名尤其在图算法里面没有必要太繁琐，常用的变量名可以是 u v w a b e。adj 替换成 e 就很不错，每次取堆顶别用 top 变量，代码看起来会很繁琐不容易找错。

**优先队列**的定义很容易忽略**要构造成小根堆**，即堆顶的元素应当是距离最小的。有两种方法，一个是重载元素的小于号，第二个是自定义比较容器
>  `priority_queue<P, vector<P>, greater<P>> qu;`

为了节省实践个人更倾向与第二种，第一种一般是在自定义结构体时使用，变量命名更加清晰易懂，详细可见 [[STL 容器自定义比较函数]] 。

当需要==获得具体路径的时候或者结合有点权重==的时候，个人喜欢的方法：求最短路径优化搜索的同时**记录下每个节点的 pre 值**，即最短路径情况下可以访问到这个节点的节点。

![[Dijkstra + DFS]]

