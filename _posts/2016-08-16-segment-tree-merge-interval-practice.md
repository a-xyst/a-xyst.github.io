---
title:  "线段树区间合并练习"
excerpt: "线段树区间合并练习。"
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


### POJ 3667

Description

The cows are journeying north to Thunder Bay in Canada to gain cultural enrichment and enjoy a vacation on the sunny shores of Lake Superior. Bessie, ever the competent travel agent, has named the Bullmoose Hotel on famed Cumberland Street as their vacation residence. This immense hotel has N (1 ≤ N ≤ 50,000) rooms all located on the same side of an extremely long hallway (all the better to see the lake, of course).

The cows and other visitors arrive in groups of size Di (1 ≤ Di ≤ N) and approach the front desk to check in. Each group i requests a set of Di contiguous rooms from Canmuu, the moose staffing the counter. He assigns them some set of consecutive room numbers r..r+Di-1 if they are available or, if no contiguous set of rooms is available, politely suggests alternate lodging. Canmuu always chooses the value of r to be the smallest possible.

Visitors also depart the hotel from groups of contiguous rooms. Checkout i has the parameters Xi and Di which specify the vacating of rooms Xi ..Xi +Di-1 (1 ≤ Xi ≤ N-Di+1). Some (or all) of those rooms might be empty before the checkout.

Your job is to assist Canmuu by processing M (1 ≤ M < 50,000) checkin/checkout requests. The hotel is initially unoccupied.

Input

* Line 1: Two space-separated integers: N and M
* Lines 2..M+1: Line i+1 contains request expressed as one of two possible formats: (a) Two space separated integers representing a check-in request: 1 and Di (b) Three space-separated integers representing a check-out: 2, Xi, and Di

Output

* Lines 1.....: For each check-in request, output a single line with a single integer r, the first room in the contiguous sequence of rooms to be occupied. If the request cannot be satisfied, output 0.

Sample Input

10 6
1 3
1 3
1 3
1 3
2 5 5
1 6

Sample Output

1
4
7
0
5

转载一个比较详细的讲解

题意：有一个线段，从1到n，下面m个操作，操作分两个类型，以1开头的是查询操作，以2开头的是更新操作

1 w  表示在总区间内查询一个长度为w的可用区间，并且要最靠左，能找到的话返回这个区间的左端点并占用了这个区间，找不到返回0 

好像n=10 ， 1 3 查到的最左的长度为3的可用区间就是[1,3]，返回1，并且该区间被占用了

2 a len , 表示从单位a开始，清除一段长度为len的区间（将其变为可用，不被占用），不需要输出

因此看sample的话就可以理解了

记录一下自己的感悟：

用线段树，首先要定义好线段树的节点信息，一般看到一个问题，很难很快能确定线段树要记录的信息

做线段树不能为了做题而做，首先线段树是一种辅助结构，它是为问题而生的，因而必须具体问题具体分析

回忆一下RMQ问题，其实解决RMQ有很多方法，根本不需要用到线段树，用线段树解决RMQ，其实是利用线段树的性质来辅助解决这个问题

回忆一下求矩形面积并或周长并的问题，一般使用的是扫描线法，其实扫描线法和线段树一点关系都没有，扫描线法应该归为计算几何的算法，
使用线段树只是为了辅助实现扫描线法

因而回到这题，要解，必须分析问题本质，才去思考怎么用线段树来辅助，另外为什么能用线段树辅助是可行的，这个问题似乎更有价值

1 查询操作，找一段长度为W的没被覆盖的最左的区间

2 更新操作，将某段连续的区域清空

更新操作相对容易解决，关键是怎么实现查询操作

既然是要找一段长度至少为W的区间，要做到这点，其实不难，我们可以在每个线段树的节点里增加一个域tlen，表示该区间可用的区间的最大长度，
至于这个tlen区间的具体位置在哪里不知道，只是知道该区间内存在这么一段可用的区间，并且注意，这个tlen表示的是最大长度，该节点可能有多段可用的区间，但是最长的长度是tlen

