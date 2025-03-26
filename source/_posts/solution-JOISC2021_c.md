---
title: 题解：JOISC2021C - フードコート
date: 2025-02-25 19:47:00
excerpt: 扫描线 数据结构 线段树
categories: 
    - 题解
tags: 
    - 扫描线
    - 数据结构
    - 线段树
---

### 题意
有 $n$ 个队列，要求支持以下三种操作：
- 在区间 $[l,r]$ 中每个队列的队尾插入 $k$ 个 $c$。
- 取出区间 $[l,r]$ 中每个队列的前 $k$ 个数，不足则清空队列。
- 输出指定队列从队头开始的第 $b$ 项，如果大小不足 $b$ 则输出 $0$。

### 题解
修改的操作不方便维护，考虑**换维扫描线**。

以队列为横轴，时间为纵轴，从左往右扫描，用线段树维护队列每个时刻的人数。这样修改操作就可以转化为差分，即在 $l$ 处增加 $k$ 个人，在 $r+1$ 处减去 $k$ 个人。

但是这样有一个问题，删除操作时队列的人数需要对 $0$ 取 $max$，这个不好直接维护。我们先忽略它，看如果队列人数始终 $\ge 0$ 时怎么做。将加操作与减操作分别求和，则每个位置上的值可以转化为 $\sum add-\sum del$。每次询问 $b$ 时，二分查找第一个 $\sum add\ge b+\sum del$ 的数的位置，返回它对应的数即可。

如果我们可以得知在询问前最后一个队列长为 $0$ 的时刻，就可以使用上述做法来计算。仅当 $\sum add-\sum del\le 0$ 时队列才会被清空，且显然这个值必然随着每次清空越来越小。于是我们只需要维护出序列中的前缀和最小值，它的位置即是上次被清零的位置，由于求了前缀和，单点加需要改为后缀加。

总结一下，设每次查询时的时间为 $t$，我们要先找到队列上次被清空的时间，即区间 $[1,t)$ 的前缀和最小值出现的时间。设它为 $p$。

这样，我们就可以在区间 $(p,t)$ 上套用前文的算法，在线段树上二分查找出 $\sum add\ge b+\sum del$ 的最小位置。其中的 $\sum del$ 可以转化为 $\sum sum-\sum add$。

线段树上需要维护的信息：考虑所有操作时人数的前缀最小值 $mi$、它出现的位置 $posmi$、只考虑加操作的和 $add$，考虑所有操作的和 $sum$。时间复杂度 $O(q \log q)$。

