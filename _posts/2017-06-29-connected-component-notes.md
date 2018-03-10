---
title:  "连通分量笔记"
excerpt: "连通分量简单笔记。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Competetive_Programming
tags:
  - 算法
  - 图论
  - 连通分量
---

## DFS
### 计算连通分量

DFS到底一次可以求出一个连通分量。所以对图上未访问的点不断DFS可以求得图中所有连通分量。

```c++
vector<int>G[maxn];int vis[maxn];int cc;int n;
void dfs(int u)
{
    vis[u]=1;
    //previsit(u);
    int d=G[u].size();
    for(int i=0;i<d;i++)
    {
        int v=G[u][i];
        if(!vis[v]) dfs(v);
    }
    //postvisit(u);
}
void find_cc()
{
    cc=0;
    memset(vis,0,sizeof(vis));
    for(int i=0;i<n;i++)
        if(!vis[i])
        dfs(i),cc++;
}
```
### 判断二分图

0=未着色，1=黑，2=白

二分图没有奇圈，如果一个图不是二分图，它一定包含一个奇圈

```c++
bool isbip(int u)
{
    int d=G[u].size();
    for(int i=0;i<d;i++)
    {
        int v=G[u][i];
        if(color[v]==color[i]) return 0;
        if(!color[v])
        {
            color[v]=3-color[i];
            if(!isbip(v)) return 0;
        }
    }
    return 1;
}
```
### 无向图的割顶和桥

对于无向图G,如果删除某个点u后，连通分量数目增加，称u为图的**割顶**，对于连通图，割顶就是删除之后使图不再连通的点。

DFS森林中的边称为树边，第一次处理时从后代指向祖先的边称为**反向边**。

**定理**：在无向连通图G的DFS树中，非根结点u是g的**割顶**当且仅当u存在一个子节点V,使得V及其所有后代都没有反向边连回u的祖先。

设pre(u)为开始访问结点u时的时间戳（全局访问数的值；易知，在DFS树中祖先的pre值一定比后代要小），设low(u)为u及其后代所能连回的最早的祖先的pre值，则上述定理可以简写为low(v)>=pre(u)。

作为一种特殊情况，如果v的后代只能连回v自己(low(v)>pre(u))，则(u,v)是**桥**。

```c++
vector<int>G[maxn];
vector<pair<int,int> >bridges;
int iscut[maxn];int pre[maxn];int low[maxn];
int dfs_clock=0;
int dfs(int u,int fa)
{
    int lowu=pre[u]=++dfs_clock;
    int child=0;int sz=G[u].size();
    for(int i=0;i<sz;i++)
    {
        int v=G[u][i];
        if(!pre[v])
        {
            child++;
            int lowv=dfs(v,u);
            lowu=min(lowu,lowv);
            if(lowv>=pre[u])
            {
                iscut[u]=1;
                if(lowv!=pre[u])bridges.push_back(pair<int,int>(v,u));
            }
        }
        else if(pre[v]<pre[u]&&v!=fa)//visited,less pre,not father(back edge)
            lowu=min(lowu,pre[v]);
    }
    if(fa<0&&child==1) iscut[u]=0;
    low[u]=lowu;
    return lowu;
}
```
### 无向图的双联通分量

对于一个连通图，如果任意两点至少存在两条哈密顿路径，则这个图是**点-双连通**（一般简称为双连通）的。这个要求等价于任意两条边都在同一个简单环中，即**无割顶**。

类似地，如果任意两点至少存在两条欧拉路径，则这个图是**边-双连通**的，这个要求等价于每条边都至少在一个简单环中，即**无桥**
。
点-双连通的极大子图称为**双连通分量**，不同双连通分量最多只有一个公共点，且它一定是割顶。而任意割顶都是至少两个不同双连通分量的公共点。

同理，边-双连通的极大子图称为**边-双连通分量**，除了桥不属于任何一个边-双连通分量，其他每条边恰好属于一个边-双连通分量。