记录了这个信息，至少能解决一个问题，就是能不能找到一个合适的区间。如果查询的区间长度W > 总区间的tlen，那么查询一定是失败的（总区间中可以的最大区间都不能满足那就肯定失败）

但这远远不够，其一查询是要返回区间的具体位置的，这里无法返回位置，另外是要查询最左区间，最左的且满足>=W的区间可能不是这个tlen区间

那么我们进一步思考这个问题

首先我们先增加两个域,llen,rlen

llen表示一个区间从最左端开始可用的且连续的最大长度

例如区间[1,5]，覆盖情况为[0,0,0,1,1]，llen = 3，从最左端有3格可以利用

区间[1,5]，覆盖情况为[1,0,0,0,0]，llen = 0，因为从最左端开始找不到1格可用的区间

rlen表示一个区间从最右端开始可用的且连续的最大长度

例如区间[1,5]，覆盖情况为[1,0,1,0,0]，rlen = 2，从最右端有2格可以利用

区间[1,5]，覆盖情况为[0,0,0,0,1]，rlen = 0，因为从最右端开始找不到1格可用的区间

对于一个区间，我们知道它左半区间的tlen，和右半区间的tlen，如果左半区间的tlen >= W ,那么我们一定能在左边找到（满足最左），所以可以深入到左半区间去确定该区间的具体位置

如果左端的不满足，那么我们要先考虑横跨两边的区间（因为要满足最左），因而记录的llen,rlen可以派上用场，一段横跨的区间，
那么是 左边区间rrlen + 右边区间llen ，如果满足的话，就是该区间了，它的位置也是可以确定的

如果横跨的区间不满足，那么就在右半区间找，如果右半区间的tlen >= W , 那么可以在右半区间找到，所以深入到右半区间去确定它的具体位置，否则的话，整个查询就失败了

可见查询是建立在tlen,llen,rlen这个信息之上的，而每次查询后其实伴随着修改，而且还有专门的修改操作，这些修改操作都会改变tlen,llen,rlen的值，所以在更新的时候是时刻维护这些信息

关于这3个信息的维护

当前区间的tlen = max{ 左半区间tlen , 右半区间tlen , 左半区间rlen+右半区间llen} （这个不难理解吧，取左右较大的那个，或者横跨中间的那个）

如果左半区间全部可以用: 当前区间llen = 左半区间llen(tlen) + 右半区间llen 
左半区间部分能用: 当前区间llen = 左半区间llen

如果右半区间全部能用: 当前区间rlen = 右半区间rlen(tlen) + 左半区间rlen
右半区间部分能用: 当前区间rlen = 右半区间rlen

这样就全部维护好了

