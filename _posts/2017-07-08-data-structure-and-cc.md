---
title:  "数据结构与连通分量"
excerpt: "伪代码整合。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Competetive_Programming
tags:
  - 数据结构
  - 连通分量
---

最近的主要学习/复习内容总结。

## 树状数组
耗时常数小于线段树
```c++
//得到末尾0的个数，x+lowbit(x)得到父结点，x-lowbit(x)得到孩子结点
int lowbit(int x){return x&-x}
//求和
int sum(int x)
{
    int ret=0;
    while(x)
    {
        ret+=vis[x];
        x-=lowbit(x);
    }
    return ret;
}
//更新
int update(int x,int v)
{
    while(x<=maxv)
    {
        vis[x]+=v;
        x+=lowbit(x);
    }
}
```
## RMQ
```c++
//预处理
void init(int n)
{
    for(int i=1;i<=maxn;i++) logs[i]=log2(i);//查询次数较多时可以打log表
    for(int i=1;i<=n;i++) st[0][i]=a[i];
    for(int i=1;i<maxd;i++)
        for(int j=1;j<=n;j++)
        if(j+(1<<i)-1>n) continue;
        else st[i][j]=comp(st[i-1][j],st[i-1][j+(1<<i-1)]);
}
//查询
void query(int L,int R)
{
    int k=logs[R-L+1];
    return comp(st[k][L],st[k][R-(1<<k)+1]);
}
```
## 线段树
结点中存放区间信息
### 点更新
```c++
//建树
void build(int left,int right,int cur)
{
    //如果左/右端点重合，建立当前结点，返回
    //递归建左儿子
    //递归建右儿子
    //用子树更新当前结点
}
//用全局变量L,R来记录查询或更新时的总区间
//查询
DataType query(int left,int right,int cur)
{
    //如果当前区间完全包括查询区间，返回当前结点的值
    //如果查询区间的右端点不在当前区间中点的左侧，递归查询当前结点的右儿子，查询值累加到返回值上
    //如果查询区间的左端点不在当前区间中点的右侧，递归查询当前结点的左儿子，查询值累加到返回值上
    //用子树更新当前结点
    //返回查询值
}
//更新
void update(int left,int right,int cur,int val)
{
    //如果当前区间完全包括更新区间，更新当前结点的值
    //如果更新区间的右端点在当前区间的左侧，递归更新当前结点的左儿子
    //否则递归更新当前结点的右儿子
    //用子树更新当前结点
}
```
### 区间更新
建立一个结点时初始化标记
更新一个结点时更新标记
每次向子结点递归进行更新或者查询前，先向子结点传递标记
## Treap
一个treap结点具有左右儿子标号、优先级、值
```c++
void rturn(int &k)
{
    //设原k的左儿子为tmp,tmp的右儿子为tr,则右旋后k变为tmp的右儿子，tr变为k的左儿子，最后把根设为tmp
}
void lturn(int &k)
{
    //设原k的右儿子为tmp,tmp的左儿子为tl,则左旋后k变为tmp的左儿子，tl变为k的右儿子，最后把根设为tmp
}
void insert(int &root,int key)
{
    //如果root是当前的根，建立新结点，标号是结点数，值是key,优先级是随机值，左右儿子为空，返回
    //如果当前root的值和key相同说明是重复结点，返回
    //如果key大于当前root的值，在当前root的右儿子插入，如果右儿子的优先级大于root，对root左旋
    //否则在当前root的左儿子插入，如果左儿子的优先级大于root，对root右旋
}
void remove(int &root,int key)
{
    //如果key和root的值相等
    {
        //如果root没有右儿子有左儿子，用左儿子替代它
        //如果root没有左儿子有右儿子，用右儿子替代它
        //否则
        {
            //如果右儿子优先权更大，把root右旋，从右儿子删除
            //否则把root左旋，从左儿子删除
        }
    }
    //如果key大于root的值，从右儿子删除
    //否则从左儿子删除
}
void query(int root,int key)
{
    //同普通BST
}
```
## Splay
```c++
void rotate(int cur,int &root)//交换cur和父亲
{
    //如果cur的父亲是root，用cur替代root
    //否则如果cur的祖父是root，用cur替代祖父儿子中root的位置
    //cur原本的祖父设为cur的父亲
    //cur设为cur原本的父亲的父亲
    //如果cur是原本父亲的左儿子，则把cur原本的父亲设为cur右儿子的父亲
    //否则把cur原本的父亲设为cur左儿子的父亲
    //如果cur是原本父亲的左儿子，则把cur的右儿子设为原本父亲的左儿子
    //否则把cur的左儿子设为原本父亲的右儿子
    //如果cur是原本父亲的左儿子，把cur原本父亲设为cur的右儿子
    //否则把cur原本父亲设为cur的左儿子
}
void splay(int from,int &to)
{
    //当from!=to
    {
        //如果from的父亲!=to
        {
            //如果from,它的父亲，它的祖父不在一条直线上，rotate(from,to);
            //否则 rotate(fa[from],to);
        }
        //rotate(from,to);
    }
}
void insert(int root,int key)
{
    //基本同treap，注意成功插入之后，需要把新结点splay到全局根
}
void remove(int key)
{
    //把key splay到根
    //如果key只有一个孩子，用它作为根
    //否则把key的右子树中的最左结点取代key，key的原右儿子设为全局根
    //更新全局根的父亲
}
```
## DFS
### 计算连通分量
```c++
void dfs(int cur)
{
    //标记cur，遍历cur的所有边，如果没有标记过目标点，则对该点dfs
}
void findcc()
{
    //遍历所有点，如果没有标记过，则对其dfs，连通分量数+1
}
```
### 判断二分图
```c++
bool isbi(int cur)
{
    //遍历cur的所有边，对于目标点：
    //如果和cur颜色相同，返回false
    //如果没有上过色，对它上和cur相反颜色的色
    //对目标点进行isbi，如果结果为false，则返回false
}
```
### 无向图的割顶和桥
```c++
int dfs(int cur,int fa)
{
    //更新全局计数器并赋值给cur的祖先和记录最低祖先值的变量low
    //遍历cur的所有边：
        //如果没有祖先，对目标点dfs，用它的最低祖先值更新low
        //如果目标点的最低祖先值大于等于cur的祖先值，说明cur是割，如果不等于，说明这条边是桥
        //如果目标点有祖先且小于cur的祖先，且目标点不是cur的父亲，用它的祖先值更新low
    //如果cur是根而且只有一个孩子，cur不是割
    //更新cur最低祖先值并返回
}
```
### 无向图的双联通分量
```c++
int dfs(int cur,int fa)
{
    //更新全局计数器并赋值给cur的祖先和记录最低祖先值的变量low
    //遍历cur的所有边：
        //如果没有祖先，对目标点dfs，用它的最低祖先值更新low,把边入栈
        //如果目标点的最低祖先值大于等于cur的祖先值，说明cur是割，更新当前连通分量；当栈中的边不为当前边，把栈顶的边出栈，对于这条边，把两点中不在此连通分量的加入该连通分量
        //如果目标点有祖先且小于cur的祖先，且目标点不是cur的父亲，用它的祖先值更新low
    //如果cur是根而且只有一个孩子，cur不是割
    //更新cur最低祖先值并返回
}
void findbcc(int n)
{
    //对于所有点，如果它没有祖先，则把它作为根进行dfs
}
```
### 有向图的强连通分量
```c++
void dfs(int cur)
{
        //更新全局计数器并赋值给cur的祖先和最低祖先值，把cur入栈
    //遍历cur的所有边，对于目标点：
        //如果没有祖先，对它dfs，用它的最低祖先值更新cur的最低祖先值
        //如果没有scc，用它的最低祖先值更新cur的祖先
    //如果cur的祖先就是最低祖先，更新全局计数器值，把cur中cur上面的所有点出栈，并用全局计数器标出它们的scc值
}
void findscc(int n)
{
    //对于所有点，如果它没有祖先，则把它作为根进行dfs
}
```