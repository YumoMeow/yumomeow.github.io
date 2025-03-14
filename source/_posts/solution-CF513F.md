---
title: 题解：CF513F - Scaygerboss
date: 2025-03-14 17:32:00
excerpt: 二分 网络流 最大流
categories: 
    - 题解
tags: 
    - 二分
    - 网络流
    - 最大流
---
### 题意
有 $a+b+1$ 个会动的棋子，在一个大小为 $n\times m$ 的棋盘上，棋盘上有一些点有障碍。棋子中，有 $a$ 个红色棋子，$b$ 个红色棋子，和 $1$ 个既能当作红棋子又能当作蓝棋子的通配棋子。每个棋子有自己的初始位置 $(x_i,y_i)$ 和速度 $t_i$，其中 $t_i$ 表示自己走到相邻的格子所需要的时间。

一个局面是好的，当且仅当对于每个格子，要么这个格子上没有棋子，要么这个格子上恰好存在一个红棋子与一个蓝棋子。你可以控制所有棋子的移动。请问最少多少时间后能达到处于一个好的局面。

### 题解
首先考虑如何消除通配棋子的影响。要满足条件红色棋子数量一定等于蓝色棋子数量，所以我们将通配棋子给少的一方，如果仍不相等则输出 `-1`。这样我们就将所有棋子划分到两个数量相等的集合。

因为棋子的任意移动我们不太好处理，考虑二分答案，用网络流求解。建立三列点，分别为红色棋子、蓝色棋子和网格上的每一个点，将网格上的点拆成入点和出点，连一条容量为 $1$ 的边满足每个格子上恰好两个棋子的限制。

从 $s$ 向红色棋子连边，蓝色棋子向 $t$ 连边。设当前答案为 $mid$，每次以每个棋子为起点 bfs 求出到其他所有点的距离，若 $\le mid$ 则可达，若是红色棋子则由棋子向入点连边，否则由出点向棋子连边，跑最大流即可。

每一个连接红色棋子、棋盘、蓝色棋子的路径表示一个格子上同时有两个棋子，提供 $1$ 的流量。若流量 $\ge \frac{a+b+1}{2}$ 则表示可以满足限制。需要卡常。

### 代码
```cpp
#include<iostream>
#include<cstring>
#include<queue>
#define int long long
#define reg register
#define il inline
using namespace std;
const int N=3010,M=2e6+10,INF=0x3f3f3f3f3f3f3f3f;
int n,m,a,b,r,c,t,dis[23][23],idx,hsh[23][23][2];
int S,T,cur[N],head[N],dep[N],tot;
int dx[]={0,1,0,-1},dy[]={1,0,-1,0};
char mp[50][50];
struct Edge{int to,nxt,f;}e[M];
struct Node{int x,y,t,id;}ma[510],fe[510];
il void add(int u,int v,int c){
    e[++tot]={v,head[u],c};head[u]=tot;
    e[++tot]={u,head[v],0};head[v]=tot;
}
il bool bfs(){
    queue<int> q;q.push(S);
    memset(dep,-1,sizeof(dep));
    dep[S]=0;cur[S]=head[S];
    while(q.size()){
        int u=q.front();q.pop();
        for(reg int i=head[u];i;i=e[i].nxt){
            int v=e[i].to;
            if(dep[v]==-1&&e[i].f){
                dep[v]=dep[u]+1;
                cur[v]=head[v];
                if(v==T) return 1;
                q.push(v);
            }
        }
    }
    return 0;
}
il int find(int u,int limit){
    if(u==T) return limit;
    int flow=0;
    for(reg int i=cur[u];i&&flow<limit;i=e[i].nxt){
        int v=e[i].to;cur[u]=i;
        if(dep[v]==dep[u]+1&&e[i].f){
            int t=find(v,min(e[i].f,limit-flow));
            if(!t) dep[v]=-1;
            e[i].f-=t;e[i^1].f+=t;flow+=t;
        }
    }
    return flow;
}
il int Dinic(){
    int r=0;
    while(bfs()){
        int flow;
        while(flow=find(S,INF)) r+=flow;
    }
    return r;
}
il void getdis(Node now,int limit,bool op){
    queue<pair<int,int> > q;
    memset(dis,0x3f,sizeof(dis));
    q.push({now.x,now.y});dis[now.x][now.y]=0;
    while(q.size()){
        int x=q.front().first,y=q.front().second;q.pop();
        if(dis[x][y]>limit) continue;
        if(!op) add(now.id,hsh[x][y][0],1);
        else add(hsh[x][y][1],now.id,1);//两种情况
        for(reg int i=0;i<4;i++){
            int tx=x+dx[i],ty=y+dy[i];
            if(tx<=n&&ty<=m&&tx>0&&ty>0&&dis[tx][ty]>dis[x][y]+now.t&&mp[tx][ty]!='#'){
                dis[tx][ty]=dis[x][y]+now.t;q.push({tx,ty});
            }
        }
    }
    return;
}
il bool check(int x){
    tot=1;memset(head,0,sizeof(head));
    for(reg int i=1;i<=a;i++) add(S,ma[i].id,1),getdis(ma[i],x,0);
    for(reg int i=1;i<=b;i++) add(fe[i].id,T,1),getdis(fe[i],x,1);//根据距离连边
    for(reg int i=1;i<=n;i++) for(reg int j=1;j<=m;j++) add(hsh[i][j][0],hsh[i][j][1],1);//入点和出点连边
    return Dinic()>=a;
}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>n>>m>>a>>b;
    for(reg int i=1;i<=n;i++)for(reg int j=1;j<=m;j++)cin>>mp[i][j];
    cin>>r>>c>>t;
    for(reg int i=1;i<=a;i++) cin>>ma[i].x>>ma[i].y>>ma[i].t,ma[i].id=++idx;
    for(reg int i=1;i<=b;i++) cin>>fe[i].x>>fe[i].y>>fe[i].t,fe[i].id=++idx;
    if(a<b) ma[++a].x=r,ma[a].y=c,ma[a].t=t,ma[a].id=++idx;
    else fe[++b].x=r,fe[b].y=c,fe[b].t=t,fe[b].id=++idx;
    for(reg int i=1;i<=n;i++)for(reg int j=1;j<=m;j++)for(reg int k=0;k<=1;k++) hsh[i][j][k]=++idx;
    S=++idx;T=++idx;
    if(a!=b){
        cout<<-1;
        return 0;
    }
    int l=0,ans=-1;r=1e12;
    while(l<=r){
        int mid=(l+r)>>1;
        if(check(mid)) r=mid-1,ans=mid;
        else l=mid+1;
    }
    cout<<ans;
    return 0;
}
```