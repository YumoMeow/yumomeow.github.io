---
title: 题解：CF1179C - Serge and Dining Room
date: 2025-02-28 19:11:00
excerpt: 数据结构 线段树
categories: 
	- 题解
tags: 
	- 数据结构
	- 线段树
---
### 题意
有 $n$ 道菜和 $m$ 个学生，每个菜有价格 $a_i$，每个学生有 $b_i$ 元。学生按顺序买菜，每个人会买能买得起的最贵的菜，买不起就离开队伍。

每次修改某道菜的价格或者某个学生的钱数，求所有学生买完菜后剩下最贵的菜，如果不剩就输出 $-1$。
### 题解
学生的顺序并不会影响答案。考虑两个学生 $i,j$，$b_i<b_j$，不管是 $i$ 先买还是 $j$ 先买都不会影响到他们买到的菜，最终答案也不会被影响。

实际上我们要求的就是一个最大的 $a_i$，使得买得起这盘菜的学生人数小于价格大于等于 $a_i$ 的菜的数量，这样大于等于 $a_i$ 的菜（除了这一个）都会被买走，剩下最大的就是 $a_i$。对于那些被买走的价格比 $a_i$ 小的菜，我们并不关心。

形式化地说，对于每个 $i$，设 $\ge i$ 的 $b_j$ 数量为 $f_i$， $\ge i$ 的 $a_j$ 数量为 $g_i$，那么我们要求的就是最大的满足 $f_i-g_i<0$ 的 $i$。

考虑加入一个 $a_i$ 对于 $f-g$ 会有什么影响。显然所有 $\le i$ 的 $g_i$ 会 $+1$，$(f_i-g_i)$ 相应减一。加入一个 $b_i$ 时，所有 $\le i$ 的 $f_i$ 会 $+1$，$f_i-g_i$ 相应加一。

建立一个线段树，对每个价格 $i$ 维护 $(f_i-g_i)$。加入$a_i,b_i$ 时就是前缀加减操作，修改时就是区间加减。查询时，只需要查最大的 $\le 0$ 的下标就可以。

### 代码
```cpp
#include<iostream>
#define ls (u<<1)
#define rs (u<<1|1)
#define mid ((l+r)>>1)
#define int long long
using namespace std;
const int N=1e6+10;
int n,m,a[N],b[N],q,mi[N<<2],tag[N<<2];
void pushdown(int u){
	mi[ls]+=tag[u];mi[rs]+=tag[u];
	tag[ls]+=tag[u];tag[rs]+=tag[u];
	tag[u]=0;
}
void insert(int u,int l,int r,int L,int R,int k){
	if(L<=l&&r<=R){
		mi[u]+=k;tag[u]+=k;
		return;
	}
	pushdown(u);
	if(L<=mid) insert(ls,l,mid,L,R,k);
	if(R>mid) insert(rs,mid+1,r,L,R,k);
	mi[u]=min(mi[ls],mi[rs]);
}
int query(int u,int l,int r){
	if(mi[u]>0) return -1;
	if(l==r) return l;
	pushdown(u);
	if(mi[rs]<0) return query(rs,mid+1,r);
	if(mi[ls]<0) return query(ls,l,mid);
	return -1;
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<=n;i++){
		cin>>a[i];
		insert(1,1,N-1,1,a[i],-1);
	}
	for(int i=1;i<=m;i++){
		cin>>b[i];
		insert(1,1,N-1,1,b[i],1);
	}
	cin>>q;
	while(q--){
		int op,i,x;
		cin>>op>>i>>x;
		if(op==1){
			if(x>a[i]) insert(1,1,N-1,a[i]+1,x,-1);
			else insert(1,1,N-1,x+1,a[i],1);
            // 这里可以画图理解一下
            // 就是减去原本 a_i 的贡献，加上 x_i 的贡献，影响的就是不重叠的那段区间
			a[i]=x;
		}
		else{
			if(x>b[i]) insert(1,1,N-1,b[i]+1,x,1);
			else insert(1,1,N-1,x+1,b[i],-1);
			b[i]=x;			
		}
		cout<<query(1,1,N-1)<<'\n';
	}
	return 0;
}
```