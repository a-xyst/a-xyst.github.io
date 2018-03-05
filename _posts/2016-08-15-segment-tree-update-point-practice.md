---
title:  "线段树单点更新练习"
excerpt: "线段树单点更新练习。"
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


### HDU 1166


Problem Description

C国的死对头A国这段时间正在进行军事演习，所以C国间谍头子Derek和他手下Tidy又开始忙乎了。A国在海岸线沿直线布置了N个工兵营地,Derek和Tidy的任务就是要监视这些工兵营地的活动情况。由于采取了某种先进的监测手段，所以每个工兵营地的人数C国都掌握的一清二楚,每个工兵营地的人数都有可能发生变动，可能增加或减少若干人手,但这些都逃不过C国的监视。
中央情报局要研究敌人究竟演习什么战术,所以Tidy要随时向Derek汇报某一段连续的工兵营地一共有多少人,例如Derek问:“Tidy,马上汇报第3个营地到第10个营地共有多少人!”Tidy就要马上开始计算这一段的总人数并汇报。但敌兵营地的人数经常变动，而Derek每次询问的段都不一样，所以Tidy不得不每次都一个一个营地的去数，很快就精疲力尽了，Derek对Tidy的计算速度越来越不满:"你个死肥仔，算得这么慢，我炒你鱿鱼!”Tidy想：“你自己来算算看，这可真是一项累人的工作!我恨不得你炒我鱿鱼呢!”无奈之下，Tidy只好打电话向计算机专家Windbreaker求救,Windbreaker说：“死肥仔，叫你平时做多点acm题和看多点算法书，现在尝到苦果了吧!”Tidy说："我知错了。。。"但Windbreaker已经挂掉电话了。Tidy很苦恼，这么算他真的会崩溃的，聪明的读者，你能写个程序帮他完成这项工作吗？不过如果你的程序效率不够高的话，Tidy还是会受到Derek的责骂的.
 

Input

第一行一个整数T，表示有T组数据。
每组数据第一行一个正整数N（N<=50000）,表示敌人有N个工兵营地，接下来有N个正整数,第i个正整数ai代表第i个工兵营地里开始时有ai个人（1<=ai<=50）。
接下来每行有一条命令，命令有4种形式：

(1) Add i j,i和j为正整数,表示第i个营地增加j个人（j不超过30）

(2)Sub i j ,i和j为正整数,表示第i个营地减少j个人（j不超过30）;

(3)Query i j ,i和j为正整数,i<=j，表示询问第i到第j个营地的总人数;

(4)End 表示结束，这条命令在每组数据最后出现;

每组数据最多有40000条命令
 

Output

对第i组数据,首先输出“Case i:”和回车,
对于每个Query询问，输出一个整数并回车,表示询问的段中的总人数,这个数保持在int以内。
 

Sample Input

1
10
1 2 3 4 5 6 7 8 9 10

Query 1 3

Add 3 6

Query 2 7

Sub 10 2

Add 6 3

Query 3 10

End 
 

Sample Output

Case 1:
6
33
59

