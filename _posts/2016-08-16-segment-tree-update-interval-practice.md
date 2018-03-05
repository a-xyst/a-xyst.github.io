---
title:  "线段树区间更新练习"
excerpt: "线段树区间更新练习。"
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

### POJ 3468

Description

You have N integers, A1, A2, ... , AN. You need to deal with two kinds of operations. One type of operation is to add some given number to each number in a given interval. The other is to ask for the sum of numbers in a given interval.

Input

The first line contains two numbers N and Q. 1 ≤ N,Q ≤ 100000.
The second line contains N numbers, the initial values of A1, A2, ... , AN. -1000000000 ≤ Ai ≤ 1000000000.
Each of the next Q lines represents an operation.
"C a b c" means adding c to each of Aa, Aa+1, ... , Ab. -10000 ≤ c ≤ 10000.
"Q a b" means querying the sum of Aa, Aa+1, ... , Ab.

Output

You need to answer all Q commands in order. One answer in a line.

Sample Input

10 5
1 2 3 4 5 6 7 8 9 10
Q 4 4
Q 1 10
Q 2 4
C 3 6 3
Q 2 4

Sample Output

4
55
9
15

区间更新模板题，注意用long long

```c
#include<stdio.h>
#include<stdlib.h>
#include<algorithm>
#include<math.h>
#include<string.h>
//#include<queue>
//#include<vector>
//#include<set>
using namespace std;
#define rep(i,n) for(int (i)=0;(i)<(int)(n);++(i))
#define rel(i,n) for(int (i)=1;(i)<=(int)(n);++(i))
#define rer(i,l,u) for(int (i)=(int)(l);(i)<=(int)(u);++(i))
#define reu(i,l,u) for(int (i)=(int)(l);(i)<(int)(u);++(i))
#define lson l,m,rt<<1
#define rson m+1,r,rt<<1|1
#define delf int m=(l+r)>>1
#define LL long long
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fLL;
//const int MAXN=1e4+5;const int MOD=1e9+7;
const int MAXNODES=100005;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long LL;
LL lazy[MAXNODES<<2];
LL sum[MAXNODES<<2];
void pushup(int rt)
{
    sum[rt]=(sum[rt<<1]+sum[(rt<<1)|1]);
}
void pushdown(LL rt,LL m)
{
    if(lazy[rt])
    {
        lazy[rt<<1]+=lazy[rt];
        lazy[rt<<1|1]+=lazy[rt];
        sum[rt<<1]+=(m-(m>>1))*lazy[rt];
        sum[rt<<1|1]+=(m>>1)*lazy[rt];
        lazy[rt]=0;
    }
}
void build(LL l,LL r,LL rt)
{
    lazy[rt]=0;
    if(l==r)
    {
        scanf("%I64d",&sum[rt]);
        return;
    }
    delf;
    build(lson);
    build(rson);
    pushup(rt);
}
void update(LL L,LL R,LL c,LL l,LL r,LL rt)
{
    if(L<=l&&r<=R)
    {
        lazy[rt]+=c;
        sum[rt]+=c*(r-l+1);
        return;
    }
    pushdown(rt,r-l+1);
    delf;
    if(L<=m) update(L,R,c,lson);
    if(R>m) update(L,R,c,rson);
    pushup(rt);
}
LL query(LL L,LL R,LL l,LL r,LL rt)
{
    if(L<=l&&r<=R) return sum[rt];
    pushdown(rt,r-l+1);
    delf;
    LL ret=0;
    if(L<=m) ret+=query(L,R,lson);
    if(R>m) ret+=query(L,R,rson);
    return ret;
}
int main()
{
    //freopen("test.txt","r",stdin);
    int n,q;
    char tmp;
    LL a,b,c;
    memset(lazy,0,sizeof(lazy));
    memset(sum,0,sizeof(sum));
    scanf("%d%d",&n,&q);
    build(1,n,1);
    while(q--)
    {
        scanf("%*c%c",&tmp);
        if(tmp=='Q')
        {
            scanf("%I64d%I64d",&a,&b);
            printf("%I64d\n",query(a,b,1,n,1));
        }
        else
        {
            scanf("%I64d%I64d%I64d",&a,&b,&c);
            update(a,b,c,1,n,1);
        }
    }
    return 0;
}
```

