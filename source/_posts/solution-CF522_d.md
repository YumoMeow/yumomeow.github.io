---
title: 题解：CF522D - Closest Equals
date: 2025-02-26 19:45:00
excerpt: 扫描线 线段树
categories: 
    - 题解
tags: 
    - 扫描线
    - 线段树
---

### 题意
给定一个序列，每次询问区间 $[l,r]$ 中距离最近的两个相同的数的距离。

### 题解
两个数 $a,b$ 可以被选中的必要条件是 $l\le a$ 且 $b\le r$。

以左端点为横轴，右端点为纵轴建立坐标系，将询问区间转化为一个点 $(l,r)$。对于序列中每个数预处理出上一个和它相同数的位置，记为 $i$ 与 $pre_i$，答案一定在这之中。将这些点放入二维平面，这样，在直线 $x=l$ 右边，$y=r$ 下边的点就满足条件。

将询问离线后从下往上扫描，用线段树记录 $x$ 轴上的信息。扫到 $y=i$ 时，将 $i-pre_i$ 插入到 $pre_i$ 对应的点中，之后处理所有右端点为 $i$ 的询问，在线段树上查后缀 $\max$ 即可。

### 代码
```cpp
#include<iostream>
#include<vector>
#include<algorithm>
#define int long long
#define mid ((l+r)>>1)
#define ls (u<<1)
#define rs (u<<1|1)
using namespace std;
const int N=5e5+10;
int n,m,a[N],pre[N],mi[N<<2],ppre[N],ans[N],X[N],p;
vector<vector<pair<int,int> > > v(N);
void insert(int u,int l,int r,int U,int k){
	if(l==r){
		mi[u]=min(mi[u],k);
		return;
	}
	if(U<=mid) insert(ls,l,mid,U,k);
	else insert(rs,mid+1,r,U,k);
	mi[u]=min(mi[ls],mi[rs]);
}
int query(int u,int l,int r,int L,int R){
	if(L<=l&&r<=R) return mi[u];
	int res=1e9;
	if(L<=mid) res=min(res,query(ls,l,mid,L,R));
	if(R>mid) res=min(res,query(rs,mid+1,r,L,R));
	return res;
}
void build(int u,int l,int r){
	mi[u]=1e9;
	if(l==r) return;
	build(ls,l,mid);
	build(rs,mid+1,r);
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<=n;i++){
		cin>>a[i];
		X[i]=a[i];
	}
	sort(X+1,X+n+1);
	p=unique(X+1,X+n+1)-X-1;
	build(1,1,n);
	for(int i=1;i<=n;i++){
		a[i]=lower_bound(X+1,X+p+1,a[i])-X;
		pre[i]=ppre[a[i]];
		ppre[a[i]]=i;
	}
	for(int i=1,l,r;i<=m;i++){
		cin>>l>>r;
		v[r].push_back({l,i});
	}	
	for(int i=1;i<=n;i++){
		if(pre[i]) insert(1,1,n,pre[i],i-pre[i]);
		for(auto it:v[i]){
			ans[it.second]=query(1,1,n,it.first,n);
			if(ans[it.second]==1e9) ans[it.second]=-1;
		}
	}
	for(int i=1;i<=m;i++) cout<<ans[i]<<'\n';
	return 0;
}
```