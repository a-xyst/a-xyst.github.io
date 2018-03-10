---
title:  "线段树简单笔记2"
excerpt: "支持区间修改的线段树笔记。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Competetive_Programming
tags:
  - 算法
  - 数据结构
  - 线段树
---

## 支持区间修改的线段树

如果要进行区间操作，则需要对线段树进行一定修改。一次点操作最多影响logn个结点，但一次区间操作可能会影响整个线段树。因此为提高效率，进行区间修改时不可能直接操作每一个子结点。
线段树在进行区间更新的时候，为了提高更新的效率，每次更新只更新到更新区间完全覆盖线段树结点区间为止，这样就会导致被更新结点的子孙结点的区间得不到需要更新的信息，所以每次更新的时候只要更新区间完全覆盖节点区间，就在被更新结点上打上一个标记，称为懒标记，表示这个区间的元素有一个待累加值。 当下次访问到有懒标记节点时，如果要继续往下访问，则把懒标记也往下传递，同时取消该节点的懒标记。

### 与点修改型线段树的区别

1 build时初始化懒标记

2 update时，若当前区间被询问区间完全覆盖，则更新懒标记和值

3 update和query都加入新的pushdown函数，实现懒标记传递，即根据当前节点的懒标记对子节点的懒标记和值进行更新，同时去除当前点的懒标记

### 例题 UVA11992

```c++
//本题同样是数组开小不判RE，直接WA，如果Udebug没有问题，但是却wa，
//可能是输出格式问题(win和linux,int和longlong)或者数组大小问题
#include<bits/stdc++.h>
using namespace std;
#define lson (cur*2)
#define rson (cur*2+1)
typedef long long LL;
const int INF=0x3f3f3f3f;
int Left;int Right;int v;int op;
const int maxn=(20005)<<4;
struct Node
{
    int sumv[maxn];int minv[maxn];int maxv[maxn];
    int addv[maxn];int setv[maxn];
    void pushup(int cur,int curl,int curr)//更新当前结点
    {
        if(curr>curl)//根据子结点的值，更新当前结点的值
        {
            sumv[cur]=sumv[lson]+sumv[rson];
            minv[cur]=min(minv[lson],minv[rson]);
            maxv[cur]=max(maxv[lson],maxv[rson]);
        }
        if(setv[cur]>=0)//如果当前结点有置数标记，根据置数标记的值，更新当前结点的值
        {
            minv[cur]=maxv[cur]=setv[cur];
            sumv[cur]=setv[cur]*(curr-curl+1);
        }
        if(addv[cur]!=0)//如果当前结点有加法标记，根据加法标记的值，更新当前结点的值（先置后加）
        {
            minv[cur]+=addv[cur];
            maxv[cur]+=addv[cur];
            sumv[cur]+=addv[cur]*(curr-curl+1);
        }
    }
    void pushdown(int cur)//向子结点传递标记
    {
        if(setv[cur]>=0)//如果当前结点有置数标记，把标记传给子结点，清空【子结点的加法标记】，和当前结点的置数标记
        {
            setv[lson]=setv[rson]=setv[cur];
            addv[lson]=addv[rson]=0;
            setv[cur]=-1;
        }
        if(addv[cur]!=0)//如果当前结点有加法标记，把标记传给子结点，清空当前结点的加法标记
        {
            addv[lson]+=addv[cur];
            addv[rson]+=addv[cur];
            addv[cur]=0;
        }
    }
    void update(int cur,int curl,int curr)
    {
        if(Left<=curl&&Right>=curr)
        {
            if(op==1) addv[cur]+=v;
            else
            {
                setv[cur]=v;
                addv[cur]=0;
            }
        }
        else
        {
            pushdown(cur);
            int mid=curl+(curr-curl)/2;
            if(Left<=mid) update(lson,curl,mid);
            else pushup(lson,curl,mid);
            if(Right>mid) update(rson,mid+1,curr);
            else pushup(rson,mid+1,curr);
        }
        pushup(cur,curl,curr);
    }
    int query(int cur,int curl,int curr,int &sm,int &mn,int &mx)
    {
        pushup(cur,curl,curr);
        if(Left<=curl&&Right>=curr)
        {
            sm=sumv[cur];
            mn=minv[cur];
            mx=maxv[cur];
        }
        else
        {
            pushdown(cur);
            int mid=curl+(curr-curl)/2;
            int lsm=0,lmn=INF,lmx=-INF;
            int rsm=0,rmn=INF,rmx=-INF;
            if(Left<=mid) query(lson,curl,mid,lsm,lmn,lmx);
            else pushup(lson,curl,mid);
            if(Right>mid) query(rson,mid+1,curr,rsm,rmn,rmx);
            else pushup(rson,mid+1,curr);
            sm=lsm+rsm;
            mn=min(lmn,rmn);
            mx=max(lmx,rmx);
        }
    }
}nds[25];//开二维矩阵，row=25（实际上可以拼接成一维线段树）
int main()
{
    //freopen("test.txt","r",stdin);
    //freopen("out.txt","w",stdout);
    int r,c,m;
    int q,w,x,y,z;
    while(scanf("%d%d%d",&r,&c,&m)==3)
    {

        memset(nds,0,sizeof(nds));
        for(int i=1;i<=r;i++)
        {
            memset(nds[i].setv,-1,sizeof(nds[i].setv));
            nds[i].setv[1]=0;
        }
        while(m--)
        {
            scanf("%d%d%d%d%d",&op,&w,&Left,&y,&Right);
            if(op==3)
            {
                int sm=0,mn=INF,mx=-INF;
                for(int i=w;i<=y;i++)
                {
                    int tsm,tmn,tmx;
                    nds[i].query(1,1,c,tsm,tmn,tmx);
                    sm+=tsm;mn=min(mn,tmn);mx=max(mx,tmx);
                }
                printf("%d %d %d\n",sm,mn,mx);
            }
            else
            {
                scanf("%d",&v);
                for(int i=w;i<=y;i++) nds[i].update(1,1,c);
            }
        }
    }
    return 0;
}
```