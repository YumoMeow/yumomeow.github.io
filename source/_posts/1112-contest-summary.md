---
title: 11.12 模拟赛总结
date: 2025-11-12 20:41:00
excerpt: mhx布置的模拟赛题。。
categories: 
    - 总结
tags: 
    - 数据结构
    - 动态规划
    - 图论
comment: 'utterances'
---

[file](/files/1112down.zip)

时间分配不合理，大量时间浪费在了 T1 又没仔细思考就写了个假的做法，打暴力也没打出来，后面几题又没好好思考，于是烂完了。

T1 题面很复杂，但是你仔细思考就会发现有很多隐藏的性质。比如每次加营养剂的时候必定加 $m-1$ 个，并且只要满足 $v+1$ 能到 $v$ 其余的都一定能满足。$v+1$ 到 $v$ 要分好几步（更换几次营养剂），显然每次应选走的最远的，这样断环为链去 dp 就行了。实现细节很多因为他是个环。做题时没仔细思考出来必须选 $m-1$ 个这一点，并且想了个很复杂但错误的做法导致浪费了很多时间。

```cpp
#include<iostream>
#include<vector>
#include<cstring>
using namespace std;
const int N=6e5+10;
int n,m,nxt[N],fa[N][20];
int solve(int l,int r){
    if(fa[l][19]<r) return -1;
    if(l==r) return 0;
    int res=0;
    for(int i=19;i>=0;i--){
        if(fa[l][i]<r){
            l=fa[l][i],res+=1<<i;
        }
    }
    return res+1;
}
int solve(){
    cin>>n>>m;
    for(int i=1;i<=3*n;i++) nxt[i]=i;
    for(int i=1;i<=m;i++){
        int cnt,now,fi;
        cin>>cnt>>now;
        fi=now;
        for(int j=2,x;j<=cnt;j++){
            cin>>x;
            nxt[now+1]=max(nxt[now+1],x);
            nxt[now+n+1]=max(nxt[now+n+1],x+n);
            nxt[now+2*n+1]=max(nxt[now+2*n+1],x+2*n);
            now=x;
        }
        nxt[now+1]=max(nxt[now+1],n+fi);
        nxt[n+now+1]=max(nxt[n+now+1],2*n+fi);
        nxt[2*n+now+1]=max(nxt[2*n+now+1],3*n);
    }
    int ma=0;
    for(int i=1;i<=3*n;i++){
        ma=max(ma,nxt[i]);
        fa[i][0]=ma;
    }
    for(int j=1;j<20;j++){
        for(int i=1;i<=3*n;i++){
            fa[i][j]=fa[fa[i][j-1]][j-1];
        }
    }
    for(int i=1;i<=n;i++)cout<<solve(n+i+1,n*2+i)<<' ';
    cout<<'\n';
    return 0;
}
int main(){
    freopen("cycle.in","r",stdin);
    freopen("cycle.out","w",stdout);
    int T;cin>>T;//T=1;
    while(T--) solve();
    return 0;
}
```

T3 比 T2 略简单。这种题看起来就像换根 dp，于是先求 1 作为根的答案再想办法转移。容易发现这个答案很好求，因为它给你的形式决定了只要往下走一步答案就除以二（右移一位）。这里有一种比较独特的记录答案方式，就是记录所有答案之和的第 $2^i$ 位有几个 $1$。最后只要把每一位乘上 $2^i$ 就是最终答案。这样，你先求一个 $f_{i,j}$ 表示 1 为根时，$i$ 为根的子树答案之和的第 $j$ 位有几个 $1$。这个挺好求的，就是根据式子求出来 $i$ 本身的答案再加上所有儿子的答案。

这样在换根时就有一个很大的好处。我们设 $g_{i,j}$ 表示这棵树以 $i$ 为根的答案，第 $j$ 位有几个 $1$，初始 $g_1$ 就和 $f_1$ 一样。

你考虑从一个点 $u$ 向他儿子 $v$ 转移。分为两部分，$v$ 子树内与 $v$ 子树外。$v$ 子树内的答案就是 $f_v$，至于子树外的答案，你需要先求出来那一部分在 $u$ 时候的答案，再把他们都左移一位（因为距离多了 $1$），把这两部分加起来就行。

```cpp
for(int i=0;i<=30;i++) g[u][i]=g[fa][i]-f[u][i+1];//外边
for(int i=0;i<=30;i++) g[u][i]=g[u][i+1]+f[u][i];//左移一位后加上里边
//这里是 fa 向 u 的转移，其实差不多
```

最后统计答案即可。

```cpp
#include<iostream>
#include<vector>
using namespace std;
const int N=3e5+10;
int n,a[N],f[N][35],g[N][35];
vector<int> G[N];
void dfs(int u,int fa){
    for(int i=0;i<=30;i++)
        f[u][i]=((a[u]&(1<<i))!=0);
    for(auto v:G[u]){
        if(v==fa) continue;
        dfs(v,u);
        for(int i=0;i<=30;i++)
            f[u][i]+=f[v][i+1];
    }
}
void work(int u,int fa){
    if(u!=1){
        for(int i=0;i<=30;i++) g[u][i]=g[fa][i]-f[u][i+1];
        for(int i=0;i<=30;i++) g[u][i]=g[u][i+1]+f[u][i];
    }
    for(auto v:G[u]) if(v!=fa) work(v,u);
}
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    freopen("sora.in","r",stdin);
    freopen("sora.out","w",stdout);
    cin>>n;
    for(int i=1;i<=n;i++) cin>>a[i];
    for(int i=1;i<n;i++){
        int u,v;
        cin>>u>>v;
        G[u].push_back(v);
        G[v].push_back(u);
    }
    dfs(1,0);
    for(int i=0;i<=30;i++) g[1][i]=f[1][i];
    work(1,0);
    for(int i=1;i<=n;i++){
        long long ans=0;
        for(int j=0;j<=30;j++) ans+=(1ll<<j)*g[i][j];
        cout<<ans<<' ';
    }
    return 0;
}
```

别的暂时还没补。