---
title:  "背包笔记"
excerpt: "背包笔记。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Competetive_Programming
tags:
  - DP
---

今天主要接触了三种背包问题，如果能写出DP函数的话，把模板套进去后这类题都不难解出，我觉得学习关键在于对状态转移方程的理解。

## 01背包

每种物品只能取一个。

设value[]是价值数组，cost[]是耗费数组，money是总容量，dp[i][j]代表取到第i个物品，剩余容量是j时的最大价值，

则取第i个物品的话，dp[i][j]=dp[i-1][j-cost[i]]+value[i]，即从上限中减去第i个物品的耗费，并且总价值加上第i个物品的价值。不取第i个物品的话，则dp[i][j]=dp[i-1][j]。也就是说，状态转移方程为：

dp[i][j]=max(dp[i-1][j],dp[i-1][j-cost[i]]+value[i])

由于数组大小有限，可以用一维数组代替二维数组，优化空间复杂度：

```c
void zopack(int cost,int value)
{
    for(int i=money; i>=cost; i--)
        dp[i]=max(dp[i],dp[i-cost]+value);
}
```

## 完全背包

和01背包的区别在于每种物品都可取无限多个。

状态转移方程为dp[i][j]=max(dp[i-1][j-k*cost[i]]+k*value[i])，其中k的个数不定。

将01背包函数的逆序循环改为顺序循环就可变成完全背包函数：

```c
void cpack(int cost,int value)
{
    for(int i=cost;i<=money;i++)
        dp[i]=min(dp[i],dp[i-cost]+value);
}
```

实现原理在于，01背包函数使用的逆序循环代表dp[i-cost]和二维数组dp[i-1][j-cost]等价，也就是说dp[i][j]是由状态dp[i-1] [j-cost[i]]递推而来，在考虑是否取第i件物品时，第i件物品只可能被取1次。

而顺序循环则使dp[i-cost]和二维数组dp[i][j-cost]等价，dp[i] [j-cost[i]]由dp[i][j]递推而来，在考虑是否取第i件物品时，第i件物品可被重复取多次。

## 多重背包

每种物品的数量都有有限个。

多重背包实际上就是多个01背包的组合，但是如果直接一个个分解成01背包会导致超时，因此分解时要借助二进制优化每次分解的个数，比如说把10分解成1+2+4+3，其中分解到8时发现10-1-2-4<8，所以直接把剩余的3拿出来。

此外，如果物品数量很少，可以直接用完全背包来解。

实现函数：

```c
void cpack(int cost,int value)
{
    for(int i=money;i>=cost;i++)
        dp[i]=min(dp[i],dp[i-cost]+value);
}
void zopack(int cost,int value)
{
    for(int i=cost;i>=money;i--)
        dp[i]=max(dp[i],dp[i-cost]+value);
}
void mpack(int cost,int value,int num)
{
    if(cost*num>=money)
    {
        cpack(cost,value);
        return ;
    }
    for(int k=1;k<num;k*=2)
    {
        zopack(k*cost,k*value);
        num-=k;
    }
    zopack(num*cost,num*value);
}
```