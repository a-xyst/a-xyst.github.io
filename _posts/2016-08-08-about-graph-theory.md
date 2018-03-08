---
title:  图论资料整理
excerpt: "资料、例题和模板。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Competetive_Programming
tags:
  - 算法
  - 图论
  - 最短路
  - 最小生成树
---

今天之前基本没有做过图论题，所以基本只是照着题解按自己的习惯重写了一遍(Floyd除外)，根据学搜索的经验，最好等把模板敲熟了再试着自己做。

因为询问值一般从1开始，所以我也把点和边都改成从1开始。

发现HDOJ支持#include<bits/stdc++.h>，但是POJ不支持。

### 并查集

把每个元素归入集合，每个集合由一个元素做代表。

建立一个集合的时间复杂度为O(1)，N次合并M查找的时间复杂度为O(M Alpha(N))，在很大的范围内Alpha(N)的值可以看成是不大于4的，所以并查集的操作可以看作是线性的。

HDU 1232

参考代码：

```c
#include<bits/stdc++.h>
using namespace std;
int aset[1005];
int Find(int n)
{
    if(aset[n]) return Find(aset[n]);
    return n;
}
void Merge(int a,int b)
{
    int a1=Find(a);
    int b1=Find(b);
    if(a1!=b1) aset[a1]=b1;
}
int main()
{
    int n,m,t1,t2;
    while(scanf("%d",&n)&&n)
    {
        scanf("%d",&m);
        memset(aset,0,sizeof(aset));
        while(m--)
        {
            scanf("%d%d",&t1,&t2);
            Merge(t1,t2);
        }
        int ans=-1;
        for(int i=1;i<=n;i++) if(!aset[i]) ans++;
        printf("%d\n",ans);
    }
    return 0;
}
```

### 最短路

求带权图两点间的最短路。

#### Floyd

时间复杂度O(N^3)，可求多点到多点的最短路。

HDU 1596(Floyd)

参考代码：

```c
#include<bits/stdc++.h>
using namespace std;
double mat[1005][1005];
void Floyd(int n)
{
    for(int k=1;k<=n;k++)
        for(int i=1;i<=n;i++)
            for(int j=1;j<=n;j++)
                mat[i][j]=max(mat[i][j],mat[i][k]*mat[k][j]);
}
int main()
{
    int n;
    while(scanf("%d",&n)!=EOF)
    {
        double tem;
        for(int i=1;i<=n;i++)
            for(int j=1;j<=n;j++)
                scanf("%lf",&mat[i][j]);
        Floyd(n);
        int t;
        scanf("%d",&t);
        while(t--)
        {
            int t1,t2;
            scanf("%d%d",&t1,&t2);
            if(mat[t1][t2]) printf("%.3lf\n",mat[t1][t2]);
            else printf("What a pity!\n");
        }
    }
    return 0;
}
```

#### Dijkstra

时间复杂度为O(N^2)，不断找图上到不在最短路中且总权值最小的点，然后加入最短路。只适用于无负权边的图，可求起点到图上每一点的最短路。

HDU 2112(Dijkstra)

这道题的点是以字符串形式输入的，比较了几个代码，觉得用map比较美观。

参考代码：

```c
#include<bits/stdc++.h>
using namespace std;
const int INF=0x3f3f3f3f;
int dis[155],len[155][155];
bool vis[155];
void Dij(int st,int ed)
{
    memset(vis,0,sizeof(vis));
    for(int i=st;i<=ed;i++) dis[i]=(i==st?0:INF);
    for(int i=st;i<=ed;i++)
    {
        int pos=-1,tem=INF;
        for(int j=st;j<=ed;j++)
            if(!vis[j]&&dis[j]<tem) tem=dis[pos=j];
        
        if(pos==-1) break;
        vis[pos]=1;
        for(int j=st;j<=ed;j++)
            dis[j]=min(dis[j],dis[pos]+len[pos][j]);
    }
}
int main()
{
    int n,iter,tdis,flag;
    char a[30],b[30],fro[30],to[30];
    map<string,int>pic;
    while(scanf("%d",&n)&&n!=-1)
    {
        pic.clear();
        memset(len,INF,sizeof(len));
        flag=0;
        scanf("%s%s",fro,to);
        if(strcmp(fro,to)==0) flag=1;
        pic[fro]=1;
        pic[to]=2;
        iter=3;
        for(int i=1;i<=n;i++)
        {
            scanf("%s%s%d",a,b,&tdis);
            if(!pic[a]) pic[a]=iter++;
            if(!pic[b]) pic[b]=iter++;
            len[pic[a]][pic[b]]=len[pic[b]][pic[a]]=tdis;
        }
        if(flag)
        {
            printf("0\n");
            continue;
        }
        Dij(1,iter);
        if(dis[2]==INF) printf("-1\n");
        else printf("%d\n",dis[2]);
    }
    return 0;
}
```