模板题，针对输入进行相应的操作即可

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
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fLL;
//const int MAXN=1e4+5;const int MOD=1e9+7;
const int MAXNODES=50005;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long LL;
int a[MAXNODES<<2];
void pushup(int rt)
{
    a[rt]=(a[rt<<1]+a[(rt<<1)|1]);
}
void build(int l,int r,int rt)
{
    if(l==r)
    {
        scanf("%d",&a[rt]);
        return;
    }
    delf;
    build(lson);
    build(rson);
    pushup(rt);
}
void update(int p,int q,int l,int r,int rt)
{
    if(l==r)
    {
        a[rt]+=q;
        return;
    }
    delf;
    if(p<=m) update(p,q,lson);
    else update(p,q,rson);
    pushup(rt);
}
int query(int L,int R,int l,int r,int rt)
{
    if(L<=l&&R>=r) return a[rt];
    delf;
    int ret=0;
    if(L<=m) ret+=query(L,R,lson);
    if(R>m) ret+=query(L,R,rson);
    return ret;
}
int main()
{
    int t,n,a,b;
    scanf("%d",&t);
    rel(i,t)
    {
        printf("Case %d:\n",i);
        scanf("%d",&n);
        build(1,n,1);
        char tmp[10];
        while(scanf("%s",tmp))
        {
            if(tmp[0]=='E') break;
            scanf("%d%d",&a,&b);
            if(tmp[0]=='Q') printf("%d\n",query(a,b,1,n,1));
            if(tmp[0]=='S') update(a,-b,1,n,1);
            if(tmp[0]=='A') update(a,b,1,n,1);
        }
    }
    return 0;
}
```

### HDU 1754

Problem Description

很多学校流行一种比较的习惯。老师们很喜欢询问，从某某到某某当中，分数最高的是多少。
这让很多学生很反感。

不管你喜不喜欢，现在需要你做的是，就是按照老师的要求，写一个程序，模拟老师的询问。当然，老师有时候需要更新某位同学的成绩。
 

Input

本题目包含多组测试，请处理到文件结束。
在每个测试的第一行，有两个正整数 N 和 M ( 0<N<=200000,0<M<5000 )，分别代表学生的数目和操作的数目。
学生ID编号分别从1编到N。
第二行包含N个整数，代表这N个学生的初始成绩，其中第i个数代表ID为i的学生的成绩。
接下来有M行。每一行有一个字符 C (只取'Q'或'U') ，和两个正整数A，B。
当C为'Q'的时候，表示这是一条询问操作，它询问ID从A到B(包括A,B)的学生当中，成绩最高的是多少。
当C为'U'的时候，表示这是一条更新操作，要求把ID为A的学生的成绩更改为B。
 

Output

对于每一次询问操作，在一行里面输出最高成绩。
 

Sample Input

5 6
1 2 3 4 5
Q 1 5
U 3 6
Q 3 4
Q 4 5
U 2 9
Q 1 5
 

Sample Output

5
6
5
9

Hint

Huge input,the C function scanf() will work better than cin

同样是模板题，和前一题的不同在于，前一题询问区间和，这一题询问区间内最大值，针对需求更改pushup和update函数的内容即可

此外，%*c%c可以简洁地解决读取缓冲区字符的问题

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
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fLL;
//const int MAXN=1e4+5;const int MOD=1e9+7;
const int MAXNODES=4000001;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long LL;
int mx[MAXNODES];
int max(int a,int b)
{
    return a>b?a:b;
}
void pushup(int rt)
{
    mx[rt]=max(mx[rt<<1],mx[(rt<<1)|1]);
}
void build(int l,int r,int rt)
{
    if(l==r)
    {
        scanf("%d",&mx[rt]);
        return;
    }
    delf;
    build(lson);
    build(rson);
    pushup(rt);
}
void update(int p,int q,int l,int r,int rt)
{
    if(l==r)
    {
        mx[rt]=q;
        return;
    }
    delf;
    if(p<=m) update(p,q,lson);
    else update(p,q,rson);
    pushup(rt);
}
int query(int L,int R,int l,int r,int rt)
{
    if(L<=l&&r<=R) return mx[rt];
    delf;
    int ans=0;
    if(L<=m) ans=max(ans,query(L,R,lson));
    if(R>m) ans=max(ans,query(L,R,rson));
    return ans;
}
int main()
{
    int n,m,a,b,i;
    char c;
    while(scanf("%d %d",&n,&m)!=EOF)
    {
        build(1,n,1);
        for(i=0;i<m;i++)
        {
            scanf("%*c%c%d %d",&c,&a,&b);
            if(c=='Q')
                printf("%d\n",query(a,b,1,n,1));
            else
                update(a,b,1,n,1);
        }
    }
    return 0;
}
```

### HDU 1394

Problem Description

The inversion number of a given number sequence a1, a2, ..., an is the number of pairs (ai, aj) that satisfy i < j and ai > aj.

For a given sequence of numbers a1, a2, ..., an, if we move the first m >= 0 numbers to the end of the seqence, we will obtain another sequence. There are totally n such sequences as the following:

a1, a2, ..., an-1, an (where m = 0 - the initial seqence)
a2, a3, ..., an, a1 (where m = 1)
a3, a4, ..., an, a1, a2 (where m = 2)
...
an, a1, a2, ..., an-1 (where m = n-1)

You are asked to write a program to find the minimum inversion number out of the above sequences.
 

Input

The input consists of a number of test cases. Each case consists of two lines: the first line contains a positive integer n (n <= 5000); the next line contains a permutation of the n integers from 0 to n-1.
 

Output

For each case, output the minimum inversion number on a single line.
 

Sample Input

10
1 3 6 9 0 8 5 7 4 2
 

Sample Output

16

