---
title: 题解：LuoguP3268 - 圆的异或并
date: 2025-02-26 19:58:00
excerpt: 扫描线 set
categories: 
    - 题解
tags: 
    - 扫描线
    - set
---

### 题意
在二维平面中给定 $n$ 个圆。已知这些圆两两没有交点，即两圆的关系只存在相离和包含。求这些圆的异或面积并。

异或面积并定义为：当一片区域在奇数个圆内，则计入其面积，否则，当一片区域在偶数个圆内则不计入其面积。

### 题解
求解的关键在于，判断一个圆在奇数个圆内还是在偶数个圆内。

设圆心为 $(a,b)$，半径为 $r$。将每一个圆沿着直线 $y=b$ 切成两个半圆，比较好考虑。

从左往右扫描每一个圆，记录两个点 $a-r$ 与 $a+r$。当扫到 $x=a-r$ 时，判断这个圆在奇数个圆内或在偶数个圆内。

具体方法：先将上半圆弧插入到某个数据结构内，查询它下面的第一个圆弧。如果是上半圆弧，则这两个圆都被同一个圆包含在内，它与那个圆的奇偶性相同。否则，当前圆包含在上一个圆内，奇偶性与其相反。维护一个 bool 数组记录奇偶性，相反时异或 $1$ 即可。这个可以用 $set$ 维护，插入时记录地址，找上一个就行。

假设扫到直线 $x=l$，就要按照与直线 $x=l$ 的交点来排序。具体来说，上半圆弧交点为 $b+\sqrt {r^2-(l-a)^2}$，下半圆弧交点为 $b-\sqrt {r^2-(l-a)^2}$。重载一下结构体的运算符即可。

### 代码
```cpp
#include<iostream>
#include<cmath>
#include<set>
#include<algorithm>
#define int long long
using namespace std;
const int N=5e5+10;
double eps=1e-9;
int n,typ[N];
double x[N],y[N],r[N],l;
struct Node{
    int id,t;
    friend bool operator < (Node a,Node b){
        double u,v;
        if(abs(l-x[a.id])==r[a.id]) u=y[a.id];
        else u=y[a.id]+a.t*sqrt(r[a.id]*r[a.id]-(l-x[a.id])*(l-x[a.id]));
        if(abs(l-x[b.id])==r[b.id]) v=y[b.id];
        else v=y[b.id]+b.t*sqrt(r[b.id]*r[b.id]-(l-x[b.id])*(l-x[b.id]));
        if(a.t==1) u+=eps;
        if(b.t==1) v+=eps;
        return u<v;            
    }

}c[N];
struct Dot{
	double pos;
    int id,t;
    bool operator < (const Dot &t) const{
        return pos<t.pos;
    }
}d[N];
set<Node> s;
int res;
signed main(){
    cin>>n;
    for(int i=1;i<=n;i++){
        cin>>x[i]>>y[i]>>r[i];
        d[i]=(Dot){x[i]-r[i],i,1};
        d[i+n]=(Dot){x[i]+r[i],i,-1};
    }
    sort(d+1,d+2*n+1);
    for(int i=1;i<=2*n;i++){
        l=d[i].pos;
        int id=d[i].id;
        if(d[i].t!=-1){
            auto it=s.insert((Node){id,1}).first;
            if(it==s.begin()) typ[id]=1;
            else{
                it--;
                if((*it).t==-1) typ[id]=typ[(*it).id]^1;
                else typ[id]=typ[(*it).id];
            }
            if(typ[id]) res+=r[id]*r[id];
            else res-=r[id]*r[id];
            s.insert((Node){id,-1});
        }
        else s.erase((Node){id,1}),s.erase((Node){id,-1});
    }
    cout<<res;
    return 0;
}
```