```c
#include<stdio.h>
#include<stdlib.h>
#include<algorithm>
#include<math.h>
#include<string.h>
//#include<queue>
//#include<vector>
//#include<set>
//#include<map>
using namespace std;
#define rep(i,n) for(int (i)=0;(i)<(int)(n);++(i))
#define rel(i,n) for(int (i)=1;(i)<=(int)(n);++(i))
#define rer(i,l,u) for(int (i)=(int)(l);(i)<=(int)(u);++(i))
#define reu(i,l,u) for(int (i)=(int)(l);(i)<(int)(u);++(i))
#define delf int m=(l+r)>>1
#define lson l,m,rt<<1
#define rson m+1,r,rt<<1|1
#define ls rt<<1
#define rs rt<<1|1
#define LL long long
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fint;
//const int MAXN=1e4+5;const int MOD=1e9+7;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long int;
const int MAXNODES=50005;
int msum[MAXNODES<<2];
int lsum[MAXNODES<<2];
int rsum[MAXNODES<<2];
int col[MAXNODES<<2];
int max(int a,int b){return a>b?a:b;}
void pushdown(int rt,int m)
{
    if(col[rt]==-1) return;
    col[ls]=col[rs]=col[rt];
    msum[ls]=lsum[ls]=rsum[ls]=col[rt]?0:m-(m>>1);
    msum[rs]=lsum[rs]=rsum[rs]=col[rt]?0:(m>>1);
    col[rt]=-1;
}
void pushup(int rt,int m)
{
    lsum[rt]=lsum[ls];
    rsum[rt]=rsum[rs];
    if(lsum[rt]==m-(m>>1)) lsum[rt]+=lsum[rs];
    if(rsum[rt]==(m>>1)) rsum[rt]+=rsum[ls];
    msum[rt]=max(lsum[rs]+rsum[ls],max(msum[ls],msum[rs]));
}
void build(int l,int r,int rt)
{
    msum[rt]=lsum[rt]=rsum[rt]=r-l+1;
    col[rt]=-1;
    if (l==r) return;
    delf;
    build(lson);
    build(rson);
}
void update(int L,int R,int c,int l,int r,int rt)
{
    if(L<=l&&r<=R)
    {
        msum[rt]=lsum[rt]=rsum[rt]=c?0:r-l+1;
        col[rt]=c;
        return;
    }
    pushdown(rt,r-l+1);
    delf;
    if(L<=m) update(L,R,c,lson);
    if(R>m) update(L,R,c,rson);
    pushup(rt,r-l+1);
}
int query(int len,int l,int r,int rt)
{
    if(l==r) return l;
    pushdown(rt,r-l+1);
    delf;
    if(msum[ls]>=len) return query(len,lson);
    else if(lsum[rs]+rsum[ls]>=len) return m-rsum[ls]+1;
    else return query(len,rson);
}
int main()
{
    int n,m,flag,a,b;
    while (scanf("%d%d",&n,&m)!=EOF)
    {
        build(1,n,1);
        while (m--)
        {
            scanf("%d",&flag);
            if (flag==1)
            {
                scanf("%d",&a);
                if(msum[1]<a) printf("0\n");
                else
                {
                    int id=query(a,1,n,1);
                    printf("%d\n",id);
                    update(id,id+a-1,1,1,n,1);
                }
            }
            else
            {
                scanf("%d%d",&a,&b);
                update(a,a+b-1,0,1,n,1);
            }
        }
    }
    return 0;
}


```

### HDU 3911

Problem Description

There are a bunch of stones on the beach; Stone color is white or black. Little Sheep has a magic brush, she can change the color of a continuous stone, black to white, white to black. Little Sheep like black very much, so she want to know the longest period of consecutive black stones in a range [i, j].
 

Input

  There are multiple cases, the first line of each case is an integer n(1<= n <= 10^5), followed by n integer 1 or 0(1 indicates black stone and 0 indicates white stone), then is an integer M(1<=M<=10^5) followed by M operations formatted as x i j(x = 0 or 1) , x=1 means change the color of stones in range[i,j], and x=0 means ask the longest period of consecutive black stones in range[i,j]
 

Output

When x=0 output a number means the longest length of black stones in range [i,j].
 

Sample Input

4
1 0 1 0
5
0 1 4
1 2 3
0 1 4
1 3 3
0 4 4
 

Sample Output

1
2
0


题目大意：给定一个序列，然后有M次操作；

0 l r：表示询问l，r中最大连续1的个数

1 l r：表示将l，r区间上的数取反

因为有一个取反的操作，所以对于每个节点要维护6个值，包括连续0,1最长序列的长度，左边和右边的最长连续长度