求点-双联通分量算法：
```c++
int pre[maxn],iscut[maxn],bccno[maxn],dfs_clock,bcc_cnt;
vector<int>G[maxn],bcc[maxn];
struct Edge
{
    int u;int v;
};
stack<Edge>S;
int dfs(int u,int fa)
{
    int lowu=pre[u]=++dfs_clock;
    int child=0;
    for(int i=0;i<G[u].size();i++)
    {
        int v=G[u][i];
        Edge e=(Edge){u,v};
        if(!pre[v])
        {
            S.push(e);
            child++;
            int lowv=dfs(v,u);
            lowu=min(lowu,lowv);
            if(lowv>=pre[u])//find new cut
            {
                iscut[u]=1;
                bcc_cnt++;bcc[bcc_cnt].clear();//initialize current bcc
                while(1)
                {
                    Edge x=S.top();S.pop();//last visited edge
                    if(bccno[x.u]!=bcc_cnt)//point not in bcc,add
                    {
                        bcc[bcc_cnt].push_back(x.u);
                        bccno[x.u]=bcc_cnt;
                    }
                    if(bccno[x.v]!=bcc_cnt)//point not in bcc,add
                    {
                        bcc[bcc_cnt].push_back(x.v);
                        bccno[x.v]=bcc_cnt;
                    }
                    if(x.u==u&&x.v==v) break;
                }
            }
            else if(pre[v]<pre[u]&&v!=fa)//back edge
            {
                S.push(e);
                lowu=min(lowu,pre[v]);
            }
        }
    }
    if(fa<0&&child==1) iscut[u]=0;
    return lowu;
}
void find_bcc(int n)
{
    memset(pre,0,sizeof(pre));
    memset(iscut,0,sizeof(iscut));
    memset(bccno,0,sizeof(bccno));
    dfs_clock=bcc_cnt=0;
    for(int i=0;i<n;i++)
    {
        if(!pre[i]) dfs(i,-1);
    }
}
```
求边-双连通分量：先求出图中所有的桥，再dfs一次，这次保证不经过桥即可
### 有向图的强连通分量

在有向图G中，如果两个顶点间至少存在一条路径，称两个顶点强连通。

如果有向图G的每两个顶点都强连通，则称G是一个强连通图。

非强连通图有向图的极大强连通子图，称为**强连通分量**(SCC)。

如果把一个集合看成一个点，那么所有SCC构成了一个SCC图。这个SCC图不存在有向环，因此是一个有向无环图（DAG）。

求SCC的Tarjan算法：
(定义Lowlink(u)为u及其后代能追溯到的最早祖先点的pre值)
```c++
vector<int>G[maxn];
int pre[maxn],lowlink[maxn],sccno[maxn],dfs_clock,scc_cnt;
stack<int>S;
void dfs(int u)
{
    pre[u]=lowlink[u]=++dfs_clock;
    S.push(u);
    for(int i=0;i>G[u].size();i++)
    {
        int v=G[u][i];
        if(!pre[v])
        {
            dfs(v);
            lowlink[u]=min(lowlink[u],lowlink[v]);
        }
        else if(!sccno[v])
        {
            lowlink[u]=min(lowlink[u],pre[v]);
        }
    }
    if(lowlink[u]==pre[u])
    {
        scc_cnt++;
        while(1)
        {
            int x=S.top();S.pop();
            sccno[x]=scc_cnt;
            if(x==u) break;
        }
    }
}
void find_scc(int n)
{
    dfs_clock=scc_cnt=0;
    memset(sccno,0,sizeof(sccno));
    memset(pre,0,sizeof(pre));
    for(int i=0;i<n;i++)
        if(!pre[i]) dfs(i);
}
```


## 网络流算法

