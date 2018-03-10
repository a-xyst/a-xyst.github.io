---
title:  "平衡树简单笔记"
excerpt: "平衡树简单笔记。"
toc: true
toc_label: "目录"
toc_icon: "gear"
categories:
  - Competetive_Programming
tags:
  - 算法
  - 数据结构
  - 平衡树
---

## 平衡树

排序二叉树(BST)的结构在最坏情况下退化为线性，因此为保证BST的查找效率，实现时通常采用平衡树，平衡树是拥有一些特殊性质和操作的BST。
常用平衡树有：Treap/Splay/SBT/红黑树/替罪羊树/朝鲜树等，竞赛中一般采用Treap和Splay。

### Treap

Treap中每个结点具有键值和优先级，对于键值来说这棵树是排序二叉树，对于优先级来说这棵树是堆。因此Treap同时具有排序和求极值的功能。

```c++
struct treap
{
	int l,r,rnd,val;//son,priority,val
}t[1005];
void rturn(int &k)//left son to root
{
	int tmp=t[k].l;t[k].l=t[tmp].r;t[tmp].r=k;k=tmp;
}
void lturn(int &k)//right son to root
{
	int tmp=t[k].r;t[k].r=t[tmp].l;t[tmp].l=k;k=tmp;
}
void insert(int &k,int x)//current root,key
{
	if(k==0)
	{
		k=++cnt;
		t[k].val=x;t[k].rnd=rand();
		t[k].l=t[k].r=-1;
		return;
	}
    if(t[k].val==x)return;
	else if(x>t[k].val)
	{
		insert(t[k].r,x);
		if(t[t[k].r].rnd<t[k].rnd)lturn(k);//if right has larger rnd,move to root
	}
	else
	{
		insert(t[k].l,x);
		if(t[t[k].l].rnd<t[k].rnd)rturn(k);
	}
}
void remove(int &k,int x)
{
    if(x==t[k].val)
    {
        if(t[k].l!=-1&&t[k].r==-1) t[k]=t[k].l;
        else if(t[k].l==-1&&t[k].r!=-1) t[k]=t[k].r;
        else
        {
            int d=(t[t[k].l].rnd>t[t[k].r].rnd)?1:0;
            if(d)//left to root,remove from right
            {
                rturn(k);
                remove(t[k].r,x);
            }
            else
            {
                lturn(k);
                remove(t[k].l,x);
            }
        }
    }
    else if(x>t[k].val) remove(t[k].r,x);
	else remove(t[k].l,x);
}
void query(int k,int val)
{
    if(!k)return;
    if(t[k].val==val){return k;}
    if(t[k].val>val)query(t[k].l,val);
    else query(t[k].r,val);
}
```

### Splay

Splay支持快速的伸展、分裂和合并，它可以把一个指定的结点转换为根结点，可以把一棵树分裂成给定两子树元素数目的两棵树，可以把两棵子树合并成一棵树。

```c++
const int maxn=1005;
int n,size,root;
int ch[maxn][2],num[maxn],fa[maxn];
void pushup(int x){};
void rotate(int x,int &k)//exchange x with its father
{
    int y=fa[x],z=fa[y],l,r;
    if(ch[y][0]==x)l=0;else l=1;r=l^1;
    if(y==k)k=x;
    else{if(ch[z][0]==y)ch[z][0]=x;else ch[z][1]=x;}
    fa[x]=z;fa[y]=x;fa[ch[x][r]]=y;
    ch[y][l]=ch[x][r];ch[x][r]=y;
}
void splay(int x,int &k)// move x to k
{
	int y,z;
	while(x!=k)
	{
		y=fa[x],z=fa[y];
		if(y!=k)
		{
			if((ch[y][0]==x)^(ch[z][0]==y))rotate(x,k);
			else rotate(y,k);
		}
		rotate(x,k);
	}
}
void insert(int x,int v)
{
    int y;
    while(true)
    {
        y=ch[x][num[x]<v];
        if(y==0)//new node
        {
            y=++size;
            num[y]=v;
            ch[y][0]=ch[y][1]=0;
            fa[y]=x;
            ch[x][num[x]<v]=y;
            break;
        }
        x=y;
    }
    splay(y,root);//move to root after insertion
}
void remove(int x)
{
	splay(x,root);
	if(ch[x][0]*ch[x][1]==0)//x has one child,make it root
	{root=ch[x][0]+ch[x][1];}
	else
	{
		int k=ch[x][1];
		while(ch[k][0])k=ch[k][0];//most left one in right tree
		ch[k][0]=ch[x][0];fa[ch[x][0]]=k;//k replace x
		root=ch[x][1];//right tree to root
	}
	fa[root]=0;
}
int pre(int x)//most right one in left tree
{
    int tmp=ch[x][0];
    while(ch[tmp][1])tmp=ch[tmp][1];
    return num[tmp];
}
int suc(int x)//most left one in right tree
{
    int tmp=ch[x][1];
    while(ch[tmp][0])tmp=ch[tmp][0];
    return num[tmp];
}
void merge(int left,int right)
{
    splay(left,num[left]);
    ch[left][1]=right;
    pushup(left);
}
void split(int &o,int k,int &left,int &right)
{
    splay(o,k);
    left=o;
    right=ch[o][1];
    ch[o][1]=0;
    pushup(left);
}
```