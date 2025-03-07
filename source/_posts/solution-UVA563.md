---
title: 题解：UVA563 - Crimewave
date: 2025-03-07 09:44:00
excerpt: 网络流 最大流
categories: 
    - 题解
tags: 
    - 网络流
    - 最大流
---

### 题意
给定 $s\times a$ 的网格和 $b$ 个点，询问有没有可能找到 $b$ 条不相交的路径满足从每个点通向网格的边缘。

### 题解
注意到数据范围 $s,a\le 50$ 和每个点经过一次的限制，很容易想到网络流拆点。

具体地，建立源点和汇点 $s,t$，将每个点拆成入点和出点。我们要连三类边：
- 对于每个点，在入点和出点之间连一条容量为 $1$ 的边，这样就满足了每个点经过一次的限制。
- 在源点和每个 $b_i$ 间连一条容量为 $1$ 的边表示需要走的路径。
- 网格上的每个点可以通向周围的四个点，所以将每个出点向相邻的入点连一条边，如果相邻点在网格外则直接连向汇点，表示可以走出去。

建完图跑最大流即可，如果最大流 $<b$ 则不能满足条件，反之可以。

注意多测要清空，不懂看代码注释。

### 代码
```cpp
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;
const int N=5010,INF=1e9;//需要开 s*a*2 个点
int s,a,b,head[N],cur[N],dep[N],tot,S,T;
int dx[]={0,1,0,-1},dy[]={1,0,-1,0};
struct Edge{int to,nxt,f;}e[100010];
int hsh(int u,int v,int c){
    return (u-1)*a+v+c*s*a;
    //将网格上点 (u,v) 转化成一个数方便建图，c 表示入点还是出点
}
void add(int u,int v,int c){
    e[++tot]={v,head[u],c};head[u]=tot;
    e[++tot]={u,head[v],0};head[v]=tot;
}
bool bfs(){
    memset(dep,-1,sizeof(dep));
    queue<int> q;
    q.push(S);dep[S]=0;cur[S]=head[S];
    while(q.size()){
        int u=q.front();q.pop();
        for(int i=head[u];i;i=e[i].nxt){
            int v=e[i].to;
            if(e[i].f&&dep[v]==-1){
                cur[v]=head[v];dep[v]=dep[u]+1;
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
        if(e[i].f&&dep[v]==dep[u]+1){
            int t=find(v,min(e[i].f,limit-flow));
            if(!t) dep[v]=-1;
            e[i].f-=t;e[i^1].f+=t;flow+=t;
        }
    }
    return flow;
}
int solve(){
    tot=1;memset(head,0,sizeof(head));//记得清空
    cin>>s>>a>>b;
    S=s*a*2+1,T=S+1;
    for(int i=1,x,y;i<=b;i++){
        cin>>x>>y;
        add(S,hsh(x,y,0),1);//源点 -> b_i
    }
    for(int i=1;i<=s;i++){
        for(int j=1;j<=a;j++){
            add(hsh(i,j,0),hsh(i,j,1),1);//入点 -> 出点
            for(int k=0;k<4;k++){
                int tx=i+dx[k],ty=j+dy[k];
                //网格外，直接连向汇点
                if(tx==0||ty==0||tx>s||ty>a) add(hsh(i,j,1),T,1);
                //网格内，连向相邻点
                else add(hsh(i,j,1),hsh(tx,ty,0),1);
            }
        }
    }
    //Dinic
    int r=0;
    while(bfs()){
        int flow;
        while(flow=find(S,INF)) r+=flow;
    }
    if(r==b) cout<<"possible\n";
    else cout<<"not possible\n";
    return 0;
}
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);	
    int T;
    cin>>T;
    while(T--) solve();
    return 0;
}
```