### HDU 1698

Problem Description

In the game of DotA, Pudge’s meat hook is actually the most horrible thing for most of the heroes. The hook is made up of several consecutive metallic sticks which are of the same length.




Now Pudge wants to do some operations on the hook.

Let us number the consecutive metallic sticks of the hook from 1 to N. For each operation, Pudge can change the consecutive metallic sticks, numbered from X to Y, into cupreous sticks, silver sticks or golden sticks.
The total value of the hook is calculated as the sum of values of N metallic sticks. More precisely, the value for each kind of stick is calculated as follows:

For each cupreous stick, the value is 1.
For each silver stick, the value is 2.
For each golden stick, the value is 3.

Pudge wants to know the total value of the hook after performing the operations.
You may consider the original hook is made up of cupreous sticks.
 

Input

The input consists of several test cases. The first line of the input is the number of the cases. There are no more than 10 cases.
For each case, the first line contains an integer N, 1<=N<=100,000, which is the number of the sticks of Pudge’s meat hook and the second line contains an integer Q, 0<=Q<=100,000, which is the number of the operations.
Next Q lines, each line contains three integers X, Y, 1<=X<=Y<=N, Z, 1<=Z<=3, which defines an operation: change the sticks numbered from X to Y into the metal kind Z, where Z=1 represents the cupreous kind, Z=2 represents the silver kind and Z=3 represents the golden kind.
 

Output

For each case, print a number in a line representing the total value of the hook after the operations. Use the format in the example.
 

Sample Input

1
10
2
1 5 2
5 9 3
 

Sample Output

Case 1: The total value of the hook is 24.

同样是模板题，比上面那题还简单些

```c
#include<stdio.h>
#include<stdlib.h>
#include<algorithm>
#include<math.h>
#include<string.h>
//#include<queue>
//#include<vector>
//#include<set>
using namespace std;
#define rep(i,n) for(int (i)=0;(i)<(int)(n);++(i))
#define rel(i,n) for(int (i)=1;(i)<=(int)(n);++(i))
#define rer(i,l,u) for(int (i)=(int)(l);(i)<=(int)(u);++(i))
#define reu(i,l,u) for(int (i)=(int)(l);(i)<(int)(u);++(i))
#define lson l,m,rt<<1
#define rson m+1,r,rt<<1|1
#define delf int m=(l+r)>>1
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fint;
//const int MAXN=1e4+5;const int MOD=1e9+7;
const int MAXNODES=100005;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long int;
int lazy[MAXNODES<<2];
int sum[MAXNODES<<2];
void pushup(int rt)
{
    sum[rt]=(sum[rt<<1]+sum[(rt<<1)|1]);
}
void pushdown(int rt,int m)
{
    if(lazy[rt])
    {
        lazy[rt<<1]=lazy[rt];
        lazy[rt<<1|1]=lazy[rt];
        sum[rt<<1]=(m-(m>>1))*lazy[rt];
        sum[rt<<1|1]=(m>>1)*lazy[rt];
        lazy[rt]=0;
    }
}
void build(int l,int r,int rt)
{
    lazy[rt]=0;
    sum[rt]=1;
    if(l==r) return;
    delf;
    build(lson);
    build(rson);
    pushup(rt);
}
void update(int L,int R,int c,int l,int r,int rt)
{
    if(L<=l&&r<=R)
    {
        lazy[rt]=c;
        sum[rt]=c*(r-l+1);
        return;
    }
    pushdown(rt,r-l+1);
    delf;
    if(L<=m) update(L,R,c,lson);
    if(R>m) update(L,R,c,rson);
    pushup(rt);
}
int main()
{
    //freopen("test.txt","r",stdin);
    int k,n,t,a,b,c;
    scanf("%d",&k);
    rel(i,k)
    {
        scanf("%d",&n);
        build(1,n,1);
        scanf("%d",&t);
        while(t--)
        {
            scanf("%d%d%d",&a,&b,&c);
            update(a,b,c,1,n,1);
        }
        printf("Case %d: The total value of the hook is %d.\n",i,sum[1]);
    }
    return 0;
}
```

### POJ 2528

Description

