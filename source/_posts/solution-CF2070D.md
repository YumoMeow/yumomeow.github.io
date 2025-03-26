---
title: 题解：CF2070D - Tree Jumps
date: 2025-03-26 18:38:00
excerpt: 区间 DP
categories: 
    - 题解
tags: 
    - 动态规划
    - 区间 DP
---
### 题意
给定一棵树，可以从一个节点走到下一层的节点，但当处在非根节点时不可以走到儿子节点，求合法走法的方案数。

### 题解
考虑 dp。设 $f_i$ 表示以点 $i$ 为结尾的合法序列数，答案即为 $\sum f_i$。考虑转移。设节点 $i$ 位于 $dep_i$ 层。
$$
f_i=\bigg(\sum_{\{j|dep_j=dep_i-1\}}f_j\bigg)-f_{fa_i}
$$
将每一层的 $f_i$ 求和即可优化到 $O(n)$。

由于转移时要先求出上一层的所有答案，所以使用 BFS 来求。注意特判节点 $1$，要将以它结尾的一种方案计入 $sum$ 与 $ans$，但 $f_1$ 应该等于 $0$ 以保证答案正确，因为第二层不需要减去。

### 代码
```cpp
#include<iostream>
#include<queue>
#define int long long
using namespace std;
const int N=3e5+10,MOD=998244353;
int n,fa[N],sum[N],head[N],tot,f[N],dep[N],ans;
queue<int> q;
struct Edge{int to,nxt;}e[N];
void add(int u,int v){e[++tot]={v,head[u]};head[u]=tot;}
int solve(){
    for(int i=1;i<=n;i++) head[i]=dep[i]=f[i]=sum[i]=0;
    ans=0;dep[1]=sum[1]=ans=1;
    cin>>n;
    for(int i=2;i<=n;i++) cin>>fa[i],add(fa[i],i),dep[i]=dep[fa[i]]+1;
    for(int i=head[1];i;i=e[i].nxt) q.push(e[i].to);
    while(q.size()){
        int u=q.front();q.pop();
        f[u]=(sum[dep[u]-1]-f[fa[u]]+MOD)%MOD;
        sum[dep[u]]=(sum[dep[u]]+f[u])%MOD;
        ans=(ans+f[u])%MOD;
        for(int i=head[u];i;i=e[i].nxt) q.push(e[i].to);
    }
    cout<<ans<<'\n';
    return 0;
}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    int T;
    cin>>T;
    while(T--) solve();
    return 0;
}
```