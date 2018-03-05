---
title: "O(N^(1/3))统计因子数量"
excerpt: "CF Blog里好东西真多……"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Competetive_Programming
tags:
  - 算法
  - 数论
---

这一个月都没写博客，光顾着刷题了……17年多校基本上是数学场（以前的没怎么做过，不知道这是不是多校的传统），计数问题做得我是痛不欲生。

扯远了，今天看到个很好用的玩意，可以处理1e20左右的数，昨天那道题我都觉得可以用这玩意试着卡一下，1e6*1e2嘛……（不过再算上常数级的计算，还是要T吧）

(Polland-Rho是O(N^(1/4))，看下面评论也提到了，不过那个我写了半天没法用，改天重写一遍吧。这个要好写多了……)

基本思想很简单，先把1e6以内的质数打表，然后除掉这些质数。对于剩下的大质数，事实上不需要去管它们，直接去数就好了。

设剩下的部分是X，由于经过了前述的处理，X只可能有以下几种情况:

1.X是质数，d(X)=2;

2.X是质数的平方数,d(X)=3;

3.X是两个不同质数的乘积,d(X)=4.

检测质数可以用Miller-Rabin，logN搞定（我前几天用这个写题WA了一次，不过当时的试错次数是20，把S设到50左右理论上就很难出错了吧）。

实现代码如下。（不包含质数打表和M-R算法的部分）

```c++
LL Countdiv(LL n)
{
    Prime(1e6);LL ans=1L;
    for(int i=0;i<cnt;i++)//cnt is the number of primes below 1e6
    {
        if(i*i*i>n) break;
        LL num=1;
        while(n%prime[i]==0)
        {
            n/=prime[i];
            num++;
        }
        ans*=num;
    }
    LL sq=sqrt(n);
    if(Miller_Rabin(n)) ans*=2L;
    else if(Miller_Rabin(sq)&&sq*sq==n) ans*=3L;
    else if(n!=1) ans*=4L;
}
```

差点忘记附上[原地址](http://codeforces.com/blog/entry/22317)。