The citizens of Bytetown, AB, could not stand that the candidates in the mayoral election campaign have been placing their electoral posters at all places at their whim. The city council has finally decided to build an electoral wall for placing the posters and introduce the following rules: 
Every candidate can place exactly one poster on the wall. 
All posters are of the same height equal to the height of the wall; the width of a poster can be any integer number of bytes (byte is the unit of length in Bytetown). 
The wall is divided into segments and the width of each segment is one byte. 
Each poster must completely cover a contiguous number of wall segments.

They have built a wall 10000000 bytes long (such that there is enough place for all candidates). When the electoral campaign was restarted, the candidates were placing their posters on the wall and their posters differed widely in width. Moreover, the candidates started placing their posters on wall segments already occupied by other posters. Everyone in Bytetown was curious whose posters will be visible (entirely or in part) on the last day before elections. 
Your task is to find the number of visible posters when all the posters are placed given the information about posters' size, their place and order of placement on the electoral wall. 
Input

The first line of input contains a number c giving the number of cases that follow. The first line of data for a single case contains number 1 <= n <= 10000. The subsequent n lines describe the posters in the order in which they were placed. The i-th line among the n lines contains two integer numbers li and ri which are the number of the wall segment occupied by the left end and the right end of the i-th poster, respectively. We know that for each 1 <= i <= n, 1 <= li <= ri <= 10000000. After the i-th poster is placed, it entirely covers all wall segments numbered li, li+1 ,... , ri.
Output

For each input data set print the number of visible posters after all the posters are placed. 

The picture below illustrates the case of the sample input. 

Sample Input

1
5
1 4
2 6
8 10
3 4
7 10

Sample Output

4

题意:在墙上贴海报,海报可以互相覆盖,问最后可以看见几张海报

思路:这题数据范围很大,直接搞超时+超内存,需要离散化

```c
#include<stdio.h>
#include<stdlib.h>
#include<algorithm>
#include<math.h>
#include<string.h>
//#include<queue>
//#include<vector>
//#include<set>
using namespace std;
#define rep(i,n) for(int (i)=0;(i)<(int)(n);++(i))
#define rel(i,n) for(int (i)=1;(i)<=(int)(n);++(i))
#define rer(i,l,u) for(int (i)=(int)(l);(i)<=(int)(u);++(i))
#define reu(i,l,u) for(int (i)=(int)(l);(i)<(int)(u);++(i))
#define lson l,m,rt<<1
#define rson m+1,r,rt<<1|1
#define delf int m=(l+r)>>1
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fint;
//const int MAXN=1e4+5;const int MOD=1e9+7;
const int MAXNODES=10005;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long int;
int lazy[MAXNODES<<4];
int poi[MAXNODES<<2];
int li[MAXNODES],ri[MAXNODES];
bool book[MAXNODES];
void build(int l,int r,int rt)
{
    lazy[rt]=-1;
    if(l==r) return;
    delf;
    build(lson);
    build(rson);
}
int bin(int g,int m)
{
    int l=0,r=m-1;
    while(r>=l)
    {
        int mid=(l+r)>>1;
        if(poi[mid]==g) return mid;
        else if(poi[mid]>g) r=mid-1;
        else l=mid+1;
    }
}
void pushdown(int rt)
{
    if(lazy[rt]!=-1)
    {
        lazy[rt<<1]=lazy[rt];
        lazy[rt<<1|1]=lazy[rt];
        lazy[rt]=-1;
    }
}

void update(int L,int R,int c,int l,int r,int rt)
{
    if(L<=l&&r<=R)
    {
        lazy[rt]=c;
        return;
    }
    pushdown(rt);
    delf;
    if(L<=m) update(L,R,c,lson);
    if(R>m) update(L,R,c,rson);
}
int query(int l,int r,int rt)
{
    int ret=0;
    if(lazy[rt]!=-1)
    {
        if(!book[lazy[rt]])
        {
            book[lazy[rt]]=1;
            ret++;
        }
        return ret;
    }
    if(l==r) return 0;
    delf;
    ret+=query(lson);
    ret+=query(rson);
    return ret;
}
int main()
{
    //freopen("test.txt","r",stdin);
    int k,n,t,a,b,c;
    scanf("%d",&k);
    while(k--)
    {
        scanf("%d",&n);
        int cnt=0;
        rel(i,n)
        {
            scanf("%d%d",&li[i],&ri[i]);
            poi[cnt++]=li[i];
            poi[cnt++]=ri[i];
        }
        sort(poi,poi+cnt);
        int top=1;
        rel(i,cnt)
            if(poi[i]!=poi[i-1])
                poi[top++]=poi[i];
        int tmp=top;
        rel(i,tmp)
            if(poi[i]-poi[i-1]>1)
                poi[top++]=poi[i-1]+1;
        sort(poi,poi+top);
        build(0,top-1,1);
        int c=0;
        rel(i,n)
        {
            int l=bin(li[i],top);
            int r=bin(ri[i],top);
            update(l,r,++c,0,top-1,1);
        }
        memset(book,0,sizeof(book));
        printf("%d\n",query(0,top-1,1));
    }
    return 0;
}
```

