---
title: 题解：UVA11167 - Monkeys in the Emei Mountain
date: 2025-03-10 12:32:00
excerpt: 图论 网络流 最大流
comment: 'utterances'
categories: 
    - 题解
tags: 
    - 图论
    - 网络流
    - 最大流
---

### 题意
峨眉山有 $n$ 只猴子和一个取水点，这个取水点同一时间能容纳 $m$ 只猴子喝水。  
每只猴子有三个参数 $v,a,b$，代表它需要在 $[a,b)$ 时间需要花 $v$ 个单位的时间来喝水。假设所有猴子都非常团结，他们会以最优方案来分配水源，请你帮助它们知道能否让所有猴子都喝上水，如果能，输出一种方案。

### 题解
网络流题，考虑如何建图。建立源点 $s$ 与汇点 $t$，$n$ 个点表示 $n$ 只猴子，其余点表示时间区间 $[i,i+1)$。

我们要连几种边：
- 源点向每个猴子连一条容量为 $v$ 的边，表示需要 $v$ 的时间喝水。
- 每个猴子向 $[a,b)$ 中每个子区间连一条容量为 $1$ 的边，表示可以在这个时间段喝水。
- 每个子区间向汇点连一条容量为 $m$ 的边，表示这个时间段最多容纳 $m$ 个猴子喝水。

这样，我们就同时满足了猴子喝水时间和每个时段猴子数量的限制，跑一遍最大流即可求出可以被满足的时间段有多少个，将它与 $\sum v$ 比较。如果恰好相等则代表每个时间段都被分配完毕，输出 `Yes`，否则输出 `No`。

考虑如何输出方案。开一个存时间段的 vector，对每个猴子遍历指向时间段的边，若剩余流量为 $0$ 表示这个时间段被占用，如果此时 vector 中有相邻区间则直接合并，否则加入 vector。遍历每个 vector 输出即可。

### 代码
```cpp
#include<iostream>
#include<cstring>
#include<queue>
#include<vector>
using namespace std;
const int N=1e5+10,INF=1e9;
int n,m,head[N],cur[N],dep[N],tot,S,T,sum;
struct Edge{int to,nxt,f;}e[N<<5];
void add(int u,int v,int c){
    e[++tot]={v,head[u],c};head[u]=tot;
    e[++tot]={u,head[v],0};head[v]=tot;
}
bool bfs(){
    queue<int> q;memset(dep,-1,sizeof(dep));
    q.push(S);cur[S]=head[S];dep[S]=0;
    while(q.size()){
        int u=q.front();q.pop();
        for(int i=head[u];i;i=e[i].nxt){
            int v=e[i].to;
            if(e[i].f&&dep[v]==-1){
                dep[v]=dep[u]+1;cur[v]=head[v];
                if(v==T) return 1;
                q.push(v);
            }
        }
    }
    return 0;
}
int find(int u,int limit){
    if(u==T) return limit;
    int flow=0;
    for(int i=cur[u];i&&flow<limit;i=e[i].nxt){
        int v=e[i].to;cur[u]=i;
        if(dep[v]==dep[u]+1&&e[i].f){
            int t=find(v,min(e[i].f,limit-flow));
            if(!t) dep[v]=-1;
            e[i].f-=t;e[i^1].f+=t;flow+=t;
        }
    }
    return flow;
}
int Dinic(){
    int r=0;
    while(bfs()){
        int flow;
        while(flow=find(S,INF)) r+=flow;
    }
    return r;
}
int _;
vector<vector<pair<int,int> > > ans(N);
int solve(){
    cin>>n;if(!n) exit(0);
    cout<<"Case "<<++_<<": ";
    sum=0;tot=1;
    memset(head,0,sizeof(head));
    S=n+50001,T=S+1;
    cin>>m;
    n++;//因为时间段可能包含0，为了避免和猴子冲突，偏移一位。
    for(int i=0;i<50000;i++) add(i+n,T,m); //时间段 -> 汇点
    for(int i=1,v,a,b;i<n;i++){
        ans[i].clear();
        cin>>v>>a>>b;
        add(S,i,v);sum+=v; //源点 -> 猴子
        for(int j=a;j<b;j++) add(i,j+n,1); //猴子 -> 时间段
    }
    if(Dinic()!=sum) cout<<"No\n";
    else{
        cout<<"Yes\n";
        for(int i=1;i<n;i++){
            for(int j=head[i];j;j=e[j].nxt){
                if(e[j].to==S) continue;
                if(!e[j].f){
                    if(ans[i].size()&&ans[i].back().first==e[j].to-n+1) ans[i].back().first--; //原来有相邻区间，合并
                    else ans[i].push_back({e[j].to-n,e[j].to-n+1}); //否则加进去新的
                }
            }
            cout<<ans[i].size()<<' ';
            for(int j=ans[i].size()-1;j>=0;j--) cout<<"("<<ans[i][j].first<<","<<ans[i][j].second<<") ";
            cout<<'\n';
        }
    }
    return 0;
}
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    while(1) solve();
    return 0;
}
```