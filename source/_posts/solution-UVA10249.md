---
title: 题解：UVA10249 - The Grand Dinner
date: 2025-03-07 08:03:00
excerpt: 图论 网络流 最大流
categories: 
    - 题解
tags: 
    - 图论
    - 网络流
    - 最大流
---

### 题意
有 $m$ 个代表团，$n$ 张桌子，代表团人数与桌子大小有可能不同，把每一位代表安排到一张桌子上，问有没有方案使同一张桌子上没有两个人来自相同的代表团，输出方案。

### 题解
网络流。建立源点 $s$ 与汇点 $t$，$s$ 向每个代表团连一条容量为人数的边，每个代表团向每张桌子连一条容量为 $1$ 的边表示只能向同一个桌子派一个人，每张桌子向 $t$ 连一条容量为桌子大小的边。这样我们就同时满足了代表团人数、桌子大小和桌上同一个代表团只能有一个人的限制。跑最大流即可。

考虑如何统计答案。若最大流小于总人数表示无法被分配，无解。否则，在残量网络上枚举所有表示代表团的点，对每个点枚举出边。若出边容量为 $0$ 表示这条代表团和桌子的连边被使用，输出桌子编号即可。

注意多测要重置的数组与变量，不懂看代码注释。

### 代码
```cpp
#include<ctime>
#include<iostream>
#include<queue>
#include<cstring>
#define int long long
using namespace std;
double START=clock();
const int N=1000,INF=1e9;
int n,m,s,t,sum,tot,head[N],dep[N],cur[N];
struct Edge{
    int to,nxt,f;
}e[100000];
void addedge(int u,int v,int c){
    e[++tot]={v,head[u],c};head[u]=tot;
    e[++tot]={u,head[v],0};head[v]=tot;
}
bool bfs(){
    queue<int> q;
    memset(dep,-1,sizeof(dep));
    q.push(s);
    dep[s]=0;cur[s]=head[s];
    while(q.size()){
        int u=q.front();q.pop();
        for(int i=head[u];i;i=e[i].nxt){
            int v=e[i].to;
            if(dep[v]==-1&&e[i].f){
                dep[v]=dep[u]+1;cur[v]=head[v];
                if(v==t) return 1;
                q.push(v);
            }
        }
    }
    return 0;
}
int find(int u,int limit){
    if(u==t) return limit;
    int flow=0;
    for(int i=cur[u];i&&flow<limit;i=e[i].nxt){
        cur[u]=i;
        int v=e[i].to;
        if(dep[v]==dep[u]+1&&e[i].f){
            int t=find(v,min(e[i].f,limit-flow));
            if(!t) dep[v]=-1;
            flow+=t;
            e[i].f-=t;e[i^1].f+=t;
        }
    }
    return flow;
}
int solve(){
	if(n==0) exit(0);
    tot=1;sum=0;memset(head,0,sizeof(head));//记得清空
    s=n+m+1,t=n+m+2;
    for(int i=1,r;i<=m;i++){
        cin>>r;sum+=r;
        addedge(s,i,r);//s -> 代表团
        for(int j=m+1;j<=n+m;j++) addedge(i,j,1);//代表团 -> 桌子 
    }
    for(int i=m+1,c;i<=n+m;i++){
        cin>>c;
        addedge(i,t,c);//桌子 -> 汇点
    }
    //Dinic
    int r=0;
    while(bfs()){
        int flow;
        while(flow=find(s,INF)) r+=flow;
    }
    if(r<sum){
        cout<<"0\n";
        return 0;
    }//无解
    cout<<"1\n";
    for(int i=1;i<=m;i++){
        for(int j=head[i];j;j=e[j].nxt){
            if(e[j].to>m&&e[j].f==0) cout<<e[j].to-m<<' ';
        }
        cout<<'\n';
    }	
    return 0;
}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);	
	while(cin>>m>>n) solve();
	return 0;
}
```