### HDU 4893

题意：

给你长度为n的序列。序列初始值都为0.m组询问。支持三种操作。
1 k d 给第k个数加上d。
2 l r 询问[l.r]区间和。
3 l r 把所有[l,r]的值a[i]变成与a[i]值最相近的费布拉奇数。
f[0]=1,f[1]=1.f[i]=f[i-1]+f[i-2].
1 ≤ n ≤ 100000, 1 ≤ m ≤ 100000, |d| < 231


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
#define LL long long
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fint;
//const int MAXN=1e4+5;const int MOD=1e9+7;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long int;
const int MAXNODES=100005;
int lazy[MAXNODES<<2];
LL sum[MAXNODES<<2];
LL fi[200];
int cnt,n,m;
void init()
{
    fi[0]=fi[1]=1;
    for (int i=2;i<88;i++)
        fi[i]=fi[i-1]+fi[i-2];
    cnt=87;
}

LL getnum(LL s)
{
    int l=1;
    int h=cnt;
    while (l<=h)
    {
        int m=(l+h)>>1;
        if (fi[m]>=s) h=m-1;
        else l=m+1;
    }
    LL t1=s-fi[l-1];
    LL t2=fi[l]-s;
    if (t1<=t2) return fi[l-1];
    else return fi[l];
}

void pushup(int rt)
{
    sum[rt]=sum[rt<<1]+sum[rt<<1|1];
    lazy[rt]=0;
    if (lazy[rt<<1]==1&&lazy[rt<<1|1]==1)
        lazy[rt]=1;
}


void build(int l,int r,int rt)
{
    sum[rt]=lazy[rt]=0;
    if (l==r) return;
    delf;
    build(lson);
    build(rson);
}
void update(int k,LL v,int l,int r,int rt)
{
    if(l==r)
    {
        sum[rt]+=v;
        lazy[rt]=0;
        if(sum[rt]==getnum(sum[rt])) lazy[rt]=1;
        return;
    }
    int m=(l+r)>>1;
    if(m>=k) update(k,v,lson);
    else update(k,v,rson);
    pushup(rt);
}
void update1(int L,int R,int l,int r,int rt)
{
    if(lazy[rt]) return;
    if(l==r)
    {
        sum[rt]=getnum(sum[rt]);
        lazy[rt]=1;
        return;
    }
    int m=(l+r)>>1;
    if(L<=m) update1(L,R,lson);
    if(R>m) update1(L,R,rson);
    pushup(rt);
    
}
LL query(int L,int R,int l,int r,int rt)
{
    if(L<=l&&r<=R) return sum[rt];
    int m=(l+r)>>1;
    LL ret=0;
    if(L<=m) ret+=query(L,R,lson);
    if(R>m) ret+=query(L,R,rson);
    return ret;
}
int main()
{
    init();
    while (scanf("%d%d",&n,&m)!=EOF)
    {
        build(1,n,1);
        while (m--)
        {
            int p,l,r;
            LL k;
            scanf("%d",&p);
            if (p==1)
            {
                scanf("%d%I64d",&l,&k);
                update(l,k,1,n,1);
            }
            else if (p==2)
            {
                scanf("%d%d",&l,&r);
                printf("%I64d\n",query(l,r,1,n,1));
            }
            else
            {
                scanf("%d%d",&l,&r);
                update1(l,r,1,n,1);
            }
        }
    }
}
```
