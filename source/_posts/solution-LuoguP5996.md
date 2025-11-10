---
title: 题解：LuoguP5996 - Muzeum
date: 2025-03-16 09:27:00
excerpt: 图论 网络流 最小割 模拟最大流
comment: 'utterances'
categories: 
	- 题解
tags: 
	- 图论
	- 网络流
	- 最小割
	- 模拟最大流
---

### 题解
首先可以想到一个很显然的最大权闭合子图做法，连边 $(s,i,v_i)$ 表示贿赂第 $i$ 个警卫的代价，连边 $(j,t,v_j)$ 表示不拿第 $j$ 个手办的代价，警卫向能看到的手办连权值为 $\infty$ 的边，用手办价值和减去最小割即为答案。但是本题数据范围过大，无法直接求最小割，考虑**模拟最大流**。

首先考虑转换题目中给的坐标形式。对于警卫 $i$ 和手办 $j$，仅当
$$
|\frac{x_i-x_j}{y_i-y_j}|\le \frac{w}{h}
$$
手办才能被看到。上式等价于
$$
x_ih-y_iw\le x_jh-y_jw\\
x_ih+y_iw\ge x_jh+y_jw
$$
令 $x=xh+yw,y=xh-yw$，限制就可以转化为 $x_i\ge x_j,y_i\le y_j$。

这样我们就可以用扫描线求解，用 set 维护手办的坐标和剩余流量，从左往右扫描求最大流。对每个警卫**贪心地取 $y$ 最小的手办的流量**直到警卫和 $t$ 之间的边流满或找不到满足的手办，因为更大的 $y$ 可以被更多的警卫取到。这里我们直接二分查找满足条件的手办。

### 代码
```cpp
#include<iostream>
#include<algorithm>
#include<set>
#define int long long
#define pii pair<int,int>
using namespace std;
const int N=2e5+10;
int n,m,w,h,sum;
struct Node{
	int x,y,v;
	bool operator < (const Node &t) const{return x<t.x;}
}a[N],b[N];
multiset<pii> s;
signed main(){
	cin>>n>>m>>w>>h;
	for(int i=1,x,y;i<=n;i++){
		cin>>x>>y>>a[i].v;
		a[i].x=x*h+y*w,a[i].y=x*h-y*w;//转换坐标
		sum+=a[i].v;
	}
	for(int i=1,x,y;i<=m;i++){
		cin>>x>>y>>b[i].v;
		b[i].x=x*h+y*w,b[i].y=x*h-y*w;
	}	
    //扫描线
	sort(a+1,a+n+1);sort(b+1,b+m+1);
	for(int i=1,j=1;i<=m;i++){
		while(j<=n&&a[j].x<=b[i].x) s.insert({a[j].y,a[j++].v});//将手办加入
		int limit=b[i].v;
		while(limit&&s.size()){
			auto it=s.lower_bound({b[i].y,0});//二分
			if(it==s.end()) break;
			pii u=*it;s.erase(it);
			int flow=min(limit,u.second);
			sum-=flow;u.second-=flow;limit-=flow;//增广流量
			if(u.second) s.insert(u);//手办没被榨干，则再次加入 set
		}
	}
	cout<<sum;
	return 0;
}
```