---
title:  "图论基础算法"
excerpt: "伪代码整合。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Competetive_Programming
tags:
  - 图论
  - 最短路
  - 最小生成树
  - 网络流
---

本周的主要学习/复习算法总结。

今天看了一下去年暑假写的博客，感觉简直惨不忍睹。为了题目量一味抄模板，却不注重自己对算法的理解和思考，一直没有什么长进也是必然的了。删掉了几篇质量特别低的博文。

```c++
struct tps//拓扑排序判图是否是DAG
{
    bool dfs(int i)
    {
        //当前点标号
        //访问所有与当前点相连的点
            //如果目标点访问过，返回false
            //如果目标点没访问过，对它dfs，如果返回false则返回false
        //返回true
    }
    bool TopoSort()
    {
        //对每个点dfs，如果其中任意一点返回false，说明有环，返回false
    }
}
void Kruskal()
{
    //初始化MST
    //把所有点的cc设为自己
    //遍历所有点
        //遍历所有点
            //如果两点cc不同，把其中一个的cc设为另一个的cc，把这条边加入MST
}
//对于最短路问题，一般用队列代替对所有点的遍历，优化时间复杂度；用优先队列代替显式查找
void Dijkstra()
{
    //把所有点的距离设为无限，起点的距离设为0，起点加入优先队列
    //当队列不空，重复
        //队首为目前距离值最小且未访问过的点
        //访问这个点，更新所有从这个点出发能到达的点的距离，把它们加入队列
    //返回终点的距离值
}
void BellmanFord()
{
    //把所有点的距离设为无限，起点的距离设为0，起点加入队列
    //当队列不空，重复
        //遍历所有边
            //若from点的距离不是无限，更新to点的距离
    //返回终点的距离值
}
void EdmondsKarp()
{
    //总流量=0
    //当图中存在增广路，重复
        //用BFS找一条从起点到终点的路径；如果找不到，说明不存在增广路，退出算法
        //对于此路径上的所有边进行增广，每次增广更新总容量
    //返回总容量的值
}
```
