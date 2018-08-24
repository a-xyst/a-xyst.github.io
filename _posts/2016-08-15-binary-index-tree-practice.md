---
title:  "树状数组练习"
excerpt: "树状数组练习。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Competetive_Programming
tags:
  - 数据结构
---


[这个博客](http://www.cppblog.com/menjitianya/category/20893.html)有关于树状数组和线段树的讲解，我感觉写得不错，虽然有些小错误，比如树状数组的求和函数写错

摘出的通用模板：

```c
///树状数组

//定义:a是原数组，c是树状数组
//对于c的两个数组下标x,y,如果x + 2^k = y (k等于x的二进制表示中末尾0的个数)，那么定义(y, x)为一组树上的父子关系
//c[i]=sum{a[j]|i-2^k+1<=j<=i}
//MAXNODES一般为数据上线左移2位
//二位树状数组写法：给add和sum各增加一个y参数，并且新增一层for循环

int c[MAXNODES];
int lowbit(int x)
{
    return x&(-x);
}
void add(int x,int v)
{
    for(int i=x;i>0&&i<=MAXNODES;i+=lowbit(i))
        c[i]+=v;
}
int sum(int x)
{
    int s=0;
    for(int i=x;i>0&&i<=MAXNODES;i-=lowbit(i))
        s += c[i];
    return s;
}
```

### HDU 1541 

树状数组裸题，因为是以y轴递增顺序输入的，所以不用特意排序

Problem Description

Astronomers often examine star maps where stars are represented by points on a plane and each star has Cartesian coordinates. Let the level of a star be an amount of the stars that are not higher and not to the right of the given star. Astronomers want to know the distribution of the levels of the stars. 



For example, look at the map shown on the figure above. Level of the star number 5 is equal to 3 (it's formed by three stars with a numbers 1, 2 and 4). And the levels of the stars numbered by 2 and 4 are 1. At this map there are only one star of the level 0, two stars of the level 1, one star of the level 2, and one star of the level 3. 

You are to write a program that will count the amounts of the stars of each level on a given map.


Input

The first line of the input file contains a number of stars N (1<=N<=15000). The following N lines describe coordinates of stars (two integers X and Y per line separated by a space, 0<=X,Y<=32000). There can be only one star at one point of the plane. Stars are listed in ascending order of Y coordinate. Stars with equal Y coordinates are listed in ascending order of X coordinate.


Output

The output should contain N lines, one number per line. The first line contains amount of stars of the level 0, the second does amount of stars of the level 1 and so on, the last line contains amount of stars of the level N-1.


Sample Input

5
1 1
5 1
7 1
3 3
5 5


Sample Output

1
2
1
1
0


```c
/*
此题有可能出现坐标为0的点，而树状数组从1开始，所以做题的时候要注意
*/
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
#define lson l,mid,now<<1
#define rson mid+1,r,now<<1|1
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fLL;
//const int MAXN=1e4+5;const int MOD=1e9+7;
const int MAXNODES=400001;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long LL;
int c[MAXNODES];
int ans[MAXNODES];
int lowbit(int x)
{
    return x&(-x);
}
void add(int x,int v)
{
    for(int i=x;i>0&&i<=MAXNODES;i+=lowbit(i))
        c[i]+=v;
}
int sum(int x)
{
    int s=0;
    for(int i=x;i>0&&i<=MAXNODES;i-=lowbit(i))
        s += c[i];
    return s;
}
int main()
{
    int x,y,n;
    while(scanf("%d",&n)!=EOF)
    {
        memset(ans,0,sizeof(ans));
        memset(c,0,sizeof(c));
        rep(i,n)
        {
            scanf("%d%d",&x,&y);
            ans[sum(x+1)]++;
            add(x+1,1);
        }
        rep(i,n) printf("%d\n",ans[i]);
    }
    return 0;
}
```

### POJ 2299

Description

In this problem, you have to analyze a particular sorting algorithm. The algorithm processes a sequence of n distinct integers by swapping two adjacent sequence elements until the sequence is sorted in ascending order. For the input sequence 
9 1 0 5 4 ,

Ultra-QuickSort produces the output 
0 1 4 5 9 .

Your task is to determine how many swap operations Ultra-QuickSort needs to perform in order to sort a given input sequence.

Input

The input contains several test cases. Every test case begins with a line that contains a single integer n < 500,000 -- the length of the input sequence. Each of the the following n lines contains a single integer 0 ≤ a[i] ≤ 999,999,999, the i-th input sequence element. Input is terminated by a sequence of length n = 0. This sequence must not be processed.

Output

For every input sequence, your program prints a single line containing an integer number op, the minimum number of swap operations necessary to sort the given input sequence.

Sample Input

5
9
1
0
5
4
3
1
2
3
0

Sample Output

6
0

树状数组离散化求逆序对，模板题

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
#define lson l,mid,now<<1
#define rson mid+1,r,now<<1|1
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fLL;
//const int MAXN=1e4+5;const int MOD=1e9+7;
const int MAXNODES=4000001;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long LL;
int c[MAXNODES];
int aa[MAXNODES];
struct Node{
   int v;
   int order;
}in[MAXNODES];
int n;
int lowbit(int x)
{
    return x&(-x);
}

void add(int t,int value)
{
    for(int i=t;i<=n;i+=lowbit(i))
    {
        c[i]+=value;
    }
}
int sum(int x)
{
    int temp=0;
    for(int i=x;i>=1;i-=lowbit(i))
    {
        temp+=c[i];
    }
    return temp;
}
bool cmp(Node a ,Node b)
{
    return a.v<b.v;
}
int main()
{
    while(scanf("%d",&n)!=EOF&&n)
    {
        //离散化
        for(int i=1;i<=n;i++)
        {
            scanf("%d",&in[i].v);
            in[i].order=i;
        }
        sort(in+1,in+n+1,cmp);
        for(int i=1;i<=n;i++) aa[in[i].order]=i;
        //树状数组求逆序
        memset(c,0,sizeof(c));
        long long ans=0;
        for(int i=1;i<=n;i++)
        {
            add(aa[i],1);
            ans+=i-sum(aa[i]);
        }
        printf("%I64d\n",ans);
    }
    return 0;
}
```

### POJ 1990

Description

Every year, Farmer John's N (1 <= N <= 20,000) cows attend "MooFest",a social gathering of cows from around the world. MooFest involves a variety of events including haybale stacking, fence jumping, pin the tail on the farmer, and of course, mooing. When the cows all stand in line for a particular event, they moo so loudly that the roar is practically deafening. After participating in this event year after year, some of the cows have in fact lost a bit of their hearing. 

Each cow i has an associated "hearing" threshold v(i) (in the range 1..20,000). If a cow moos to cow i, she must use a volume of at least v(i) times the distance between the two cows in order to be heard by cow i. If two cows i and j wish to converse, they must speak at a volume level equal to the distance between them times max(v(i),v(j)). 

Suppose each of the N cows is standing in a straight line (each cow at some unique x coordinate in the range 1..20,000), and every pair of cows is carrying on a conversation using the smallest possible volume. 

Compute the sum of all the volumes produced by all N(N-1)/2 pairs of mooing cows. 

Input

* Line 1: A single integer, N 

* Lines 2..N+1: Two integers: the volume threshold and x coordinate for a cow. Line 2 represents the first cow; line 3 represents the second cow; and so on. No two cows will stand at the same location. 
  Output

* Line 1: A single line with a single integer that is the sum of all the volumes of the conversing cows. 

Sample Input

4
3 1
2 5
2 6
4 3

Sample Output

57

题意：牛的听力为v，两头牛i,j之间交流，需要max(v[i], v[j])dist(i,j)的音量。应该说，题意是说，v[i]越小，听力越好。
求所有牛交谈时叫声总和∑(max(v[i],v[j])\*abs(dist[j]-dist[i]) ) )

思路：先对牛按照v从小到大排序。对于牛i，它与比他听力还小的牛之间交谈需要音量都是v[i]，再乘以之间的距离就可以了。在排好序后，假设：

count[i]：比i听力小的且x坐标比第i头牛小的牛总数

total[i]：count[i]中那些牛的x坐标总和

alltotal：表示所有比第i头牛听力小的牛的总数的话

那么，原题要求的式子就成了

∑( v[i]\*所有比i听力小的牛到i的总距离 ）

=∑(v[i]\*(count[i]\*x[i] – total + alltotal – total – (i – count[i] – 1)\*x[i] ))

其中，count[i]\*x[i] – total表示所有比i听力小且在它左边的牛们到i的距离总和， alltotal – total – (i – count[i] – 1)\*x[i] ) 表示所有比i听力小且在它右边的牛们到i的距离总和。
剩下的关键就在，如何求count[i]和total[i]更快。因为如果排好序后是扫一遍所有牛的坐标的话，时间复杂度就是n^2了，不行。所以想到了树状数组。树状数组用于动态的求一个数组前i个数的总和。
所以，把count作为一个树状数组，如果在坐标x上有一头牛，那么count[x] = 1。这样求i之前有多少头牛，就是count(i)的一个查询。把total同样作为一个树状数组，如果坐标x上有一头牛，那么total[x] = x。这是因为total数组表示的是距离。这样求i之前听力小于v[i]且在i左边的牛总数，就是total(i)的查询。

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
#define lson l,mid,now<<1
#define rson mid+1,r,now<<1|1
#define LL long long
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fLL;
//const int MAXN=1e4+5;const int MOD=1e9+7;
const int MAXNODES=20005;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long LL;
LL cnt[MAXNODES];
LL s[MAXNODES];
struct Node{
   int v;
   int x;
}in[MAXNODES];
int n;
int lowbit(int x)
{
    return x&(-x);
}

void add(LL *c,int t,int value)
{
    for(int i=t;i<=MAXNODES;i+=lowbit(i))
    {
        c[i]+=value;
    }
}
int sum(LL *c,int x)
{
    int temp=0;
    for(int i=x;i>=1;i-=lowbit(i))
    {
        temp+=c[i];
    }
    return temp;
}
bool cmp(Node a ,Node b)
{
    return a.v<b.v;
}
int main()
{
    while(scanf("%d",&n)!=EOF)
    {
        rep(i,n) scanf("%d%d",&in[i].v,&in[i].x);
        sort(in,in+n,cmp);
        memset(cnt,0,sizeof(cnt));
        memset(s,0,sizeof(s));
        LL ans=0,tot=0;
        rep(i,n)
        {
            update(cnt,in[i].x,1);
            update(s,in[i].x,in[i].x);
            tot+=in[i].x;
            LL s1=sum(cnt,in[i].x);
            LL s2=sum(s,in[i].x);
            ans+=in[i].v*(s1*in[i].x-s2-in[i].x*(i+1-s1)+tot-s2);
        }
        printf("%I64d",ans);
    }
    return 0;
}
```