题意：
一个由0..n-1组成的序列，每次可以把队首的元素移到队尾，求形成的n个序列中最小逆序对数目
          
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
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fLL;
//const int MAXN=1e4+5;const int MOD=1e9+7;
const int MAXNODES=5005;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long LL;
int b[MAXNODES];
int a[MAXNODES<<2];
void pushup(int rt)
{
    a[rt]=(a[rt<<1]+a[(rt<<1)|1]);
}
void build(int l,int r,int rt)
{
    if(l==r)
    {
        a[rt]=0;
        return;
    }
    delf;
    build(lson);
    build(rson);
    pushup(rt);
}
void update(int p,int l,int r,int rt)
{
    if(l==r)
    {
        a[rt]++;
        return;
    }
    delf;
    if(p<=m) update(p,lson);
    else update(p,rson);
    pushup(rt);
}
int query(int L,int R,int l,int r,int rt)
{
    if(L<=l&&R>=r) return a[rt];
    delf;
    int ret=0;
    if(L<=m) ret+=query(L,R,lson);
    if(R>m) ret+=query(L,R,rson);
    return ret;
}
int main()
{
    int n;
    while(scanf("%d",&n)!=EOF)
    {
        int sum=0,ans;
        build(0,n-1,1);
        rep(i,n)
        {
            scanf("%d",&b[i]);
            sum+=query(b[i]+1,n-1,0,n-1,1);
            /* 
              query求出区间[b[i], n - 1]中的点数，因为这个区 
              间内的点都比b[i]先插入且比b[i]大，所以，这个 
              区间内的点的个数就等于b[i]的逆序数, 把这些点的 
              逆序数全加起来，就得到整个序列的逆序数sum。 
            */
            update(b[i],0,n-1,1);//更新线段内的点数
        }
        ans=sum;
        rep(i,n)
        {
            sum+=(n-1-2*b[i]);
            if(sum<ans) ans=sum;
            /* 
              因为序列为[0, n-1]，若最前面一个数为x，序列中比x 
              小的数为[0, x-1], 共x个，比x大的数为[x+1, n-1], 
              共n-x-1个，将x移到最后，比x小的数的逆序数均减1， 
              x的前面比x大的数有n-x-1个，x的逆序数增加n-x-1。 
              所以新序列的逆序数为原序列的逆序数加上n-2*x-1。 
            */
        }
        printf("%d\n",ans);
    }
    return 0;
}

```

### HDU 2795

Problem Description

At the entrance to the university, there is a huge rectangular billboard of size h*w (h is its height and w is its width). The board is the place where all possible announcements are posted: nearest programming competitions, changes in the dining room menu, and other important information.

On September 1, the billboard was empty. One by one, the announcements started being put on the billboard.

Each announcement is a stripe of paper of unit height. More specifically, the i-th announcement is a rectangle of size 1 * wi.

When someone puts a new announcement on the billboard, she would always choose the topmost possible position for the announcement. Among all possible topmost positions she would always choose the leftmost one.

If there is no valid location for a new announcement, it is not put on the billboard (that's why some programming contests have no participants from this university).

Given the sizes of the billboard and the announcements, your task is to find the numbers of rows in which the announcements are placed.
 

Input

There are multiple cases (no more than 40 cases).

The first line of the input file contains three integer numbers, h, w, and n (1 <= h,w <= 10^9; 1 <= n <= 200,000) - the dimensions of the billboard and the number of announcements.

Each of the next n lines contains an integer number wi (1 <= wi <= 10^9) - the width of i-th announcement.
 

Output

For each announcement (in the order they are given in the input file) output one number - the number of the row in which this announcement is placed. Rows are numbered from 1 to h, starting with the top row. If an announcement can't be put on the billboard, output "-1" for this announcement.
 

Sample Input

3 5 5
2
4
3
3
3
 

Sample Output

1
2
1
3
-1
 
题意:h\*w的木板,放进一些1\*L的物品,求每次放空间能容纳且最上边的位子

思路:每次找到最大值的位子,然后减去L

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
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fLL;
//const int MAXN=1e4+5;const int MOD=1e9+7;
const int MAXNODES=4000001;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long LL;
int a[MAXNODES],b[MAXNODES];
int h,w,n;
int max(int a,int b)
{
    return a>b?a:b;
}
void pushup(int rt)
{
    a[rt]=max(a[rt<<1],a[(rt<<1)|1]);
}
void build(int l,int r,int rt)
{
    if(l==r)
    {
        a[rt]=w;
        return;
    }
    delf;
    build(lson);
    build(rson);
    pushup(rt);
}
void update(int p,int q,int l,int r,int rt)
{
    if(l==r)
    {
        a[rt]-=q;
        return;
    }
    delf;
    if(p<=m) update(p,q,lson);
    else update(p,q,rson);
    pushup(rt);
}
int query(int k,int l,int r,int rt)
{
    if(l==r) return l;
    delf;
    int ans=0;
    if(a[rt]>=k)
    {
        if(a[rt<<1]>=k) ans=query(k,lson);
        else ans=query(k,rson);
    }
    else ans=0;
    return ans;
}
int main()
{
    while(scanf("%d%d%d",&h,&w,&n)!=EOF)
    {
        if(h>n) h=n;//根据题意，因为最多放n个公告，占用的最大高度也只有n
        build(1,h,1);
        rep(i,n)
        {
            scanf("%d",&b[i]);
            if(a[1]<b[i]) {printf("-1\n");continue;}
            int k=query(b[i],1,h,1);
            printf("%d\n",k);
            update(k,b[i],1,h,1);
        }
    }
    return 0;
}

```

