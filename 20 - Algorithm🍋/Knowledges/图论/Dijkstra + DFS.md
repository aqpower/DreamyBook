---
creation date: 2023-03-18 15:42 
---
 [[2023-03-18-星期六]]  #🌲长青  #图论  [[Dijkstra 算法堆优化实战--直捣黄龙]] [[最短路径]]

"在 Dijkstra 算法里面记录下所有的最短路径 (只考虑距离)，然后使用 DFS 遍历所有路径，选出第二标尺最优的路径"

思想是记录下每个节点在最短路径情况下的前驱节点之后再 DFS 即可形成一条路径数哦。 
最后在给定一条路径的情况下，针对这条路径的信息都可以通过边权和点权很容易求出来。

## Pre 数组求解
![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20230318154548.png)
![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20230318155143.png)

> 每次到达叶子节点时，都会产生一条完整的最短路径。需要求最短路径条数的话设置一个计数器在每次遍历到叶子节点时候加一即可。

![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20230318154734.png)
## DFS 代码
![image.png](https://jgox-image-1316409677.cos.ap-guangzhou.myqcloud.com/blog/20230318155048.png)







