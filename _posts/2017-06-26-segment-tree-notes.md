---
title:  "线段树笔记"
excerpt: "支持点修改的线段树笔记。"
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

## 支持点修改的线段树

动态范围最小值问题：给出有n个元素的数组A，需要实现两种操作：
Update(x,v):把A[x]修改为v。
Query(L,R):计算min{A[L]...A[R]}。
如果使用线段树，则可以在O(n)的时间内完成建树，O(logn)的时间内完成一次操作。

### 数据结构

按照从上到下顺序给所有结点编号为1,2,3...，每个叶子结点代表左右端点相同的一个区间，每个非叶子结点都有2个儿子。对于所有非叶子结点，它表示的区间为[l, r]，那么令mid为(l + r)/2的下整，则它的左儿子表示的区间为[l, mid]，右儿子表示的区间为[mid+1, r]。

竞赛中通常采用数组存储线段树。一般为避免数组越界，当用数组存储线段树时，会开区间长度4倍大小的数组。具体证明网上有，这里不详述。

线段树之所以能实现高效的操作，是因为每个结点代表一个区间，一般每个结点内存储管辖的区间范围（竞赛中常用全局变量或者操作函数中的形参代替这一数据来减少冗余数据量），以及其区间的特征（如对于动态范围最小值问题，一个结点里存储的是它所代表的区间当中的最小值）。

在询问某个区间的最值时，一定可以将这个区间拆分成log(n)个子区间，然后只要比较这些结点的最值域，就能得出原区间的最值了，时间复杂度O(log(n))。

更新数组某个元素的值时，首先修改对应的叶子结点的最值域，然后修改它的父结点的最值域，以及祖先结点的最值域，换言之，修改的只是线段树的叶子结点到根结点的某一条路径上的最值域，所以这一步操作的时间复杂度也是log(n)的。

```c++
//为简化代码，采用以下宏
#define lson (cur*2)
#define rson (cur*2+1)
```

### 构造

二分递归构造，将左半区间分配给左子树，右半区间给右子树。递归到叶子结点时进行回溯，对于任何一个非叶子结点，需要根据两个子结点的数据域计算它自身的数据域。

```c++
void build(int cur,int curl,int curr)
{
    if(curl==curr)setval(cur,curl,curr);//set data of current node
    int mid=(curl+(curr-curl)/2);
    if(curl<curr)
    {
        build(lson,curl,mid);
        build(rson,mid+1,curr);
        pushup(cur,curl,curr);//update by data of children
    }
}
```

### 更新

更新的具体操作根据实际需求而定，可以是覆盖祖先的数据域，也可以是累加。过程同样采用二分，当更新区间完全覆盖当前访问的区间，则更新当前区间的数据域。

```c++
void update(int cur,int curl,int curr,int left,int right,DataType val)
{
    if(curr<left||curl>right) return;
    if(right>=curr&&left<=curl) updatevalue(p,val);
    pushdown(cur,l,r);//some problem require updating lazy tag of children
    int mid=(curl+(curr-curl)/2);
    update(lson,curl,mid,left,right,val);
    update(rson,mid+1,curr,left,right,val);
    pushup(cur,curl,curr);
}
```

### 询问

类似于更新。所不同的是，修改的是用于存储（部分）询问结果的变量值，而非存储数据的线段树本身。

```c++
DataType query(int cur,int curl,int curr,int left,int right,Node &ans)
{
    if(curr<left||curl>right) return;
    if(right>=curr&&left<=curl) updatequery(p);
    pushdown(cur,curl,curr);
  	DataType ret;
    int mid=(curl+(curr-curl)/2);
    updatevalue(ret,query(lson,curl,mid,left,right,ans));
    updatevalue(ret,query(rson,mid+1,curr,left,right,ans));
    pushup(cur,curl,curr);
    return ret;
}
```

### 例题 UVAL3938

