---
title: 题解：UVA1515 - Pool construction
date: 2025-03-07 15:20:00
comment: 'utterances'
excerpt: 图论 网络流 最小割
categories: 
    - 题解
tags: 
    - 图论
    - 网络流
    - 最小割
---

### 题意
给定一个 $n\times m$ 的矩阵，每个格子是草地 `#` 或水洼 `.`，有以下修改方法或要求：
- 将草地变成水洼，花费 $d$ 代价。
- 将水洼变成草地，花费 $f$ 代价。
- 若两个相邻格子字符不同，花费 $b$ 代价。
- 矩阵边缘必须全为草地。

求最小代价。

### 题解
看到这么几种限制和 $n,m\le 50$ 的数据范围，~~很容易~~想到用网络流求解。

可以转化为一个最小割模型，建立源点 $s$ 与汇点 $t$，将每个点与 $s$ 和 $t$ 分别连一条边，边权分别为变成草地和水洼的代价。这样，我们就可以通过割掉一些边满足**一个点只能是草地或水洼中的一个**，即要么连向 $s$，要么连向 $t$，此时 $s$ 与 $t$ 必定不连通，发现满足限制需要花费的代价就是最小割。

在具体连边时，我们分以下几种情况，设当前点为 $x$，$(u,v,c)$ 表示一条从 $u$ 指向 $v$ 容量为 $c$ 的边：
- 若点在矩阵边缘，则它必须为草地。
    - 若本来就是草地，连边 $(s,x,INF)$，表示不可变成水洼。连边 $(x,t,0)$，表示变草地不需要代价。
    - 若本来是水洼，连边 $(s,x,INF)$ 和 $(x,t,f)$，表示必须花费 $f$ 变成草地。
- 若点不在矩阵边缘。
    - 若本来是草地，连边 $(s,x,d)$ 与 $(x,t,0)$，表示变成水洼代价为 $d$。
    - 若本来是水洼，连边 $(s,x,0)$ 与 $(x,t,f)$，表示变成草地代价为 $f$。

接下来考虑另外一个限制：相邻两个格子若字符不同则需花费 $b$ 代价。

我们发现，直接在所有相邻格子之间连一条容量为 $b$ 的边即可。因为若颜色相同，则两点都只有与 $s$ 或 $t$ 其中一个的连边，代价为 $b$ 的边即使不割掉也不会造成 $s$ 与 $t$ 联通。若颜色不同，则两点必定其中一个连向 $s$，一个连向 $t$，此时若不割掉会造成在此处 $s$ 与 $t$ 联通。看不懂可以画图理解。

实现中容量为 $0$ 的边不用连，注意**先读入列数再读入行数**。

### 代码
```cpp
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;
const int N=5010,INF=1e9;
int n,m,d,f,b,head[N],cur[N],dep[N],tot,S,T;
int dx[]={0,1,0,-1},dy[]={1,0,-1,0};
struct Edge{int to,nxt,f;}e[50010];
void add(int u,int v,int c){
    e[++tot]={v,head[u],c};head[u]=tot;
    e[++tot]={u,head[v],0};head[v]=tot;
}
int hsh(int a,int b){return (a-1)*m+b;}
bool bfs(){
    queue<int> q;memset(dep,-1,sizeof(dep));
    q.push(S);dep[S]=0;cur[S]=head[S];
    while(q.size()){
        int u=q.front();q.pop();
        for(int i=head[u];i;i=e[i].nxt){
            int v=e[i].to;
            if(dep[v]==-1&&e[i].f){
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
        if(dep[v]==dep[u]+1&&e[i].f){
            int t=find(v,min(e[i].f,limit-flow));
            if(!t) dep[v]=-1;
            e[i].f-=t;e[i^1].f+=t;flow+=t;
        }
    }
    return flow;
}
int solve(){
    tot=1;memset(head,0,sizeof(head));
    cin>>m>>n>>d>>f>>b;
    S=n*m+1,T=S+1;
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            char x;
            cin>>x;
            if(i==1||j==1||i==n||j==m){
                //边框
                if(x=='#') add(S,hsh(i,j),INF);
                else add(S,hsh(i,j),INF),add(hsh(i,j),T,f);
            }
            else{
                //非边框
                if(x=='#') add(S,hsh(i,j),d);
                else add(hsh(i,j),T,f);
            }
            //相邻格子
            for(int k=0;k<4;k++){
                int tx=i+dx[k],ty=j+dy[k];
                if(tx==0||ty==0||tx>n||ty>m) continue;
                add(hsh(i,j),hsh(tx,ty),b);
            }
        }
    }
    int r=0;
    while(bfs()){
        int flow;
        while(flow=find(S,INF)) r+=flow;
    }
    cout<<r<<'\n';
    return 0;
}
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0),cout.tie(0);
    int T;cin>>T;
    while(T--) solve();
    return 0;
}
```