```c
#include<stdio.h>
#include<stdlib.h>
#include<algorithm>
#include<math.h>
#include<string.h>
//#include<queue>
//#include<vector>
//#include<set>
//#include<map>
using namespace std;
#define rep(i,n) for(int (i)=0;(i)<(int)(n);++(i))
#define rel(i,n) for(int (i)=1;(i)<=(int)(n);++(i))
#define rer(i,l,u) for(int (i)=(int)(l);(i)<=(int)(u);++(i))
#define reu(i,l,u) for(int (i)=(int)(l);(i)<(int)(u);++(i))
#define delf int m=(l+r)>>1
#define lson l,m,rt<<1
#define rson m+1,r,rt<<1|1
#define ls rt<<1
#define rs rt<<1|1
#define LL long long
using namespace std;
const int MAXNODES=111111;
int n,Q,ax;
int preb[MAXNODES<<2],sufb[MAXNODES<<2],mxb[MAXNODES<<2];
int prew[MAXNODES<<2],sufw[MAXNODES<<2],mxw[MAXNODES<<2],lazy[MAXNODES<<2];
int num[MAXNODES];
inline int max(int a,int b) {return a>b?a:b;}
inline int min(int a,int b) {return a<b?a:b;}
void pushup(int rt,int m)
{
    preb[rt]=preb[ls];
    prew[rt]=prew[ls];
    if(preb[ls]==m-(m>>1))preb[rt]+=preb[rs];
    if(prew[ls]==m-(m>>1))prew[rt]+=prew[rs];

    sufb[rt]=sufb[rs];
    sufw[rt]=sufw[rs];
    if(sufb[rs]==(m>>1))sufb[rt]+=sufb[ls];
    if(sufw[rs]==(m>>1))sufw[rt]+=sufw[ls];

    mxb[rt]=max(mxb[ls],mxb[rs]);
    mxb[rt]=max(mxb[rt],sufb[ls]+preb[rs]);
    mxw[rt]=max(mxw[ls],mxw[rs]);
    mxw[rt]=max(mxw[rt],sufw[ls]+prew[rs]);
}
inline void exchange(int rt)
{
    swap(preb[rt],prew[rt]);
    swap(sufb[rt],sufw[rt]);
    swap(mxb[rt],mxw[rt]);
}
void pushdown(int rt)
{
    if(lazy[rt])
    {
        lazy[ls]^=1,lazy[rs]^=1,lazy[rt]=0;
        exchange(ls);
        exchange(rs);
    }
}
void build(int l,int r,int rt)
{
    lazy[rt]=0;
    if(l==r)
    {
        scanf("%d",&ax);
        if(ax==1)
        {
            preb[rt]=sufb[rt]=mxb[rt]=1;
            prew[rt]=sufw[rt]=mxw[rt]=0;
        }
        else
        {
            preb[rt]=sufb[rt]=mxb[rt]=0;
            prew[rt]=sufw[rt]=mxw[rt]=1;
        }
        return;
    }
    delf;
    build(lson);build(rson);
    pushup(rt,r-l+1);
}
void update(int& L,int& R,int l,int r,int rt)
{
    if(L<=l&&r<=R)
    {
        lazy[rt]^=1;
        exchange(rt);
        return;
    }
    pushdown(rt);
    delf;
    if(L<=m) update(L,R,lson);
    if(R>m) update(L,R,rson);
    pushup(rt,r-l+1);
}
int query(int& L,int& R,int l,int r,int rt)
{
    if(L<=l&&r<=R) return mxb[rt];
    pushdown(rt);
    delf;
    if(L>m) return query(L,R,rson);
    if(R<=m) return query(L,R,lson);
    int t1=query(L,R,lson);
    int t2=query(L,R,rson);
    int wa1=min(m-L+1,sufb[ls]);
    int wa2=min(R-m,preb[rs]);
    return max(max(t1,t2),wa1+wa2);
}
int main()
{
    int op,a,b;
    while(scanf("%d",&n)!=EOF)
    {
        build(1,n,1);
        scanf("%d",&Q);
        for(int i=1;i<=Q;i++)
        {
            scanf("%d%d%d",&op,&a,&b);
            if(op==1) update(a,b,1,n,1);
            else
            {
                int ans=query(a,b,1,n,1);
                printf("%d\n",ans);
            }
        }
    }
    return 0;
}


```

### HDU 3308

Problem Description

Given n integers.
You have two operations:
U A B: replace the Ath number by B. (index counting from 0)
Q A B: output the length of the longest consecutive increasing subsequence (LCIS) in [a, b].
 

Input

T in the first line, indicating the case number.
Each case starts with two integers n , m(0<n,m<=105).
The next line has n integers(0<=val<=105).
The next m lines each has an operation:
U A B(0<=A,n , 0<=B=105)
OR
Q A B(0<=A<=B< n).
 

Output

For each Q, output the answer.
 