### CodeForces 339D

Xenia the beginner programmer has a sequence a, consisting of 2n non-negative integers: a1, a2, ..., a2n. Xenia is currently studying bit operations. To better understand how they work, Xenia decided to calculate some value v for a.

Namely, it takes several iterations to calculate value v. At the first iteration, Xenia writes a new sequence a1 or a2, a3 or a4, ..., a2n - 1 or a2n, consisting of 2n - 1 elements. In other words, she writes down the bit-wise OR of adjacent elements of sequence a. At the second iteration, Xenia writes the bitwise exclusive OR of adjacent elements of the sequence obtained after the first iteration. At the third iteration Xenia writes the bitwise OR of the adjacent elements of the sequence obtained after the second iteration. And so on; the operations of bitwise exclusive OR and bitwise OR alternate. In the end, she obtains a sequence consisting of one element, and that element is v.

Let's consider an example. Suppose that sequence a = (1, 2, 3, 4). Then let's write down all the transformations (1, 2, 3, 4)  →  (1 or 2 = 3, 3 or 4 = 7)  →  (3 xor 7 = 4). The result is v = 4.

You are given Xenia's initial sequence. But to calculate value v for a given sequence would be too easy, so you are given additional m queries. Each query is a pair of integers p, b. Query p, b means that you need to perform the assignment ap = b. After each query, you need to print the new value v for the new sequence a.

Input

The first line contains two integers n and m (1 ≤ n ≤ 17, 1 ≤ m ≤ 105). The next line contains 2n integers a1, a2, ..., a2n (0 ≤ ai < 230). Each of the next m lines contains queries. The i-th line contains integers pi, bi (1 ≤ pi ≤ 2n, 0 ≤ bi < 230) — the i-th query.

Output

Print m integers — the i-th integer denotes value v for sequence a after the i-th query.

Examples

input

2 4
1 6 3 5
1 4
3 4
1 2
1 2

output

1
3
3
3

题意：

输入n，m以及2^n个数字组成的数组

接下来是m行，每行输入p，b，将a[p]的值改成b，然后计算数值，计算规则如下：

第一次计算b1=a[1]\|a[2],b2=a[3]\|a[4],b3=a[5]\|a[6],b4=a[7]\|a[8] 

第二次计算c1=b1^b2,c2=b3^b4

第四次计算v=c1\|c2

输出v


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
#define lson l,m,rt<<1
#define rson m+1,r,rt<<1|1
#define delf int m=(l+r)>>1
//static const int INF = 0x3f3f3f3f; static const long long INFL = 0x3f3f3f3f3f3f3f3fint;
//const int MAXN=1e4+5;const int MOD=1e9+7;
//typedef vector<int> vi; typedef pair<int, int> pii; typedef vector<pair<int, int> > vpii; typedef long long int;
const int MAXNODES=1<<17;
int a[MAXNODES<<2];
int op[MAXNODES<<2];
int spow(int x)
{
    int ret=1;
    rep(i,x) ret<<=1;
    return ret;
}
void pushup(int rt)
{
    if(!op[rt])a[rt]=(a[rt<<1]^a[(rt<<1)|1]);
    else a[rt]=(a[rt<<1]|a[(rt<<1)|1]);
}
void build(int l,int r,int rt)
{
    if(l==r)
    {
        scanf("%d",&a[rt]);
        op[rt]=0;
        return;
    }
    delf;
    build(lson);
    build(rson);
    if(!op[rt<<1]) op[rt]=1;
    else op[rt]=0;
    pushup(rt);
}
void update(int p,int q,int l,int r,int rt)
{
    if(l==r)
    {
        a[rt]=q;
        return;
    }
    delf;
    if(p<=m) update(p,q,lson);
    else update(p,q,rson);
    pushup(rt);
}
int main()
{
    //freopen("test.txt","r",stdin);
    int n,m,t1,t2;
    scanf("%d%d",&n,&m);
    n=spow(n);
    build(1,n,1);
    rep(i,m)
    {
        scanf("%d%d",&t1,&t2);
        update(t1,t2,1,n,1);
        printf("%d\n",a[1]);
    }
    return 0;
}
```
