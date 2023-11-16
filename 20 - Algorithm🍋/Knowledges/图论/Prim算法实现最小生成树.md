Prim 算法是另一种常见并且好写的最小生成树算法。该算法的基本思想是从一个结点开始，不断加点（而不是 Kruskal 算法的加边）。
![Fetching Title#clm6](https://oi-wiki.org/graph/images/mst-3.apng)
## 原理
Prim 算法是一种用于寻找加权无向图的最小生成树的贪心算法。算法的基本思想是从一个任意节点开始，不断找到与当前生成树连接的最小权值的边所连接的顶点，并将该顶点加入生成树中，直到所有的顶点都被加入到生成树中。

具体来说，Prim算法的实现步骤如下：

1.  选定一个任意的起始顶点，将其加入生成树中。
2.  以该顶点为起点，找到与之相连的所有边，并记录这些边连接的顶点及其权值。
3.  从上述记录中选择权值最小的边所连接的顶点，将该顶点加入到生成树中，并将该边加入到生成树的边集合中。
4.  重复步骤2和3，直到所有的顶点都被加入到生成树中。

可以使用优先队列来实现Prim算法，以快速查找当前生成树和其他顶点之间的最小边。具体来说，每次从队列中取出权值最小的边所连接的顶点，并将其加入到生成树中。

## 实现
```C++
#include <iostream>
#include <cstring>
using namespace std;

const int MAXN = 1005;
const int INF = 0x3f3f3f3f;

int G[MAXN][MAXN];  // 存储图的邻接矩阵
int dist[MAXN];  // 存储当前生成树到每个顶点的最短距离
bool vis[MAXN];  // 存储每个顶点是否已经加入生成树中

void prim(int s, int n) {
    memset(vis, false, sizeof(vis));
    memset(dist, INF, sizeof(dist));
    dist[s] = 0;
    for (int i = 0; i < n; i++) {
        int v = -1;
        for (int j = 1; j <= n; j++) {
            if (!vis[j] && (v == -1 || dist[j] < dist[v])) {
                v = j;
            }
        }
        vis[v] = true;
        for (int u = 1; u <= n; u++) {
            if (!vis[u] && G[v][u] < dist[u]) {
                dist[u] = G[v][u];
            }
        }
    }
}
```

### 堆优化
```C++
vector<pair<int, int>> G[MAXN];  // 存储图的邻接表
int dist[MAXN];  // 存储当前生成树到每个顶点的最短距离
bool vis[MAXN];  // 存储每个顶点是否已经加入生成树中

struct Edge {
    int u, v, w;
    bool operator>(const Edge& rhs) const {
        return w > rhs.w;
    }
};

void prim(int s) {
    priority_queue<Edge, vector<Edge>, greater<Edge>> pq;
    memset(vis, false, sizeof(vis));
    memset(dist, INF, sizeof(dist));
    dist[s] = 0;
    pq.push({-1, s, 0});  // 从起点开始加入生成树
    while (!pq.empty()) {
        Edge e = pq.top();
        pq.pop();
        if (vis[e.v]) continue;
        vis[e.v] = true;
        for (auto p : G[e.v]) {
            int u = p.first, w = p.second;
            if (!vis[u] && w < dist[u]) {
                dist[u] = w;
                pq.push({e.v, u, w});
            }
        }
    }
}

```