Sample Input

1
10 10
7 7 3 3 5 9 9 8 1 8 
Q 6 6
U 3 4
Q 0 1
Q 0 5
Q 4 7
Q 3 5
Q 0 2
Q 4 6
U 6 10
Q 0 9
 

Sample Output

1
1
4
2
3
1
2
5

题目大意：给一个整数序列，m次询问，每次询问某个区间中最长连续上升子序列的长度。

维护以区间左端开头的、以区间右端点结尾的和区间最长的上升连续序列。

```c
#include<stdio.h>
#include<stdlib.h>
#include<algorithm>
#include<math.h>
#include<string.h>
//#include<queue>
//#include<vector>
//#include<set>
//#include<map>
using namespace std;
#define rep(i,n) for(int (i)=0;(i)<(int)(n);++(i))
#define rel(i,n) for(int (i)=1;(i)<=(int)(n);++(i))
#define rer(i,l,u) for(int (i)=(int)(l);(i)<=(int)(u);++(i))
#define reu(i,l,u) for(int (i)=(int)(l);(i)<(int)(u);++(i))
#define delf int m=(l+r)>>1
#define lson l,m,rt<<1 
#define rson m+1,r,rt<<1|1 
#define ls rt<<1 
#define rs rt<<1|1 
#define LL long long
using namespace std;
const int MAXNODES=100005;
int n,m,k,x,y;
int no[MAXNODES<<2],ls[MAXNODES<<2],rs[MAXNODES<<2];
int a[MAXNODES];
inline void pushup(int rt,int l,int r)
{
    delf;
    ls[rt]=ls[ls];rs[rt]=rs[rs];
    no[rt]=max(no[ls],no[rs]);
    if(a[mid]<a[mid+1])
    {
        if(ls[rt]==mid-l+1)ls[rt]+=ls[rs];
        if(rs[rt]==r-mid)rs[rt]+=rs[ls];
        no[rt]=max(no[rt],ls[rs]+rs[ls]);
    }
}
void build(int rt,int l,int r)
{
    if(l==r)
    {
        no[rt]=ls[rt]=rs[rt]=1;
        return;
    }
    delf;
    build(lson);build(rson);
    pushup(rt,l,r);
}
void add(int rt,int l,int r)
{
    if(l==r){return;}
    delf;
    if(x<=mid)add(lson);
    else add(rson);
    pushup(rt,l,r);
}
int query(int rt,int l,int r)
{
    if(x<=l&&r<=y)return no[rt];
    delf;
    if(y<=mid)return query(lson);
    if(x>mid)return query(rson);
    int t1=query(lson);
    int t2=query(rson);
    int ans=max(t1,t2);
    if(a[mid]<a[mid+1])
        ans=max(ans,(min(ls[rs],y-mid)+min(rs[ls],mid+1-x)));
    return ans;
}
int main(){
    int i,j,group;
    scanf("%d",&group);
    while(group--)
    {
        char str[5];
        scanf("%d%d",&n,&m);
        for(i=1;i<=n;++i)scanf("%d",&a[i]);
        build(1,1,n);
        while(m--)
        {
            scanf("%s%d%d",str,&x,&y);
            ++x;
            if(str[0]=='U'){a[x]=y;add(1,1,n);}
            else{++y;printf("%d\n",query(1,1,n));}
        }
    }
    return 0;
}
```

### HDU 3333(线段树离线)

Problem Description

After inventing Turing Tree, 3xian always felt boring when solving problems about intervals, because Turing Tree could easily have the solution. As well, wily 3xian made lots of new problems about intervals. So, today, this sick thing happens again...

Now given a sequence of N numbers A1, A2, ..., AN and a number of Queries(i, j) (1≤i≤j≤N). For each Query(i, j), you are to caculate the sum of distinct values in the subsequence Ai, Ai+1, ..., Aj.
 

Input