### Dinic
```c++
struct Edge {
  int from, to, cap, flow;
};

bool operator < (const Edge& a, const Edge& b) {
  return a.from < b.from || (a.from == b.from && a.to < b.to);
}

struct Dinic {
  int n, m, s, t;
  vector<Edge> edges;    // 边数的两倍
  vector<int> G[maxn];   // 邻接表，G[i][j]表示结点i的第j条边在e数组中的序号
  bool vis[maxn];         // BFS使用
  int d[maxn];           // 从起点到i的距离
  int cur[maxn];        // 当前弧指针

  void ClearAll(int n) {
    for(int i = 0; i < n; i++) G[i].clear();
    edges.clear();
  }

  void ClearFlow() {
    for(int i = 0; i < edges.size(); i++) edges[i].flow = 0;    
  }

  void AddEdge(int from, int to, int cap) {
    edges.push_back((Edge){from, to, cap, 0});
    edges.push_back((Edge){to, from, 0, 0});
    m = edges.size();
    G[from].push_back(m-2);
    G[to].push_back(m-1);
  }

  bool BFS() {
    memset(vis, 0, sizeof(vis));
    queue<int> Q;
    Q.push(s);
    vis[s] = 1;
    d[s] = 0;
    while(!Q.empty()) {
      int x = Q.front(); Q.pop();
      for(int i = 0; i < G[x].size(); i++) {
        Edge& e = edges[G[x][i]];
        if(!vis[e.to] && e.cap > e.flow) {
          vis[e.to] = 1;
          d[e.to] = d[x] + 1;
          Q.push(e.to);
        }
      }
    }
    return vis[t];
  }

  int DFS(int x, int a) {
    if(x == t || a == 0) return a;
    int flow = 0, f;
    for(int& i = cur[x]; i < G[x].size(); i++) {
      Edge& e = edges[G[x][i]];
      if(d[x] + 1 == d[e.to] && (f = DFS(e.to, min(a, e.cap-e.flow))) > 0) {
        e.flow += f;
        edges[G[x][i]^1].flow -= f;
        flow += f;
        a -= f;
        if(a == 0) break;
      }
    }
    return flow;
  }

  int Maxflow(int s, int t) {
    this->s = s; this->t = t;
    int flow = 0;
    while(BFS()) {
      memset(cur, 0, sizeof(cur));
      flow += DFS(s, INF);
    }
    return flow;
  }

  vector<int> Mincut() { // call this after maxflow
    vector<int> ans;
    for(int i = 0; i < edges.size(); i++) {
      Edge& e = edges[i];
      if(vis[e.from] && !vis[e.to] && e.cap > 0) ans.push_back(i);
    }
    return ans;
  }

  void Reduce() {
    for(int i = 0; i < edges.size(); i++) edges[i].cap -= edges[i].flow;
  }
};
```
### ISAP
```c++
struct Edge {
  int from, to, cap, flow;
};

bool operator < (const Edge& a, const Edge& b) {
  return a.from < b.from || (a.from == b.from && a.to < b.to);
}

struct ISAP {
  int n, m, s, t;
  vector<Edge> edges;
  vector<int> G[maxn];   // 邻接表，G[i][j]表示结点i的第j条边在e数组中的序号
  bool vis[maxn];        // BFS使用
  int d[maxn];           // 从起点到i的距离
  int cur[maxn];        // 当前弧指针
  int p[maxn];          // 可增广路上的上一条弧
  int num[maxn];        // 距离标号计数

  void AddEdge(int from, int to, int cap) {
    edges.push_back((Edge){from, to, cap, 0});
    edges.push_back((Edge){to, from, 0, 0});
    m = edges.size();
    G[from].push_back(m-2);
    G[to].push_back(m-1);
  }

  bool BFS() {
    memset(vis, 0, sizeof(vis));
    queue<int> Q;
    Q.push(t);
    vis[t] = 1;
    d[t] = 0;
    while(!Q.empty()) {
      int x = Q.front(); Q.pop();
      for(int i = 0; i < G[x].size(); i++) {
        Edge& e = edges[G[x][i]^1];
        if(!vis[e.from] && e.cap > e.flow) {
          vis[e.from] = 1;
          d[e.from] = d[x] + 1;
          Q.push(e.from);
        }
      }
    }
    return vis[s];
  }

  void ClearAll(int n) {
    this->n = n;
    for(int i = 0; i < n; i++) G[i].clear();
    edges.clear();
  }

  void ClearFlow() {
    for(int i = 0; i < edges.size(); i++) edges[i].flow = 0;    
  }

  int Augment() {
    int x = t, a = INF;
    while(x != s) {
      Edge& e = edges[p[x]];
      a = min(a, e.cap-e.flow);
      x = edges[p[x]].from;
    }
    x = t;
    while(x != s) {
      edges[p[x]].flow += a;
      edges[p[x]^1].flow -= a;
      x = edges[p[x]].from;
    }
    return a;
  }

  int Maxflow(int s, int t, int need) {
    this->s = s; this->t = t;
    int flow = 0;
    BFS();
    memset(num, 0, sizeof(num));
    for(int i = 0; i < n; i++) num[d[i]]++;
    int x = s;
    memset(cur, 0, sizeof(cur));
    while(d[s] < n) {
      if(x == t) {
        flow += Augment();
        if(flow >= need) return flow;
        x = s;
      }
      int ok = 0;
      for(int i = cur[x]; i < G[x].size(); i++) {
        Edge& e = edges[G[x][i]];
        if(e.cap > e.flow && d[x] == d[e.to] + 1) { // Advance
          ok = 1;
          p[e.to] = G[x][i];
          cur[x] = i; // 注意
          x = e.to;
          break;
        }
      }
      if(!ok) { // Retreat
        int m = n-1; // 初值注意
        for(int i = 0; i < G[x].size(); i++) {
          Edge& e = edges[G[x][i]];
          if(e.cap > e.flow) m = min(m, d[e.to]);
        }
        if(--num[d[x]] == 0) break;
        num[d[x] = m+1]++;
        cur[x] = 0; // 注意
        if(x != s) x = edges[p[x]].from;
      }
    }
    return flow;
  }

  vector<int> Mincut() { // call this after maxflow
    BFS();
    vector<int> ans;
    for(int i = 0; i < edges.size(); i++) {
      Edge& e = edges[i];
      if(!vis[e.from] && vis[e.to] && e.cap > 0) ans.push_back(i);
    }
    return ans;
  }

  void Reduce() {
    for(int i = 0; i < edges.size(); i++) edges[i].cap -= edges[i].flow;
  }
```