##### 优先队列优化Dijkstra

时间复杂度可降到O(NlogN)。常规的Dijkstra算法复杂度较高，因为要花大量时间来找当前已知的距顶点距离最小的值，所以用优先队列（值小的先出队列）优化可提高效率。

```c
struct Node{
    int pos;
    int dis;
    friend bool operator <(const node &a,const node &b)
   {
      return a.dis>b.dis;//（小顶）
   }
}ns[100];
const int INF=100000;
const int n=10;
void Pqdij(int st)
{
    memset(vis,0,sizeof(vis));
    for(int i=1;i<=n;i++) d[i]=(i==st?0:INF);
    parent[st]=st;
    priority_queue<node>pq;
    pq.push((node){st,d[st]});
    for(int i=1;i<=n;i++)
    {
        int k=-1;
        while(!pq.empty()&&vis[k=(pq.top().pos)]) pq.pop();
        if(k==-1) break;
        vis[k]=1;
        for(int j=1;j<=n;i++)
        if(d[j]>d[k]+w[k][j])
        {
            d[j]=d[k]+w[k][j];
            parent[j]=k;
            pq.push((node){j,d[j]});
        }
    }
}
```

```c
#define pa pair<int,int>
struct edge{
	int to,next,v;
}e[5000005];
priority_queue<pa,vector<pa>,greater<pa> >q;
void insert(int u,int v,int w)
{
	e[++cnt]=(edge){v,head[u],w};head[u]=cnt;
}
void dijkstra()
{
	for(int i=0;i<n;i++)dis[i]=INF;dis[0]=0;
	q.push(make_pair(0,0));//(value,position)
	while(!q.empty())
	{
		int now=q.top().second;
		q.pop();
		if(vis[now])continue;
		vis[now]=1;
		for(int i=head[now];i;i=e[i].next)
            if(dis[e[i].to]>dis[now]+e[i].v)
            {
                dis[e[i].to]=dis[now]+e[i].v;
                q.push(make_pair(dis[e[i].to],e[i].to));
            }
	}
}
```

#### Bellman-Ford

适用于有负权边的图，可求起点到图上每一点的最短路。时间复杂度O(VE)，图上所有点按顺序修正临接点，重复V-1次，如果此时还能继续修正，则必有负环。

```c
struct Edge
{
    int u;
    int v;
    int w;
}edge[100];
bool Bell()
{
    for(int i=0;i<nn;i++)
        for(int j=0;j<en;j++)
            if(dis[edge[j].u]>dis[edge[j].v]+edge[j].w)
                dis[edge[j].u]=dis[edge[j].v]+edge[j].w;
    for(int i=0;i<en;i++)//判负环
        if(dis[edge[i].u]>dis[edge[i].v]+edge[i].w) return 0;
    return 1;
}
```

##### SPFA

Bellman-Ford的队列优化版。

时间复杂度O(KE)，其中K为所有顶点进队的平均次数，K一般小于等于2，但是可能被特殊数据卡。


题目描述

德克萨斯纯朴的民眾们这个夏天正在遭受巨大的热浪！！！他们的德克萨斯长角牛吃起来不错，可是他们并不是很擅长生產富含奶油的乳製品。Farmer  John此时以先天下之忧而忧，后天下之乐而乐的精神，身先士卒地承担起向德克萨斯运送大量的营养冰凉的牛奶的重任，以减轻德克萨斯人忍受酷暑的痛苦。 FJ已经研究过可以把牛奶从威斯康星运送到德克萨斯州的路线。这些路线包括起始点和终点先一共经过T  (1  < =  T  < =  2,500)个城镇，方便地标号為1到T。除了起点和终点外地每个城镇由两条双向道路连向至少两个其它地城镇。每条道路有一个通过费用（包括油费，过路费等等）。考虑这个有7个城镇的地图。城镇5是奶源，城镇4是终点（括号内的数字是道路的通过费用）。

输入

*  第一行:  4个由空格隔开的整数:  T,  C,  Ts,  Te *  第2到第C+1行:  第i+1行描述第i条道路。有3个由空格隔开的整数:  Rs,  Re和Ci

输出

*  第一行:  一个单独的整数表示Ts到Te的最短路的长度。（不是费用麼？怎麼突然变直白了 ——译者注）数据保证至少存在一条道路。

