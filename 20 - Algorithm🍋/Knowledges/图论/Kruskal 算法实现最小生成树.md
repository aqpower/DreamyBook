## Kruskal 算法
![kruskal_gif](https://oi-wiki.org/graph/images/mst-2.apng)
Kruskal 算法是一种常见并且好写的最小生成树算法，由 Kruskal 发明。该算法的基本思想是从小到大加入边，是个贪心算法。
好了，我总是忘记的算法又多了一个，**Kruskal 算法**！
### 前置知识
[并查集](https://oi-wiki.org/ds/dsu/)、[贪心](https://oi-wiki.org/basic/greedy/)、[图的存储](https://oi-wiki.org/graph/save/)。
### 实现
==理解一==
算法虽简单，但需要相应的数据结构来支持……具体来说，维护一个森林，查询两个结点是否在同一棵树中，连接两棵树。
抽象一点地说，维护一堆 **集合**，查询两个元素是否属于同一集合，合并两个集合。
其中，查询两点是否连通和连接两点可以使用并查集维护。
==理解二==
Kruskal 算法基于贪心思想，它的核心思想是通过将边按照权值从小到大排序，逐步将边添加到生成树中，同时避免形成环。
下面是 Kruskal 算法的步骤：
1.  初始化一个空的最小生成树集合。
2.  将图中所有的边按照权值从小到大排序。
3.  逐个考虑排序后的边，如果当前边连接的两个顶点不在同一个连通分量中，则将这条边添加到最小生成树集合中，并将这两个顶点所在的连通分量合并。
4.  重复步骤 3 直到最小生成树集合包含了所有的顶点。

在 Kruskal 算法中，使用并查集来维护连通性。在每次添加一条边之前，判断这条边连接的两个顶点是否在同一个连通分量中，可以使用并查集来实现。如果两个顶点不在同一个连通分量中，就将它们所在的连通分量合并起来。
Kruskal 算法的时间复杂度为 O (E log E)，其中 E 是边的数量。这个时间复杂度的计算是基于对边进行排序的复杂度和执行并查集操作的复杂度。
```C++
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

const int MAXN = 10005; // 最大顶点数
const int MAXM = 100005; // 最大边数

int n, m;
int f[MAXN]; // 并查集数组，用于维护连通性

struct Edge {
    int u, v, w;
    Edge() {}
    Edge(int u, int v, int w): u(u), v(v), w(w) {}
    bool operator<(const Edge& e) const {
        return w < e.w;
    }
} e[MAXM]; // 存储所有边的数组

int find(int x) { // 并查集查找操作
    return x == f[x] ? x : f[x] = find(f[x]);
}

void merge(int x, int y) { // 并查集合并操作
    f[find(x)] = find(y);
}

int kruskal() {
    sort(e, e + m); // 将所有边按照权值从小到大排序
    int cnt = 0, ans = 0;
    for (int i = 0; i < n; i++) {
        f[i] = i; // 初始化并查集数组
    }
    for (int i = 0; i < m; i++) {
        if (find(e[i].u) != find(e[i].v)) { // 判断加入这条边是否会形成环
            merge(e[i].u, e[i].v); // 将这条边的两个端点合并
            ans += e[i].w; // 将这条边的权值加入最小生成树的总权值
            cnt++; // 统计加入的边数
            if (cnt == n - 1) { // 边数已达到 n-1 条，生成树已经形成
                break;
            }
        }
    }
    return ans;
}

int main() {
    cin >> n >> m;
    for (int i = 0; i < m; i++) {
        int u, v, w;
        cin >> u >> v >> w;
        e[i] = Edge(u, v, w);
    }
    int ans = kruskal();
    cout << ans << endl;
    return 0;
}
```
### 本人第一次实践忽略的点
1. 统计加入边的次数作为退出循环的条件，`cnt == n - 1` 当满足这个条件即退出循环。
2. kruskal () 可以把最后求出的权值作为返回值。