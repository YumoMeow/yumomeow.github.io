---
title: 题解：UVA11294 - Wedding
date: 2025-03-27 16:40:00
excerpt: 图论 2-SAT 强连通分量
comment: 'utterances'
categories: 
    - 题解
tags: 
    - 图论
    - 2-SAT
    - 强连通分量
---
### 题意
有至多 $30$ 对夫妻将会参加一个婚宴。他们将会坐在一个长桌子的两边。

新郎新娘坐在彼此相对的一端，新娘只能看到对面的人，夫妻不能坐在同一边。这些人中还有人是通奸关系（同性恋或者非同性恋都有可能），新娘不能同时看到任何一对。问能否满足要求。
### 题解
~~贵圈真乱~~

我们考虑所有的通奸关系。若 A 与 B 有通奸关系（不能坐在同一边），**那么确定了 A 之后 B 的配偶必须与他坐在同一边，确定 B 之后 A 的配偶必须与他坐同一边**，这符合 2-SAT 的性质。从 A 向 B 的配偶连边，从 B 向 A 的配偶连边即可。注意新郎与新娘之间也需要连一条边，表示他俩必须相对。

记第 $i$ 对夫妻中妻子为 $2i$，丈夫为 $2i+1$，跑一遍 2-SAT 输出方案即可。因为题目中给出的范围是 $0$ 到 $n-1$，我们都往右偏移一位。
### 代码
```cpp
#include<iostream>
#include<stack>
#include<cstring>
using namespace std;
const int N=2010;
int n,m,head[N],dfn[N],low[N],bel[N],tot,cnt;
bool vis[N];
stack<int> s;
struct Edge{int to,nxt;}e[N];
void add(int u,int v){e[++tot]={v,head[u]};head[u]=tot;}
void tarjan(int u){
    dfn[u]=low[u]=++tot;
    s.push(u);vis[u]=1;
    for(int i=head[u];i;i=e[i].nxt){
        int v=e[i].to;
        if(!dfn[v]) tarjan(v),low[u]=min(low[u],low[v]);
        else if(vis[v]) low[u]=min(low[u],dfn[v]);
    }
    if(low[u]==dfn[u]){
        cnt++;
        while(1){
            int v=s.top();s.pop();
            vis[v]=0,bel[v]=cnt;
            if(u==v) return;
        }
    }
}
int solve(){
    tot=cnt=0;
    memset(vis,0,sizeof(vis));
    memset(head,0,sizeof(head));
    memset(dfn,0,sizeof(dfn));
    memset(low,0,sizeof(low));
    while(s.size()) s.pop();
    cin>>n>>m;
    if(n==0&&m==0) exit(0);
    while(m--){
        int a,c;char b,d;
        cin>>a>>b>>c>>d;
        int x=2*(a+1)+(b=='h'),y=2*(c+1)+(d=='h');
        add(x,y^1);add(y,x^1);
    }
    add(2,3);
    tot=0;
    for(int i=2;i<=2*n+1;i++) if(!dfn[i]) tarjan(i);
    for(int i=1;i<=n;i++){
        if(bel[i*2]==bel[i*2+1]){
            cout<<"bad luck\n";
            return 0;
        } 
    }
    for(int i=2;i<=n;i++){
        cout<<i-1;
        if(bel[i*2]<bel[i*2+1]) cout<<"h ";
        else cout<<"w ";
    }
    cout<<'\n';
    return 0;
}
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    while(1) solve(); 
    return 0;
}
```