```c
#include<iostream>
#include<cstdio>
#include<cstring>
using namespace std;
struct edge{
    int to,next,w;
}e[12401];
int head[2501],dis[2501];
int ne;
int n,m,bg,ed;
int d[20001];
void insert(int a,int b,int c)
{
    e[++ne]=(edge){b,head[a],c};
    head[a]=ne;
}
void spfa()
{
	dis[bg]=0;d[0]=bg;
	int q,t=0,w=1;
	while(t<w)
	{
		q=head[d[t]];
		while(q!=0)
		{
			if(dis[e[q].to]>dis[d[t]]+e[q].w)
			{
			dis[e[q].to]=dis[d[t]]+e[q].w;
			d[w++]=e[q].to;
		    }
		    q=e[q].next;
		}
		t++;
	}
}
int main()
{
	memset(dis,127/3,sizeof(dis));
	scanf("%d%d%d%d",&n,&m,&bg,&ed);
	for(int i=1;i<=m;i++)
	{
		int u,v,w;
		scanf("%d%d%d",&u,&v,&w);
		insert(u,v,w);
		insert(v,u,w);
	}
	spfa();
	printf("%d",dis[ed]);
	return 0;
}
```


HDU 2544(SPFA)

参考代码：

```c
#include<bits/stdc++.h>
using namespace std;
const int INF=0x3f3f3f3f;
int dis[105],pre[105],vis[105],nex[20005];
struct Edge
{
    int u;
    int v;
    int w;
}edge[20005];
void SPFA(int a)
{
    queue<int>q;
    q.push(a);
    while(!q.empty())
    {
        int x=q.front();
        q.pop();
        vis[x]=0;
        for(int i=pre[x];i!=-1;i=nex[i])
        {
            int y=edge[i].v;
            if(dis[y]>dis[x]+edge[i].w)
            {
                dis[y]=dis[x]+edge[i].w;
                if(!vis[y])
                {
                    vis[y]=1;
                    q.push(y);
                }
            }
        }
    }
}
int main()
{
    int n,m,a,b,c;
    while(scanf("%d%d",&n,&m)&&n&&m)
    {
        for(int i=1;i<=n;i++) dis[i]=(i==1?0:INF);
        memset(pre,-1,sizeof(pre));
        for(int i=1;i<=m;i++)
        {
            scanf("%d%d%d",&a,&b,&c);
            edge[i].u=a;
            edge[i].v=b;
            edge[i].w=c;
            nex[i]=pre[a];
            pre[a]=i;
        }
        for(int i=1;i<=m;i++)
        {
            edge[i+m].u=edge[i].v;
            edge[i+m].v=edge[i].u;
            edge[i+m].w=edge[i].w;
            nex[i+m]=pre[edge[i].v];
            pre[edge[i].v]=i+m;
        }
        SPFA(1);
        printf("%d\n",dis[n]);
    }
    return 0;
}
```

### 最小生成树

求包含原图所有点且边最少的连通子图。

#### Kruskal

时间复杂度O(ElogV)，先把边按权重排序，然后从权重小的选起。

每次选一条边（最小的边），如果形成环，就不加入(u,v)中，否则加入。那么加入的(u,v)一定是最佳的。可利用并查集判环。

HDU 1233(Kruskal)

参考代码：

```c
#include<bits/stdc++.h>
using namespace std;
int father[105];
struct Edge
{
    int pre;
    int next;
    int w;
}edge[5005];
int Cmp(Edge x,Edge y)
{
    return x.w<y.w;
}
int Find(int x)
{
    if(x==father[x]) return x;
    return father[x]=Find(father[x]);
}
int Krus(int n,int m)
{
    for(int i=1;i<=n;i++) father[i]=i;
    sort(edge+1,edge+1+m,Cmp);
    int sum=0;
    for(int i=1;i<=m;i++)
    {
        int fp=Find(edge[i].pre),fn=Find(edge[i].next);
        if(fp!=fn)//如果祖先相同，则形成环，不能加入
        {
            father[fp]=fn;
            sum+=edge[i].w;
        }
    }
    return sum;
}
int main()
{
    int n,m;
    while(scanf("%d",&n)&&n)
    {
        m=n*(n-1)/2;
        for(int i=1;i<=m;i++)
            scanf("%d%d%d",&edge[i].pre,&edge[i].next,&edge[i].w);
        printf("%d\n",Krus(n,m));
    }
    return 0;
}
```

POJ 2395(Kruskal)

参考代码：