```c++
#include<bits/stdc++.h>
using namespace std;
#define lson (cur*2)
#define rson (cur*2+1)
typedef long long LL;
typedef pair<int,int> pii;
const int maxn=500005;
struct Node
{
    pii max_sub;
    int max_prefix;int max_suffix;
}node[maxn<<2];
LL prefix_sum[maxn];//use LL
LL intersum(int a,int b){return prefix_sum[b]-prefix_sum[a-1];}//use LL
pii cmp(pii a,pii b)
{
    LL sa=intersum(a.first,a.second);
    LL sb=intersum(b.first,b.second);
    if(sa==sb) return a<b?a:b;
    return sa>sb?a:b;
}
void setval(int cur,int curl,int curr)
{
    node[cur].max_sub=pii(curl,curl);
    node[cur].max_prefix=node[cur].max_suffix=curl;
}
void pushup(int cur,int curl,int curr)
{
    LL lv=intersum(curl,node[lson].max_prefix);
    LL rv=intersum(curl,node[rson].max_prefix);
    if(lv>rv) node[cur].max_prefix=node[lson].max_prefix;
    else if(lv<rv) node[cur].max_prefix=node[rson].max_prefix;
    else node[cur].max_prefix=min(node[lson].max_prefix,node[rson].max_prefix);

    lv=intersum(node[lson].max_suffix,curr);
    rv=intersum(node[rson].max_suffix,curr);
    if(lv>rv) node[cur].max_suffix=node[lson].max_suffix;
    else if(lv<rv) node[cur].max_suffix=node[rson].max_suffix;
    else node[cur].max_suffix=min(node[lson].max_suffix,node[rson].max_suffix);

    node[cur].max_sub=cmp(node[lson].max_sub,node[rson].max_sub);
    node[cur].max_sub=cmp(node[cur].max_sub,pii(node[lson].max_suffix,node[rson].max_prefix));

}
void build(int cur,int curl,int curr)
{
    if(curl==curr)setval(cur,curl,curr);
    else
    {
        int mid=curl+(curr-curl)/2;
        build(lson,curl,mid);
        build(rson,mid+1,curr);
        pushup(cur,curl,curr);
    }
}
pii query_p(int cur,int curl,int curr,int left,int right)
{
    if(node[cur].max_prefix<=right) return pii(curl,node[cur].max_prefix);
    int mid=curl+(curr-curl)/2;
    if(right<=mid) return query_p(lson,curl,mid,left,right);
    pii tmp=query_p(rson,mid+1,curr,left,right);
    tmp.first=curl;
    return cmp(tmp,pii(curl,node[lson].max_prefix));
}
pii query_s(int cur,int curl,int curr,int left,int right)
{
    if(node[cur].max_suffix>=left) return pii(node[cur].max_suffix,curr);
    int mid=curl+(curr-curl)/2;
    if(left>mid) return query_s(rson,mid+1,curr,left,right);
    pii tmp=query_s(lson,curl,mid,left,right);
    tmp.second=curr;
    return cmp(tmp,pii(node[rson].max_suffix,curr));
}
pii query(int cur,int curl,int curr,int left,int right)
{
    //printf("cur:%d,curl:%d,curr:%d,left:%d,right:%d\n",cur,curl,curr,left,right);
    if(curr<=right&&curl>=left)
    {
        return node[cur].max_sub;
    }
    int mid=curl+(curr-curl)/2;
    if(right<=mid)
    {
        return query(lson,curl,mid,left,right);
    }
    if(left>mid)
    {
        return query(rson,mid+1,curr,left,right);
    }

    pii lval=query_s(lson,curl,mid,left,right);
    pii rval=query_p(rson,mid+1,curr,left,right);
    pii mval=cmp(query(lson,curl,mid,left,right),query(rson,mid+1,curr,left,right));
    return cmp(mval,pii(lval.first,rval.second));
}
int main()
{
    //freopen("test.txt","r",stdin);
    int n,m,a,b,tmp;int ks=1;
    while(scanf("%d%d",&n,&m)==2)
    {
        prefix_sum[0]=0;
        for(int i=1;i<=n;i++)
        {
            scanf("%d",&tmp);
            prefix_sum[i]=prefix_sum[i-1]+tmp;
        }
        build(1,1,n);
        printf("Case %d:\n",ks++);
        for(int i=0;i<m;i++)
        {
            scanf("%d%d",&a,&b);
            pii ans=query(1,1,n,a,b);
            printf("%d %d\n",ans.first,ans.second);
        }
    }
    return 0;
}
```