### 代码
```cpp
#include<iostream>
#include<algorithm>
#define int long long
#define ls (u<<1)
#define rs (u<<1|1)
#define mid ((l+r)>>1)
using namespace std;
const int N=5e5+10;
int n,m,q,c[N],cntop,cntqu,ans[N];
struct Op{
    int tim,pos,k;//什么时间，哪个队列，加减多少
    bool op;//加还是减
    bool operator < (const Op &t) const{
        return pos<t.pos;
    }
}op[N];
struct Query{
    int id,tim,pos,b;//第几个询问，什么时间，哪个队列，B
    bool operator < (const Query &t) const{
        return pos<t.pos;
    }    
}qu[N];
struct Node{
    int sum,add;//操作1-操作2，操作1
    int mi,posmi,tag;//前缀最小值，前缀最小值位置，后缀加标记
}tr[N<<2];
void pushup(Node &u,Node lc,Node rc){
    u.mi=min(lc.mi,rc.mi);
    u.posmi=lc.mi<=rc.mi?lc.posmi:rc.posmi;
    u.sum=lc.sum+rc.sum;
    u.add=lc.add+rc.add;
    return;
}
void build(int u,int l,int r){
    if(l==r){
        tr[u].posmi=l;
        return;
    }
    build(ls,l,mid);
    build(rs,mid+1,r);
}
void updadd(int u,int k){
    tr[u].mi+=k;
    tr[u].tag+=k;
    return;
}
void pushdown(int u){
    updadd(ls,tr[u].tag);
    updadd(rs,tr[u].tag);
    tr[u].tag=0;
    return;
}
void add(int u,int l,int r,int L,int R,int k){
    //后缀加
    if(L<=l&&r<=R){
        updadd(u,k);
        return;
    }
    pushdown(u);
    if(L<=mid) add(ls,l,mid,L,R,k);
    if(R>mid) add(rs,mid+1,r,L,R,k);
    pushup(tr[u],tr[ls],tr[rs]);
}
void add(int u,int l,int r,int U,int k,bool op){
    //单点加
    if(l==r){
        tr[u].sum+=k;
        if(op) tr[u].add+=k;//仅当现在是加操作，计算加操作的和
        return;
    }
    pushdown(u);
    if(U<=mid) add(ls,l,mid,U,k,op);
    else add(rs,mid+1,r,U,k,op);
    pushup(tr[u],tr[ls],tr[rs]);
}
Node query(int u,int l,int r,int L,int R){
    if(L<=l&&r<=R) return tr[u];
    pushdown(u);
    if(L<=mid&&R>mid){
        Node res;
        pushup(res,query(ls,l,mid,L,R),query(rs,mid+1,r,L,R));
        return res;
    }
    else if(L<=mid) return query(ls,l,mid,L,R);
    else return query(rs,mid+1,r,L,R);
}
int find(int u,int l,int r,int U,int &v){
    if(r<U) return 0;//跑出边界
    pushdown(u);
    if(l>=U){
        if(tr[u].add<v){
            v-=tr[u].add;
            return 0;
        }
        if(l==r) return l;
    }
    int res=find(ls,l,mid,U,v);//左边找不到找右边
    if(!res) res=find(rs,mid+1,r,U,v);
    return res;
}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>n>>m>>q;
    for(int i=1;i<=q;i++){
        int opt;
        cin>>opt;
        if(opt==1){
            int l,r,k;
            cin>>l>>r>>c[i]>>k;
            op[++cntop]=(Op){i,l,k,1};
            op[++cntop]=(Op){i,r+1,-k,1};
        }
        if(opt==2){
            int l,r,k;
            cin>>l>>r>>k;
            op[++cntop]=(Op){i,l,-k,0};
            op[++cntop]=(Op){i,r+1,k,0};
        }
        if(opt==3){
            int a,b;
            cin>>a>>b;cntqu++;
            qu[cntqu]=(Query){cntqu,i,a,b};
        }
        ans[i]=m+1;
    }
    sort(op+1,op+cntop+1);
    sort(qu+1,qu+cntqu+1);
    build(1,1,q);
    //i 当前处理的队列 j 当前处理的操作 k 当前处理的询问
    for(int i=1,j=1,k=1;i<=n;i++){
        while(j<=cntop&&op[j].pos==i){
            //先操作完当前队列
            add(1,1,q,op[j].tim,q,op[j].k);//后缀加
            add(1,1,q,op[j].tim,op[j].k,op[j].op);//单点加/减
            j++;
        }
        while(k<=cntqu&&qu[k].pos==i){
            //处理当前队列的询问
            if(qu[k].tim==1){ans[qu[k++].id]=0;continue;}//特判询问1
            Node t=query(1,1,q,1,qu[k].tim-1);//查询前缀最小值
            int p=t.mi>=0?0:t.posmi;//大于0代表没清空过，否则是最小值位置
            t=query(1,1,q,p+1,qu[k].tim);
            int val=qu[k].b+(t.add-t.sum);
            int pos=find(1,1,q,p+1,val);//二分找时刻p右边>=b+del的第一个时刻
            //因为我们已经在b上减去了删掉的人，因而只需要在add上面找
            //又因为add单调不减，所以可以二分。
            ans[qu[k].id]=(!pos||pos>qu[k].tim)?0:c[pos];
            k++;
        }
    }
    for(int i=1;i<=cntqu;i++) cout<<ans[i]<<'\n';
    return 0;
}
```