```c
#include<cstdio>
#include<iostream>
#include<cstring>
#include<algorithm>
using namespace std;
const int INF=0x3f3f3f3f;
const int MAXN=10005;
int u[MAXN],v[MAXN],w[MAXN],aset[MAXN],num[MAXN];
int m,n;
int Cmp(int a,int b)
{
    return w[a]<w[b];
}
int Find(int x)
{
    if(aset[x]==x) return x;
    return aset[x]=Find(aset[x]);
}
int Krus()
{
    int cnt=0,ans=0;
    for(int i=1;i<=n;i++) aset[i]=i;
    for(int i=1;i<=m;i++) num[i]=i;
    sort(num+1,num+1+m,Cmp);
    for(int i=1;i<=m;i++)
    {
        int e=num[i];
        int x=Find(u[e]),y=Find(v[e]);
        if(x!=y)
        {
            ans=max(ans,w[e]);
            aset[x]=y;
            cnt++;
        }
        if(cnt==n-1) return ans;
    }
    if(cnt<n-1) ans=0;
    return ans;
}
int main()
{
    while(scanf("%d%d",&n,&m)!=EOF)
    {
        for(int i=1;i<=m;i++)
            scanf("%d%d%d",&u[i],&v[i],&w[i]);
        printf("%d\n",Krus());
    }
    return 0;
}
```

#### Prim

时间复杂度O(V^2)，与Dijkstra的思想大致相同，主要差异是：Dijkstra找离根最近的点，Prim找离树最近的点；最小生成树的起点不固定。

HDU 1233(Prim)

参考代码：

```c
#include<bits/stdc++.h>
using namespace std;
const int INF=0x3f3f3f3f;
int n;
int mat[105][105],vis[105],dis[105];
int Prim()
{
    for(int i=2;i<=n;i++)
    {
        vis[i]=0;
        dis[i]=mat[i][1];
    }
    vis[1]=1;
    int sum=0;
    for(int i=1;i<=n-1;i++)
    {
        int tem=INF,pos;
        for(int j=1;j<=n;j++)
        {
        if(!vis[j]&&dis[j]<tem)
            tem=dis[pos=j];
        }
        vis[pos]=1;
        sum+=dis[pos];
        for(int j=1;j<=n;j++)
        {
            if(!vis[j]&&dis[j]>mat[pos][j]&&mat[pos][j]!=INF)
                dis[j]=mat[pos][j];
        }
    }
    return sum;
}
int main()
{
    int u,v,w;
    while(scanf("%d",&n)&&n)
    {
        for(int i=1;i<=n;i++) dis[i]=INF;
        for(int i=1;i<=n*(n-1)/2;i++)
        {
            scanf("%d%d%d",&u,&v,&w);
            mat[u][v]=mat[v][u]=w;
        }
        printf("%d\n",Prim());
    }
    return 0;
}
```

POJ 1679(Prim，求次小生成树)

参考代码：

```c
#include<cstdio>
#include<iostream>
#include<cstring>
#include<algorithm>
using namespace std;
const int INF=0x3f3f3f3f;
const int MAXN=105;
bool vis[MAXN],used[MAXN][MAXN];
int low[MAXN],pre[MAXN],ma[MAXN][MAXN],edge[MAXN][MAXN];
int ans;
int Prim(int cost[][MAXN],int n)
{
    int ret=0;
    memset(vis,0,sizeof(vis));
    memset(ma,0,sizeof(ma));
    memset(used,0,sizeof(used));
    vis[1]=1;
    pre[1]=-1;
    for(int i=2;i<=n;i++)
    {
        low[i]=cost[1][i];
        pre[i]=1;
    }
    low[1]=0;
    for(int i=2;i<=n;i++)
    {
        int mi=INF;
        int p=-1;
        for(int j=1;j<=n;j++)
            if(!vis[j]&&mi>low[j])
            {
                mi=low[j];
                p=j;
            }
        if(mi==INF) return -1;
        ret+=mi;
        vis[p]=1;
        used[p][pre[p]]=used[pre[p]][p]=1;
        for(int j=1;j<=n;j++)
        {
            if(vis[j]) ma[j][p]=ma[p][j]=max(ma[j][pre[p]],low[p]);
            if(!vis[j]&&low[j]>cost[p][j])
            {
                low[j]=cost[p][j];
                pre[j]=p;
            }
        }
    }
    return ret;
}
int Same(int cost[][MAXN],int n)
{
    int mi=INF;
    for(int i=1;i<=n;i++)
        for(int j=i+1;j<=n;j++)
            if(cost[i][j]!=INF&&!used[i][j])
                mi=min(mi,ans+cost[i][j]-ma[i][j]);
    if(mi==INF) return -1;
    return mi;
}
int main()
{
    int t,m,n;
    scanf("%d",&t);
    while(t--)
    {
        int u,v,w;
        scanf("%d%d",&n,&m);
        for(int i=1;i<=n;i++)
            for(int j=1;j<=n;j++)
                edge[i][j]=(i==j?0:INF);
        while(m--)
        {
            scanf("%d%d%d",&u,&v,&w);
            edge[u][v]=edge[v][u]=w;
        }
        ans=Prim(edge,n);
        if(ans==-1)
        {
            printf("Not Unique!\n");
            continue;
        }
        if(ans==Same(edge,n))printf("Not Unique!\n");
        else printf("%d\n",ans);
    }
    return 0;
}
```