The first line is an integer T (1 ≤ T ≤ 10), indecating the number of testcases below.
For each case, the input format will be like this:
* Line 1: N (1 ≤ N ≤ 30,000).
* Line 2: N integers A1, A2, ..., AN (0 ≤ Ai ≤ 1,000,000,000).
* Line 3: Q (1 ≤ Q ≤ 100,000), the number of Queries.
* Next Q lines: each line contains 2 integers i, j representing a Query (1 ≤ i ≤ j ≤ N).
 

Output

For each Query, print the sum of distinct values of the specified subsequence in one line.
 

Sample Input

2
3
1 1 4
2
1 2
2 3
5
1 1 2 1 3
3
1 5
2 4
3 5
 

Sample Output

1
5
6
3
6


题意：给定一个区间，查询这个区间上不同的数的和

首先把所有查询区间记录下来，然后按照区间的右值排序，接着从左到右把每一个数更新到线段树中，并记录它出现的位置。如果一个数已经出现过，那么就把他上次出现的位置的值置为0，并更新它出现的位置。因为查询区间是按右值排序的，因此把过去重复出现的数字置为0不会影响结果。当更新到某个区间的右值时，就查询一次该区间的答案，并把答案记下来。最后把区间查询的答案按照输入顺序输出即可。

```c
#include<stdio.h>
#include<stdlib.h>
#include<algorithm>
#include<math.h>
#include<string.h>
//#include<queue>
//#include<vector>
//#include<set>
//#include<map>
using namespace std;
#define rep(i,n) for(int (i)=0;(i)<(int)(n);++(i))
#define rel(i,n) for(int (i)=1;(i)<=(int)(n);++(i))
#define rer(i,l,u) for(int (i)=(int)(l);(i)<=(int)(u);++(i))
#define reu(i,l,u) for(int (i)=(int)(l);(i)<(int)(u);++(i))
#define delf int m=(l+r)>>1
#define lson l,m,rt<<1 
#define rson m+1,r,rt<<1|1 
#define ls rt<<1 
#define rs rt<<1|1 
#define LL long long
using namespace std;
const int N=30005;  
const int Q=100005;   
int n,tt,qq;  
map<LL,int> hashs;  
LL sum[N*4],a[N],ans[Q];  
struct QN{  
    int l,r,index;  
}q[Q];  
int cmp(QN a,QN b){return a.r<b.r;  }  
void build(int l,int r,int rt)
{  
    sum[rt]=0;  
    if(l==r) return;  
    delf;  
    build(lson);  
    build(rson);  
}  
void pushup(int rt)
{
    sum[rt]=sum[ls]+sum[rs];  
}
void update(int a,LL c,int l,int r,int rt)
{
    if(l==r)
    {  
        sum[rt]=c;
        return;  
    }  
    delf;  
    if(a<=m) update(a,c,lson);  
    else update(a,c,rson);  
    pushup(rt);  
}  
LL query(int a,int b,int l,int r,int rt)
{  
    if(a<=l&&b>=r) return sum[rt];  
    delf;  
    LL ret=0;
    if(a<=m) ret+=query(a,b,lson);  
    if(b>m) ret+=query(a,b,rson);  
    return ret;  
}  
void solve()
{  
    int pos=1;  
    for(int i=0;i<qq;i++)
    {  
        while(q[i].r>=pos)
        {
            if(hashs[a[pos]]) update(hashs[a[pos]],0,1,n,1);  
            hashs[a[pos]]=pos;  
            update(pos,a[pos++],1,n,1);
        }  
        ans[q[i].index]=query(q[i].l,q[i].r,1,n,1);  
    }
}
int main()
{  
    for(scanf("%d",&tt);tt>0;tt--)
    {  
        hashs.clear();  
        scanf("%d",&n);  
        build(1,n,1);  
        for(int i=1;i<=n;i++) scanf("%d",&a[i]);  
        scanf("%d",&qq);  
        for(int i=0;i<qq;i++)
        { 
            scanf("%d %d",&q[i].l,&q[i].r);  
            q[i].index=i;  
        }  
        sort(q,q+qq,cmp);  
        solve();  
        for(int i=0;i<qq;i++) printf("%I64d\n",ans[i]);  
    }  
    return 0;  
}  
```
