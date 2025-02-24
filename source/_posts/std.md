---
title: 标程
date: 2025-02-24 20:20:00
hide: true
---
## 洛谷P1637
```cpp
#include<iostream>
#define int long long
using namespace std;
const int N=1e5+10;
int n,a[N];
struct BIT{
	int bit[N];
	int lowbit(int x){return x&-x;}
	void add(int x,int k){for(;x<N;x+=lowbit(x)) bit[x]+=k;}
	int query(int x,int res=0){for(;x>0;x-=lowbit(x)) res+=bit[x];return res;}
}l,r;
int ans;
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n;
	for(int i=1;i<=n;i++){
		cin>>a[i];
		r.add(a[i],1);
	}
	for(int i=1;i<=n;i++){
		r.add(a[i],-1);
		ans+=l.query(a[i]-1)*(r.query(N-1)-r.query(a[i]));
		l.add(a[i],1);
	}
	cout<<ans;
	return 0;
}
```
## 洛谷P7706
```cpp
#include<iostream>
#define int long long
#define ls (u<<1)
#define rs (u<<1|1)
#define mid ((l+r)>>1)
using namespace std;
const int N=5e5+10,INF=1e9;
int n,m,a[N],b[N];
struct Node{int psi,maA,miB,maL,maR;}tr[N<<2];
void pushup(Node &u,Node lc,Node rc){
	u.maA=max(lc.maA,rc.maA);
	u.miB=min(lc.miB,rc.miB);
	u.maL=max(max(lc.maL,rc.maL),lc.maA-rc.miB);
	u.maR=max(max(lc.maR,rc.maR),rc.maA-lc.miB);
	u.psi=max(max(lc.psi,rc.psi),max(lc.maL+rc.maA,lc.maA+rc.maR));
	return;
}
void build(int u,int l,int r){
	if(l==r){
		tr[u].maA=a[l];
		tr[u].miB=b[l];
		tr[u].maL=tr[u].maR=tr[u].psi=-INF;
		return;
	}
	build(ls,l,mid);
	build(rs,mid+1,r);
	pushup(tr[u],tr[ls],tr[rs]);
	return;
}
void change(int u,int l,int r,int U,int k,bool op){
	if(l==r){
		if(!op) tr[u].maA=k;
		else tr[u].miB=k;
		return;
	}
	if(U<=mid) change(ls,l,mid,U,k,op);
	else change(rs,mid+1,r,U,k,op);
	pushup(tr[u],tr[ls],tr[rs]);
}
Node query(int u,int l,int r,int L,int R){
	if(L<=l&&r<=R) return tr[u];
	if(L<=mid&&R>mid){
		Node res;
		pushup(res,query(ls,l,mid,L,R),query(rs,mid+1,r,L,R));
		return res;
	}
	else if(L<=mid) return query(ls,l,mid,L,R);
	else return query(rs,mid+1,r,L,R);
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<=n;i++) cin>>a[i];
	for(int i=1;i<=n;i++) cin>>b[i];
	build(1,1,n);
	while(m--){
		int op,x,y;
		cin>>op>>x>>y;
		if(op==1) change(1,1,n,x,y,0);
		if(op==2) change(1,1,n,x,y,1);
		if(op==3) cout<<query(1,1,n,x,y).psi<<'\n';
	}
	return 0;
}
```
## 洛谷P4146
```cpp
#include<iostream>
#define fa(x) tr[x].f
#define ls(x) tr[x].s[0]
#define rs(x) tr[x].s[1]
#define int long long
using namespace std;
const int N=5e4+10;
int n,m,idx,root;
struct Node{
	int s[2],f,ma,addtag,siz,key;
	bool revtag;
}tr[N];
int get(int x){return x==rs(fa(x));}
void pushup(int x){
	tr[x].siz=tr[ls(x)].siz+tr[rs(x)].siz+1;
	tr[x].ma=max(tr[x].key,max(tr[ls(x)].ma,tr[rs(x)].ma));
}
void rotate(int x){
    int y=fa(x),z=fa(y),c=get(x);
    if(tr[x].s[c^1]) fa(tr[x].s[c^1])=y;tr[y].s[c]=tr[x].s[c^1];
    fa(y)=x;tr[x].s[c^1]=y;
    if(z) tr[z].s[tr[z].s[1]==y]=x;fa(x)=z;
    pushup(x);pushup(y);
}
void splay(int x,int tgt){
	for(int y=fa(x);y=fa(x),y!=tgt;rotate(x)){
		if(fa(y)!=tgt) rotate(get(y)==get(x)?y:x);
	}
	if(!tgt) root=x;
}
void pushdown(int x){
	if(tr[x].addtag){
		int k=tr[x].addtag;
		if(ls(x))tr[ls(x)].ma+=k,tr[ls(x)].addtag+=k,tr[ls(x)].key+=k;
		if(rs(x))tr[rs(x)].ma+=k,tr[rs(x)].addtag+=k,tr[rs(x)].key+=k;
		tr[x].addtag=0;
	}
	if(tr[x].revtag){
		if(ls(x)) swap(ls(ls(x)),rs(ls(x))),tr[ls(x)].revtag^=1;
		if(rs(x)) swap(ls(rs(x)),rs(rs(x))),tr[rs(x)].revtag^=1;
		tr[x].revtag=0;		
	}
}
int find(int k){
	int now=root;k++;
	while(1){
		pushdown(now);
		int siz=tr[ls(now)].siz+1;
		if(k==siz) return now;
		else if(siz>k) now=ls(now);
		else now=rs(now),k-=siz;
	}
}
void add(int l,int r,int k){
	int x=find(l-1),y=find(r+1);
	splay(x,0);splay(y,x);
	tr[ls(y)].addtag+=k,tr[ls(y)].ma+=k,tr[ls(y)].key+=k;
	pushup(y),pushup(x);
}
void reverse(int l,int r){
	int x=find(l-1),y=find(r+1);
	splay(x,0);splay(y,x);
	tr[ls(y)].revtag^=1;
	swap(ls(ls(y)),rs(ls(y)));
	pushup(y);pushup(x);		
}
int query(int l,int r){
	int x=find(l-1),y=find(r+1);
	splay(x,0);splay(y,x);
	return tr[ls(y)].ma;	
}
int build(int l,int r,int f){
	if(l>r)return 0;
	int mid=(l+r)>>1;
	tr[mid].f=f;
	if(l==r){
		if(l==1||l==n+2) tr[l].ma=tr[l].key=-1e9;
		tr[l].siz=1;
		return l;
	}
	ls(mid)=build(l,mid-1,mid);
	rs(mid)=build(mid+1,r,mid);
	pushup(mid);
	return mid;
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	tr[0].ma=-1e9;
	root=build(1,n+2,0);
	while(m--){
		int op,l,r,v;
		cin>>op>>l>>r;
		if(op==1){
			cin>>v;
			add(l,r,v);
		}
		if(op==3) cout<<query(l,r)<<'\n';
		if(op==2) reverse(l,r);
	}
	return 0;
}
```
## 洛谷P3793
```cpp
#include<ctime>
#include<iostream>
#include<cmath>
namespace GenHelper{
    unsigned z1,z2,z3,z4,b;
    unsigned rand_(){
        b=((z1<<6)^z1)>>13;
        z1=((z1&4294967294U)<<18)^b;
        b=((z2<<2)^z2)>>27;
        z2=((z2&4294967288U)<<2)^b;
        b=((z3<<13)^z3)>>21;
        z3=((z3&4294967280U)<<7)^b;
        b=((z4<<3)^z4)>>12;
        z4=((z4&4294967168U)<<13)^b;
        return (z1^z2^z3^z4);
    }
}
void srand(unsigned x){using namespace GenHelper;z1=x; z2=(~x)^0x233333333U; z3=x^0x1234598766U; z4=(~x)+51;}
int read(){
    using namespace GenHelper;
    int a=rand_()&32767;
    int b=rand_()&32767;
    return a*32768+b;
}

using namespace std;
double START=clock();
const int N=2e7+10,M=5000;
int n,m,s,B,bel[N],L[M],R[M],a[N],cnt;
int st[M][14],bg[M][M],ed[M][M];
unsigned long long ans;
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>n>>m>>s;
    srand(s);
    B=sqrt(n);cnt=n/B;if(n%B) cnt++;
    for(int i=1;i<=n;i++){
        a[i]=read();
        bel[i]=(i-1)/B+1;
        st[bel[i]][0]=max(st[bel[i]][0],a[i]);
        bg[bel[i]][(i-1)%B+1]=max(bg[bel[i]][(i-1)%B],a[i]);
    }
    for(int i=1;i<=cnt;i++) L[i]=(i-1)*B+1,R[i]=min(n,i*B);
    for(int i=n;i>=1;i--){
        ed[bel[i]][(i-1)%B+1]=max(ed[bel[i]][(i-1)%B+2],a[i]);
    }
    for(int i=1;i<=13;i++){
        for(int j=1;j+(1<<i)-1<=cnt;j++){
            st[j][i]=max(st[j][i-1],st[j+(1<<(i-1))][i-1]);
        }
    }
    while(m--){
        int l=read()%n+1,r=read()%n+1;
        if(l>r) swap(l,r);
        int res=0;
        if(bel[l]==bel[r]){
            for(int i=l;i<=r;i++) res=max(res,a[i]);
            ans+=res;
            continue;
        }
        res=max(bg[bel[r]][(r-1)%B+1],ed[bel[l]][(l-1)%B+1]);
        int x=bel[l]+1,y=bel[r]-1;
        if(x<=y){
            int k=log2(y-x+1);
            res=max(res,max(st[x][k],st[y-(1<<k)+1][k]));    
        }
        ans+=res;   
    }
    cout<<ans;
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```