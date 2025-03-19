---
title: 标程
date: 2025-02-24 20:20:00
hide: true
---
## 0227A
```cpp
#include<iostream>
#define int long long
using namespace std;
int n,a[5010],ans,b[2000010];
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n;
	for(int i=1;i<=n;i++) cin>>a[i];
	for(int i=1;i<=n;i++){
		for(int j=i+1;j<=n;j++){
			ans+=b[a[i]^a[j]];//先算后面的
		}
		for(int j=1;j<i;j++) b[a[i]^a[j]]++;//再加前面的
	}
	cout<<ans;
	return 0;
}
```
## 0227B
```cpp
#include<iostream>
#include<cmath>
#include<queue>
#include<cstring>
#define int long long
#define pii pair<int,int>
using namespace std;
const int N=5e6+10;
int n,m,s,t,tot,p,head[N],dis[N];
bool vis[N];
int hsh(pii a){return a.first+a.second*n;}//映射
struct Edge{pii to;int w,nxt;bool fl;}e[N];
void addedge(pii u,pii v,int w,bool fl){
	e[++tot].nxt=head[hsh(u)];
	e[tot].to=v;
	e[tot].w=w;e[tot].fl=fl;
	head[hsh(u)]=tot;
}
struct Node{
	pii u;int dis;
	bool operator < (const Node &t) const{
		return dis>t.dis;
	}
};
priority_queue<Node> q;
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m>>s>>t;
	for(int i=1,a,b,d;i<=m;i++){
		char ch;
		cin>>a>>b>>d>>ch;
		addedge({a,0},{b,0},d,ch=='G'?1:0);
	}
	for(int i=1;i<=20;i++){
		for(int j=1;j<=n;j++){
			for(int k=head[hsh({j,0})];k;k=e[k].nxt){
				if(e[k].fl) addedge({j,i},{e[k].to.first,i},(int)ceil(1.0*e[k].w/pow(2,i)),1);//平整
				else addedge({j,i},{e[k].to.first,0},(int)ceil(1.0*e[k].w/pow(2,i)),1);//坑
			}
		}
	}
	cin>>p;
	for(int i=1,x,c;i<=p;i++){
		cin>>x>>c;
		for(int j=1;j<=20;j++) addedge({x,j-1},{x,j},c,1);//修理站建边
	}
	for(int i=1;i<=n;i++){
		for(int j=0;j<=20;j++) dis[hsh({i,j})]=0x3f3f3f3f;
	}
	dis[hsh({s,0})]=0;
	q.push({{s,0},0});
	while(q.size()){
		int u=hsh(q.top().u);
		q.pop();
		if(vis[u]) continue;
		vis[u]=1;
		for(int i=head[u];i;i=e[i].nxt){
			pii v=e[i].to;
			if(dis[hsh(v)]>dis[u]+e[i].w){
				dis[hsh(v)]=dis[u]+e[i].w;
				if(!vis[hsh(v)]){
					q.push({v,dis[hsh(v)]});
				}
			}
		}
	}	
	int ans=0x3f3f3f3f;
	for(int i=0;i<=20;i++) ans=min(ans,dis[hsh({t,i})]);
	if(ans==0x3f3f3f3f) cout<<"-1";
	else cout<<ans;
	return 0;
}
```
## 0227C
```cpp
#include<iostream>
#define int long long
using namespace std;
const int MOD=19260817;
int m,f[110][110][10010],h[110][10010],w[110][10010];
int dx[]={2,2,1,1,-1,-1,-2,-2},dy[]={1,-1,2,-2,2,-2,1,-1};
int tx[]={2,2,-2,-2},ty[]={2,-2,2,-2};
int op,a,b,c,d,e;
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
    freopen("chess.in","r",stdin);
    freopen("chess.out","w",stdout);
	cin>>m;
	while(m--){
		cin>>op>>a>>b>>c>>d>>e;
		if(e>10000){//只有车
			a=a+b-2;b=e;int ans=1;
			while(b){
				if(b&1) ans=ans*a%MOD;
				a=a*a%MOD;
				b>>=1;
			}
			cout<<ans<<'\n';
			continue;
		}
		f[c][d][0]=1;
		h[c][0]++,w[d][0]++;
		for(int i=1;i<=e;i++){
			for(int j=1;j<=a;j++){
				for(int k=1;k<=b;k++){
					if(op==0){//车
						f[j][k][i]=(h[j][i-1]+w[k][i-1]%MOD-2*f[j][k][i-1]%MOD+MOD)%MOD;
						h[j][i]+=f[j][k][i],w[k][i]+=f[j][k][i];
						h[j][i]%=MOD,w[k][i]%=MOD;						
					}
					else if(op==1){//马
						for(int l=0;l<8;l++){
							if(j+dx[l]<=0||j+dx[l]>a||k+dy[l]<=0||k+dy[l]>b) continue;
							f[j][k][i]+=f[j+dx[l]][k+dy[l]][i-1];
							f[j][k][i]%=MOD;
						}						
					}
					else{//象
						for(int l=0;l<4;l++){
							if(j+tx[l]<=0||j+tx[l]>a||k+ty[l]<=0||k+ty[l]>b) continue;
							f[j][k][i]+=f[j+tx[l]][k+ty[l]][i-1];
							f[j][k][i]%=MOD;
						}
					}
				}
			}
		}
		int ans=0;
		for(int i=1;i<=a;i++){
			for(int j=1;j<=b;j++){
				ans+=f[i][j][e];
				ans%=MOD;
			}
		}
		cout<<ans<<'\n';		
		for(int i=1;i<=a;i++){
			for(int j=1;j<=b;j++){
				for(int k=0;k<=e;k++){
					f[i][j][k]=0;
					h[i][k]=0,w[j][k]=0;
				}
			}
		}
	}
	return 0;
}
```
## 0227D
```cpp
#include<iostream>
#include<cmath>
#define int long long
using namespace std;
const int N=2e5+10,MOD=1e9+7;
int n,head[N],tot,ans[N],a[N],b[N],fa[N][20],lg[N],dep[N];
struct Edge{int to,nxt;}e[N];
void addedge(int u,int v){
	e[++tot].nxt=head[u];
	e[tot].to=v;
	head[u]=tot;
}
void dfs(int u){
	dep[u]=dep[fa[u][0]]+1;
	for(int i=1;i<=lg[dep[u]];i++){
		fa[u][i]=fa[fa[u][i-1]][i-1];
	}//预处理，lca要用
	for(int i=head[u];i;i=e[i].nxt){
		if(e[i].to!=fa[u][0]){
			fa[e[i].to][0]=u;
			dfs(e[i].to);
			ans[u]+=ans[e[i].to];//求答案
			ans[u]%=MOD;
		}
	}
}
int lca(int u,int v){
	if(dep[u]<dep[v]) swap(u,v);
	while(dep[u]>dep[v]) u=fa[u][lg[dep[u]-dep[v]]];
	if(u==v) return u;
	for(int i=lg[dep[u]];i>=0;i--){
		if(fa[u][i]!=fa[v][i]) u=fa[u][i],v=fa[v][i];
	}
	return fa[u][0];
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	lg[0]=-1;
	cin>>n;
	for(int i=1;i<n;i++){
		int u,v;
		cin>>u>>v;
		addedge(u,v);
		addedge(v,u);
	}
	for(int i=1;i<=n;i++){
		lg[i]=lg[i>>1]+1;
		cin>>a[i]>>b[i];
	}
	dfs(1);
	for(int i=1;i<=n;i++){
		for(int j=i+1;j<=n;j++){
			ans[lca(i,j)]+=2*min(abs(a[i]-a[j]),abs(b[i]-b[j]));
			ans[lca(i,j)]%=MOD;//对每个点对，在lca加上贡献。乘二是因为 (x,y) 与 (y,x) 各算一次贡献。
		}
	}
	dfs(1);
	for(int i=1;i<=n;i++) cout<<ans[i]<<'\n';
	return 0;
}
```
## LuoguP1637
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
## LuoguP7706
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
## LuoguP4146
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
## LuoguP3793
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

## LuoguP9981
```cpp
#include<iostream>
#include<queue>
#include<algorithm>
#define int long long
using namespace std;
const int N=2e5+10;
struct Edge{int to,w;};
int n,m,tot,cnt,tp[N],in[N],dep[N],rk[N],id[N];
long long ans[N];
vector<Edge> v[N],v2[N];
struct res{
    int e,rnk,to;
    bool operator <(const res &t)const{
        return e==t.e?rnk>t.rnk:e<t.e;
    }
};
bool cmp(int x,int y){return dep[x]<dep[y];}
signed main(){
	scanf("%lld%lld",&n,&m);
	for(int i=1;i<=m;i++){
        int x,y,w;
		scanf("%lld%lld%lld",&x,&y,&w);
		v[x].push_back({y,w});
        in[x]++;
		v2[y].push_back({x,w});
	}
    queue<int> q;
	for(int i=1;i<=n;i++) if(!in[i]) q.push(i);
	while(q.size()){
		int x=q.front();
        q.pop();
		tp[++tot]=x;
		for(auto it:v2[x]){
			if(in[it.to]){
				in[it.to]--;
				if(!in[it.to]) q.push(it.to);
			}
		}
	}
	for(int i=1;i<=n;i++){
		int x=tp[i];
		for(auto it:v2[x]){
			dep[it.to]=max(dep[it.to],dep[x]+1);
		}
	}
	for(int i=1;i<=n;i++) id[i]=i;
	sort(id+1,id+n+1,cmp);
	priority_queue<res> pq;
	int mxdep=0;
	for(int i=1;i<=n;i++){
		int x=id[i];
		if(dep[x] != mxdep){
			mxdep=dep[x];
			while(!pq.empty()){
				auto it=pq.top();pq.pop();
				rk[it.to]=++cnt;
			}
		}
		if(dep[x]){
			int mn=0x3f3f3f3f,mx=0;
			for(auto it:v[x]){
				if(dep[it.to]==dep[x]-1) mn=min(mn,it.w);
			}
			for(auto it:v[x]){
				if(dep[it.to]==dep[x]-1&&it.w==mn) mx=max(mx,rk[it.to]);
			}
			for(auto it:v[x]){
				if(dep[it.to]==dep[x]-1&&it.w==mn&&rk[it.to]==mx){
					ans[x]=ans[it.to]+it.w;
					pq.push({it.w,rk[it.to],x});
					break;
				}
			}
		}
		else pq.push({0,0,x});
	}
	for(int i=1;i<=n;i++) printf("%lld %lld\n",dep[i],ans[i]);
	return 0;
}
```

## LuoguP10475
```cpp
#include<ctime>
#include<iostream>
#define int long long
using namespace std;
double START=clock();
int r,c,nxt[10010],ans1,ans2;
int hsh[10010];
char mp[10010][100];
signed main(){
    scanf("%lld%lld",&r,&c);
    for(int i=1;i<=r;i++) scanf("%s",mp[i]+1);
    for(int i=1;i<=r;i++){
        for(int j=1;j<=c;j++){
            hsh[i-1]=(hsh[i-1]*20070903+mp[i][j]);
        }
    }
    int j=0;
    for(int i=1;i<r;i++){
        while(j>0&&hsh[i]!=hsh[j]) j=nxt[j-1];
        if(hsh[i]==hsh[j]) j++;
        nxt[i]=j;
    }
    ans1=r-nxt[r-1];
    for(int i=0;i<r;i++) hsh[i]=nxt[i]=0;
    for(int i=1;i<=c;i++){
        for(int j=1;j<=r;j++){
            hsh[i-1]=(hsh[i-1]*20070903+mp[j][i]);
        }
    }
    j=0;
    for(int i=1;i<c;i++){
        while(j>0&&hsh[i]!=hsh[j]) j=nxt[j-1];
        if(hsh[i]==hsh[j]) j++;
        nxt[i]=j;
    }
    ans2=c-nxt[c-1];  
    printf("%lld",ans1*ans2);
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```
## LuoguP10195
```cpp
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int N=2e5+10;
int n,p[N],v[N],nxt[N],pre[N],ans[N];
struct Node{
	int x,y,ti;
	bool operator < (const Node a) const{
		return ti>a.ti;
	}
};
priority_queue<Node> q;
int calc(int x,int y){
	return 2*(ceil(((p[y]-p[x])*1.0)/((v[x]+v[y])*1.0)))-x%2;
}
int solve(){
	scanf("%lld",&n);
    for(int i=1;i<=n;i++) nxt[i]=i+1,pre[i]=i-1,ans[i]=0;
    while(q.size()) q.pop();
	for(int i=1;i<=n;i++) scanf("%lld",&p[i]);
	for(int i=1;i<=n;i++) scanf("%lld",&v[i]);
	for(int i=1;i<n;i++) q.push({i,i+1,calc(i,i+1)});
	while(q.size()){
		Node f=q.top();
		q.pop();
		if(ans[f.x]||ans[f.y]) continue;
		ans[f.x]=ans[f.y]=f.ti;
		if(pre[f.x]>=1&&nxt[f.y]<=n){
			q.push({pre[f.x],nxt[f.y],calc(pre[f.x],nxt[f.y])});
		}
		nxt[pre[f.x]]=nxt[f.x];
		pre[nxt[f.x]]=pre[f.x];
		nxt[pre[f.y]]=nxt[f.y];
		pre[nxt[f.y]]=pre[f.y];
	}
	for(int i=1;i<=n;i++) printf("%lld ",ans[i]);
	printf("\n");
    return 0;
}
signed main(){
    int T;
	scanf("%lld",&T);
	while(T--) solve();
    return 0;
}
```
## LuoguP4185
```cpp
#include<ctime>
#include<iostream>
#include<algorithm>
#define int long long
using namespace std;
double START=clock();
const int N=1e5+10;
int n,m,ans[N],fa[N],siz[N];
struct Edge{
    int u,v,r;
    bool operator < (const Edge &t) const{
        return r>t.r;
    }
}e[N];
struct Query{
    int k,v,id;
    bool operator < (const Query &t) const{
        return k>t.k;
    }
}q[N];
int find(int x){
    return fa[x]==x?x:find(fa[x]);
}
void merge(int x,int y){
    int fx=find(x),fy=find(y);
    if(fx==fy) return;
    if(siz[fx]>siz[fy]) swap(fx,fy);
    fa[fx]=fy;siz[fy]+=siz[fx];
    return;
}
signed main(){
    scanf("%lld%lld",&n,&m);
    for(int i=1;i<n;i++){
        scanf("%lld%lld%lld",&e[i].u,&e[i].v,&e[i].r);
        fa[i]=i;siz[i]=1;
    } 
    fa[n]=n,siz[n]=1;
    for(int i=1;i<=m;i++){
        scanf("%lld%lld",&q[i].k,&q[i].v);
        q[i].id=i;
    }
    sort(e+1,e+n+1);
    sort(q+1,q+m+1);
    int pos=1;
    for(int i=1;i<=m;i++){
        while(e[pos].r>=q[i].k) merge(e[pos].u,e[pos].v),++pos;
        ans[q[i].id]=siz[find(q[i].v)]-1;
    }
    for(int i=1;i<=m;i++) printf("%lld\n",ans[i]);
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```
## LuoguP1955
```cpp
#include<ctime>
#include<iostream>
#include<algorithm>
#define int long long
using namespace std;
double START=clock();
const int N=1e5+10;
int n,x[N<<2],tot,fa[N<<2];
struct Query{int a,b,e;}q[N];
bool cmp(Query a,Query b){
    return a.e==b.e?a.a<b.a:a.e>b.e;
}
int find(int x){return fa[x]==x?x:fa[x]=find(fa[x]);}
void merge(int x,int y){
    if(find(x)==find(y)) return;
    fa[find(x)]=find(y);
}
int solve(){
    tot=0;
    scanf("%lld",&n);
    for(int i=1;i<=n;i++){
        scanf("%lld%lld%lld",&q[i].a,&q[i].b,&q[i].e);
        x[++tot]=q[i].a;x[++tot]=q[i].b;
    }
    sort(x+1,x+tot+1);
    tot=unique(x+1,x+tot+1)-x-1;
    for(int i=1;i<=tot;i++) fa[i]=i;
    sort(q+1,q+n+1,cmp);
    for(int i=1;i<=n;i++){
        q[i].a=lower_bound(x+1,x+tot+1,q[i].a)-x;
        q[i].b=lower_bound(x+1,x+tot+1,q[i].b)-x;
        if(q[i].e) merge(q[i].a,q[i].b);
        else if(find(q[i].a)==find(q[i].b)){
            printf("NO\n");
            return 0;
        }
    }
    printf("YES\n");
    return 0;
}
signed main(){
    int T;
    scanf("%lld",&T);
    while(T--) solve();
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```
## LuoguP4479
```cpp
#include<algorithm>
#include<iostream>
#include<cmath>
#include<cstring>
using namespace std;
#define int long long
const int N=100005;
int n,k,X[N],bit[N];
struct Point{
    int x,y,idx,t;
    bool operator < (const Point &b) const{
        return t==b.t?x<b.x:t<b.t;
    }
}p[N];
int lowbit(int x){return x & -x;}
void update(int x){for(;x<=n;x+=lowbit(x)) bit[x]++;}
int query(int x){
    int ans=0;
    for(;x>0;x-=lowbit(x)) ans+=bit[x];
    return ans;
}
bool check(int mid){
    for(int i=1;i<=n;i++) p[i].t=p[i].y-mid*p[i].x;
    sort(p+1,p+n+1);
    memset(bit,0,sizeof(bit));
    int rnk=0;
    for(int i=1;i<=n;i++){
        rnk+=query(p[i].idx-1);
        update(p[i].idx);
    }
    return rnk>=k;
}
signed main(){
    cin>>n>>k;
    for(int i=1;i<=n;i++){
    	cin>>p[i].x>>p[i].y;
    	X[i]=p[i].x;
	}
    sort(X+1,X+n+1);
    int tot=unique(X+1,X+n+1)-X-1;
    for(int i=1;i<=n;i++) p[i].idx=lower_bound(X+1,X+tot+1,p[i].x)-X;
    int l=-2e8,r=2e8,ans;
    while(l<=r){
        int mid=(l+r)>>1;
        if(check(mid)){
            ans=mid;
            l=mid+1;
        }
        else r=mid-1;
    }
    cout<<ans;
    return 0;
}
```
## LuoguP1966
```cpp
#include<bits/stdc++.h>
#define int long long
using namespace std;
const int N=1e5+10,MOD=1e8-3;
int A[N],B[N],a[N],b[N],l[N],bit[5*N],ans,n;
void add(int x,int y){for(;x<=n;x+=x&-x) bit[x]+=y;}
int query(int x){
	int res=0;
	for(;x;x-=x&-x) res+=bit[x];
	return res;
}
bool cmp1(int i,int j){return A[i]<A[j];}
bool cmp2(int i,int j){return B[i]<B[j];}
signed main(){
	cin>>n;
	for(int i=1;i<=n;i++){
		cin>>A[i];
		a[i]=i;
	} 
	for(int i=1;i<=n;i++){
		cin>>B[i];
		b[i]=i;
	}
	sort(a+1,a+1+n);sort(b+1,b+1+n);
	sort(a+1,a+1+n,cmp1);sort(b+1,b+1+n,cmp2);
	for(int i=1;i<=n;i++) l[a[i]]=b[i];
	for(int i=n;i;i--){
		ans=(ans+query(l[i]-1))%MOD;
		add(l[i],1);	
	}
	cout<<ans%MOD<<'\n';
	return 0;
}
```
## LuoguP3605
```cpp
#include<ctime>
#include<iostream>
#include<algorithm>
#include<vector>
#define int long long
using namespace std;
double START=clock();
const int N=1e5+10;
int n,p[N],x[N],bit[N],ans[N];
vector<vector<int> > ch(N);
int lowbit(int x){return x&-x;}
void add(int x,int k){
    for(int i=x;i<=n;i+=lowbit(i)) bit[i]+=k;
}
int query(int x){
    int res=0;
    for(int i=x;i>0;i-=lowbit(i)) res+=bit[i];
    return res;
}
void dfs(int u){
    ans[u]=-query(p[u]-1);
    add(p[u],1);
    for(auto v:ch[u]) dfs(v);
    ans[u]+=query(p[u]-1);
}
signed main(){
    scanf("%lld",&n);
    for(int i=1;i<=n;i++){
        scanf("%lld",p+i);
        x[i]=p[i];
    }
    sort(x+1,x+n+1);
    for(int i=1;i<=n;i++) p[i]=n-(lower_bound(x+1,x+n+1,p[i])-x)+1;
    for(int i=2;i<=n;i++){
        int t;
        scanf("%lld",&t);
        ch[t].push_back(i);
    }
    dfs(1);
    for(int i=1;i<=n;i++) printf("%lld\n",ans[i]);
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```
## LuoguP1972
```cpp
#include<iostream>
#include<vector>
#include<map>
#define int long long
using namespace std;
const int N=2e6+10;
int n,q,a[N],ans[N],bit[N];
vector<vector<pair<int,int> > > v(N);
int lowbit(int x){return x&-x;}
void add(int x,int k){
    for(int i=x;i<=n;i+=lowbit(i)) bit[i]+=k;
}
int query(int x){
    int res=0;
    for(int i=x;i>0;i-=lowbit(i)) res+=bit[i];
    return res;
}
signed main(){
    scanf("%lld",&n);
    for(int i=1;i<=n;i++) scanf("%lld",a+i);
    cin>>q;
    for(int i=1;i<=q;i++){
        int x,y;
        scanf("%lld%lld",&x,&y);
        v[x].push_back({y,i});
    }
    map<int,int> lst;
    for(int i=n;i>0;i--){
        if(lst.count(a[i])) add(lst[a[i]],-1);
        lst[a[i]]=i;
        add(i,1);
        for(auto j:v[i]) ans[j.second]=query(j.first);
    }
    for(int i=1;i<=q;i++) printf("%lld\n",ans[i]);
    return 0;
}
```
## LuoguP4113
```cpp
#include<iostream>
#include<vector>
#include<algorithm>
#define int long long
using namespace std;
const int N=2e6+10;
int c,n,m,a[N],ans[N],lst[N],lst1[N],bit[N];
struct Query{
	int l,r,id;
	bool operator < (const Query &t) const{
		return r==t.r?l<t.l:r<t.r;
	}
}q[N];
int lowbit(int x){return x&-x;}
void add(int x,int k){
	for(int i=x;i<=n;i+=lowbit(i)) bit[i]+=k;
}
int query(int x){
	int res=0;
	for(int i=x;i>0;i-=lowbit(i)) res+=bit[i];
	return res;
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>c>>m;
	for(int i=1;i<=n;i++) cin>>a[i];
	for(int i=1;i<=m;i++){
		cin>>q[i].l>>q[i].r;
		q[i].id=i;
	}
	sort(q+1,q+m+1);
	int i=1;
	for(int j=1;j<=m;j++){
		for(;i<=q[j].r;i++){
			if(lst1[a[i]]){
				add(lst1[a[i]],-1);
				lst1[a[i]]=lst[a[i]];
				lst[a[i]]=i;
				add(lst1[a[i]],1);
			}
			else if(lst[a[i]]){
				lst1[a[i]]=lst[a[i]];
				lst[a[i]]=i;
				add(lst1[a[i]],1);
			}
			else lst[a[i]]=i;			
		}
		ans[q[j].id]=query(q[j].r)-query(q[j].l-1);
	}
	for(int i=1;i<=m;i++) cout<<ans[i]<<endl;
	return 0;
}
```
## LuoguP3586
```cpp
#include<ctime>
#include<iostream>
#define int long long
using namespace std;
double START=clock();
#define ls(x) tr[(x)].s[0]
#define rs(x) tr[(x)].s[1]
#define fa(x) tr[(x)].fa
const int N=1e6+10;
int n,m,idx,root;
int p[N];
struct Node{
    int sum,siz,s[2],fa,key;
}tr[N];
bool get(int x){return x==rs(fa(x));}
void maintain(int x){
    tr[x].siz=tr[ls(x)].siz+tr[rs(x)].siz+1;
    tr[x].sum=tr[ls(x)].sum+tr[rs(x)].sum+tr[x].key;
}
int newnode(int key){tr[++idx].key=key,tr[idx].sum=key,tr[idx].siz=1;return idx;}
void clear(int x){ls(x)=rs(x)=fa(x)=tr[x].siz=tr[x].key=tr[x].sum=0;}
void rotate(int x){
    int y=fa(x),z=fa(y),c=get(x);
    if(tr[x].s[c^1]) fa(tr[x].s[c^1])=y; tr[y].s[c]=tr[x].s[c^1];
    tr[x].s[c^1]=y,fa(y)=x;
    if(z) tr[z].s[tr[z].s[1]==y]=x;fa(x)=z;
    maintain(y);maintain(x);
    return;
}
void splay(int x){
    for(int y=fa(x);y=fa(x),y;rotate(x)){
        if(fa(y)) rotate(get(y)==get(x)?y:x);
    }
    root=x;
}
void ins(int key){
    int now=root,f=0;
    while(now){
        f=now;
        now=tr[now].s[key>=tr[now].key];
    }
    now=newnode(key);
    fa(now)=f;tr[f].s[key>=tr[f].key]=now;
    splay(now);
}
void del(int key){
    int now=root,p=0;
    while(tr[now].key!=key&&now){
        p=now;
        now=tr[now].s[key>=tr[now].key];
    }
    if(!now){splay(p);return;}
    splay(now);
    int cur=ls(now);
    if(!cur){
        root=rs(now);fa(rs(now))=0;clear(now);
        return;
    }
    while(rs(cur)) cur=rs(cur);
    fa(rs(now))=cur;rs(cur)=rs(now);fa(ls(now))=0;clear(now);
    maintain(cur);
    splay(cur);
    return;
}
signed main(){
    scanf("%lld%lld",&n,&m);
    for(int i=1;i<=n;i++) ins(0);
    while(m--){
        char op;
        int x,y;
        cin>>op;
        scanf("%lld%lld",&x,&y);
        if(op=='U'){
            del(p[x]),p[x]=y,ins(y);     
        }
        else{
            ins(y);
            int sum=tr[ls(root)].sum;
            int siz=tr[root].siz-tr[ls(root)].siz-1;
            del(y);
            if(sum>=(x-siz)*y) printf("TAK\n");
            else printf("NIE\n");
        }
    }
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```
## LuoguP4588
```cpp
#include<iostream>
#define int long long
#define ls (u<<1)
#define rs (u<<1|1)
#define mid ((l+r)>>1)
using namespace std;
const int N=1e5+10;
int tr[N<<2];
int q,MOD;
void pushup(int u){tr[u]=tr[ls]*tr[rs]%MOD;}
void build(int u,int l,int r){
	if(l==r){
		tr[u]=1;
		return;
	}
	build(ls,l,mid);
	build(rs,mid+1,r);
	pushup(u);
}
void change(int u,int l,int r,int U,int k){
	if(l==r){
		tr[u]=k;
		return;
	}
	if(U<=mid) change(ls,l,mid,U,k);
	else change(rs,mid+1,r,U,k);
	pushup(u);
}
int solve(){
	cin>>q>>MOD;
	build(1,1,q);
	for(int i=1;i<=q;i++){
		int op,x;
		cin>>op>>x;
		if(op==1){
			x%=MOD;
			change(1,1,q,i,x);
			cout<<tr[1]<<'\n';
		}
		else{
			change(1,1,q,x,1);
			cout<<tr[1]<<'\n';
		}
	}
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
## LuoguP4198
```cpp
#include<bits/stdc++.h>
#define ls (u<<1)
#define rs (u<<1|1)
#define mid ((l+r)>>1)
using namespace std;
const int N=1e5+10;
int n,m;
double a[N];
struct node{
	double ma;
	int len;
}tr[4*N];
void pushup1(int u){
	tr[u].ma=max(tr[ls].ma,tr[rs].ma);
}
int pushup2(double lmax,int u,int l,int r){
	if(tr[u].ma<=lmax) return 0;
	if(a[l]>lmax) return tr[u].len; 
	if(l==r) return a[l]>lmax;
	if(tr[ls].ma<=lmax) return pushup2(lmax,rs,mid+1,r);
	else return pushup2(lmax,ls,l,mid)+tr[u].len-tr[ls].len;
}
void change(int u,int l,int r,int U,int c){
	if(l==r){
		tr[u].ma=(double)c/U;
		tr[u].len=1;
		return ;
	}
	if(U<=mid) change(ls,l,mid,U,c);
	else if(U>mid) change(rs,mid+1,r,U,c);
	pushup1(u);
	tr[u].len=tr[ls].len+pushup2(tr[ls].ma,rs,mid+1,r);
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	int x,y;
	while(m--){
		cin>>x>>y;
		a[x]=(double)y/x;
		change(1,1,n,x,y);
		cout<<tr[1].len<<'\n';
	}
	return 0;
}
```
## LuoguP3870
```cpp
#include<iostream>
#define int long long
#define ls (u<<1)
#define rs (u<<1|1)
#define mid ((l+r)>>1)
using namespace std;
const int N=2e5+10;
int n,m;
struct Node{
	int tag,sum;
}tr[N<<2];
void pushdown(int u,int l,int r){
	if(!tr[u].tag) return;
	tr[ls].tag^=1;tr[rs].tag^=1;
	tr[ls].sum=(mid-l+1)-tr[ls].sum;
	tr[rs].sum=(r-mid)-tr[rs].sum;
	tr[u].tag=0;
}
void change(int u,int l,int r,int L,int R){
	if(L<=l&&r<=R){
		tr[u].tag^=1;
		tr[u].sum=(r-l+1)-tr[u].sum;
		return;
	}
	pushdown(u,l,r);
	if(L<=mid) change(ls,l,mid,L,R);
	if(R>mid) change(rs,mid+1,r,L,R);
	tr[u].sum=tr[ls].sum+tr[rs].sum;
}
int query(int u,int l,int r,int L,int R){
	if(L<=l&&r<=R) return tr[u].sum;
	pushdown(u,l,r);
	int res=0;
	if(L<=mid) res+=query(ls,l,mid,L,R);
	if(R>mid) res+=query(rs,mid+1,r,L,R);
	return res;
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	while(m--){
		int c,a,b;
		cin>>c>>a>>b;
		if(!c) change(1,1,n,a,b);
		else cout<<query(1,1,n,a,b)<<'\n';
	}
	return 0;
}
```
## LuoguP5490
```cpp
#include<iostream>
#include<algorithm>
#define int long long
#define mid ((l+r)>>1)
#define ls (u<<1)
#define rs (u<<1|1)
using namespace std;
const int N=1e5+10;
struct Line{
    int x1,x2,y,k;
    bool operator < (const Line &t) const{
        return y<t.y;
    }
}line[N<<1];
struct Node{
    int cnt,len;
}tr[N<<4];
int n,X[N<<1],p,ans;
void pushup(int u,int l,int r){
    if(tr[u].cnt) tr[u].len=X[r]-X[l];
    else tr[u].len=tr[ls].len+tr[rs].len;
}
void add(int u,int l,int r,int L,int R,int k){
    if(L<=l&&r<=R){
        tr[u].cnt+=k;
        pushup(u,l,r);
        return;
    }
    if(L<mid) add(ls,l,mid,L,R,k);
    if(R>mid) add(rs,mid,r,L,R,k);
    pushup(u,l,r);
}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>n;
    for(int i=1;i<=n;i++){
        int x1,y1,x2,y2;
        cin>>x1>>y1>>x2>>y2;
        line[i]=(Line){x1,x2,y1,1};
        line[i+n]=(Line){x1,x2,y2,-1};
        X[i]=x1,X[i+n]=x2;
    }
    sort(X+1,X+2*n+1);
    sort(line+1,line+2*n+1);
    int p=unique(X+1,X+2*n+1)-X-1;
    for(int i=1;i<=2*n;i++){
        line[i].x1=lower_bound(X+1,X+p+1,line[i].x1)-X;
        line[i].x2=lower_bound(X+1,X+p+1,line[i].x2)-X;
        ans+=tr[1].len*(line[i].y-line[i-1].y);
        add(1,1,p,line[i].x1,line[i].x2,line[i].k);
    }
    cout<<ans;
    return 0;
}
```
## LuoguP1502
```cpp
#include<iostream>
#include<algorithm>
#define int long long
using namespace std;
#define ls (u<<1)
#define rs (u<<1|1)
#define mid ((l+r)>>1)
const int N=1e5+10;
int n,w,h,X[N];
struct Line{
	int l,r,h,w;
	bool operator < (const Line &a)const{
		return h!=a.h?h<a.h:w>a.w;
	}
}line[N<<2];
struct Node{
	int l,r,mx,add;
}tr[N<<2];
void pushup(int u){
	tr[u].mx=max(tr[ls].mx,tr[rs].mx);
}
void build(int u,int l,int r){
	tr[u].l=l,tr[u].r=r,tr[u].mx=tr[u].add=0;
	if(l==r) return;
	build(ls,l,mid);
	build(rs,mid+1,r);
}
void pushdown(int u){
	tr[ls].mx+=tr[u].add;
	tr[rs].mx+=tr[u].add;
	tr[ls].add+=tr[u].add;
	tr[rs].add+=tr[u].add;
	tr[u].add=0;
}
void change(int u,int L,int R,int k){
	int l=tr[u].l,r=tr[u].r;
	if(L<=l&&r<=R){
		tr[u].mx+=k;
		tr[u].add+=k;
		return;
	}
	pushdown(u);
	if(L<=mid) change(ls,L,R,k);
	if(R>mid) change(rs,L,R,k);
	pushup(u);
}
int solve(){
	cin>>n>>w>>h;
	for(int i=1;i<=n;i++){
		int x,y,l;
		cin>>x>>y>>l;
		int x1=x+w-1,y1=y+h-1;
		X[(i<<1)-1]=y;
		X[i<<1]=y+h-1;
		line[(i<<1)-1]=(Line){y,y1,x,l};
		line[i<<1]=(Line){y,y1,x1,-l};	
	}
	n<<=1;
	sort(X+1,X+n+1);
	sort(line+1,line+n+1);
	int cnt=unique(X+1,X+n+1)-X-1;
	for(int i=1;i<=n;i++){
		line[i].l=lower_bound(X+1,X+cnt+1,line[i].l)-X;
		line[i].r=lower_bound(X+1,X+cnt+1,line[i].r)-X;
	}
	build(1,1,cnt);
	int ans=0;
	for(int i=1;i<=n;i++){
		change(1,line[i].l,line[i].r,line[i].w);
		ans=max(ans,tr[1].mx);
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
## LuoguP4097
```cpp
#include<iostream>
#define int long long
#define ls (u<<1)
#define rs (u<<1|1)
#define mid ((l+r)>>1)
#define pii pair<double,int>
using namespace std;
const int M=39990,N=1e5+10;
const double eps=1e-10;
int n,lst,cnt,id[M<<2];
struct Line{
	double k,b;
	int xmi,xma;
	double q(int x){
		if(xmi<=x&&x<=xma) return k*x+b;
		else return -1e9;
	}
}f[N];
pii _max(pii a,pii b){
	if(a.first-b.first>eps) return a;
	else if(b.first-a.first>eps) return b;
	else return a.second<b.second?a:b;
}
pii query(int u,int l,int r,int k){
	pii res;
	if(id[u]) res={f[id[u]].q(k),id[u]};
	if(l==r) return res;
	if(k<=mid) res=_max(query(ls,l,mid,k),res);
	else res=_max(query(rs,mid+1,r,k),res);
	return res;
}
void update(int u,int l,int r,int L,int R,int k){
	//cerr<<u<<' '<<l<<' '<<r<<'\n';
	if(L<=l&&r<=R){
		if(!id[u]) return id[u]=k,void();//还没有过
		if(f[k].q(mid)-f[id[u]].q(mid)>eps) swap(k,id[u]);
		if(f[k].q(l)-f[id[u]].q(l)>eps||(f[k].q(l)==f[id[u]].q(l)&&k<id[u])) update(ls,l,mid,L,R,k);
		if(f[k].q(r)-f[id[u]].q(r)>eps||(f[k].q(r)==f[id[u]].q(r)&&k<id[u])) update(rs,mid+1,r,L,R,k);
		return;
	}
	if(L<=mid) update(ls,l,mid,L,R,k);
	if(R>mid) update(rs,mid+1,r,L,R,k);
}
signed main(){
	//freopen("P4097_8.in","r",stdin);
	//freopen("1.out","w",stdout);
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n;
	for(int i=1;i<=n;i++){
		int op;
		cin>>op;
		if(op==0){
			int k;
			cin>>k;k=(k+lst-1)%39989+1;
			cout<<(lst=query(1,1,M,k).second)<<'\n';
		}
		else{
			int x0,y0,x1,y1;
			cin>>x0>>y0>>x1>>y1;
			x0=(x0+lst-1)%39989+1;x1=(x1+lst-1)%39989+1;
			y0=(y0+lst-1)%(int)1e9+1;y1=(y1+lst-1)%(int)1e9+1;
			cnt++;
			if(x0==x1){
				f[cnt].k=0;
				f[cnt].b=max(y1,y0);
				f[cnt].xmi=f[cnt].xma=x0;
			}else{
				f[cnt].k=1.0*(1.0*y1-y0)/(1.0*x1-x0);
				f[cnt].b=1.0*(1.0*y1-f[cnt].k*x1);
				if(x0>x1) swap(x0,x1);
				f[cnt].xmi=x0;
				f[cnt].xma=x1;
			}
			update(1,1,M,x0,x1,cnt);
		}
	}
	return 0;
}
```
## LuoguP4556
```cpp
#include<iostream>
#include<vector>
#define int long long
#define mid ((l+r)>>1)
using namespace std;
const int N=1e5+10;
int n,m,fa[N][30],dep[N],tot,lg[N];
vector<vector<int> > tree(N);
struct Node{
	int u,cnt;
    friend bool operator < (Node a,Node b){return (a.cnt==b.cnt)?a.u>b.u:a.cnt<b.cnt;}
    friend Node operator + (Node a,Node b){return (Node){a.u,a.cnt+b.cnt};}
};
vector<vector<Node> > op(N);
int s[N<<5][2];
Node ma[N<<5]; 
void merge(int p1,int p2,int l,int r){
	if(l==r){
		ma[p1]=ma[p1]+ma[p2];
		return;
	}
	if(s[p1][0]&&s[p2][0]) merge(s[p1][0],s[p2][0],l,mid);
	else if(s[p2][0]) s[p1][0]=s[p2][0];
	if(s[p1][1]&&s[p2][1]) merge(s[p1][1],s[p2][1],mid+1,r);
	else if(s[p2][1]) s[p1][1]=s[p2][1];
	ma[p1]=max(ma[s[p1][0]],ma[s[p1][1]]);
}
void insert(int u,int l,int r,Node k){
	if(l==r){
		ma[u]=k+ma[u];
		return;
	}
	if(k.u<=mid) insert(s[u][0]=(s[u][0]?s[u][0]:++tot),l,mid,k);
	if(k.u>mid) insert(s[u][1]=(s[u][1]?s[u][1]:++tot),mid+1,r,k);
	ma[u]=max(ma[s[u][0]],ma[s[u][1]]);
}
void build_tree(int u){
	dep[u]=dep[fa[u][0]]+1;
	for(int i=1;i<=lg[dep[u]];i++) fa[u][i]=fa[fa[u][i-1]][i-1];
	for(auto it:tree[u]){
		if(it!=fa[u][0]){
			fa[it][0]=u;
			build_tree(it);
		}
	} 
}
int get_lca(int x,int y){
	if(dep[x]<dep[y]) swap(x,y);
	while(dep[x]>dep[y]) x=fa[x][lg[dep[x]-dep[y]]];
	if(x==y) return x;
	for(int i=lg[dep[x]];i>=0;i--){
		if(fa[x][i]!=fa[y][i]) x=fa[x][i],y=fa[y][i];
	}
	return fa[x][0];
}
int ans[N];
void merge_tree(int u){
	for(auto it:tree[u]){
		if(it!=fa[u][0]){
			merge_tree(it); 
			merge(u,it,0,N);
		}
	}
	for(auto it:op[u]) insert(u,0,N,it);
	ans[u]=ma[u].u;
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	lg[0]=-1;tot=n;
	for(int i=1;i<n;i++){
		int a,b;
		cin>>a>>b;
		tree[a].push_back(b);
		tree[b].push_back(a);
		lg[i]=lg[i>>1]+1;
	}
	build_tree(1);
	for(int i=1;i<=m;i++){
		int x,y,z;
		cin>>x>>y>>z;
		int lca=get_lca(x,y);
		op[x].push_back((Node){z,1});
		op[y].push_back((Node){z,1});
		op[lca].push_back((Node){z,-1});
		op[fa[lca][0]].push_back((Node){z,-1});
	}
	merge_tree(1);
	for(int i=1;i<=n;i++) cout<<ans[i]<<'\n';
	return 0;
}
```
## LuoguP3919
```cpp
#include<iostream>
#define int long long
#define mid ((l+r)>>1)
using namespace std;
const int N=1e6+10;
int n,m,a[N],root[N],tot;
struct Node{
	int ls,rs,val;
}tr[N<<5];
void build(int &u,int l,int r){
	u=++tot;
	if(l==r){
		tr[u].val=a[l];
		return;
	}
	build(tr[u].ls,l,mid);
	build(tr[u].rs,mid+1,r);
}
void change(int &u,int l,int r,int U,int k){
	tr[++tot]=tr[u]; u=tot;
	if(l==r){
		tr[u].val=k;
		return;
	}
	if(U<=mid) change(tr[u].ls,l,mid,U,k);
	else change(tr[u].rs,mid+1,r,U,k);
	return;
}
int query(int u,int l,int r,int U){
	if(l==r) return tr[u].val;
	if(U<=mid) return query(tr[u].ls,l,mid,U);
	else return query(tr[u].rs,mid+1,r,U);
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<=n;i++) cin>>a[i];
	build(root[0],1,n);
	for(int i=1;i<=m;i++){
		int v,op,loc,val;
		cin>>v>>op>>loc;
		if(op==1){
			cin>>val;
			root[i]=root[v];
			change(root[i],1,n,loc,val);
		}
		else{
			cout<<query(root[v],1,n,loc)<<'\n';
			root[i]=root[v];
		}
	}
	return 0;
}
```
## LuoguP3402
```cpp
#include<iostream>
#define mid ((l+r)>>1)
using namespace std;
const int N=2e5+10;
int n,m,ti;
struct Tree{
	int root[N],st[N],ls[N<<5],rs[N<<5],val[N<<5],tot;
	void build(int &rt,int l,int r){
		rt=++tot;
		if(l==r){
			val[rt]=st[l];
			return;
		}
		build(ls[rt],l,mid);
		build(rs[rt],mid+1,r);
	}
	void upload(int &rt,int l,int r,int U,int k){
		int kk=rt;
		rt=++tot;
		ls[rt]=ls[kk];
		rs[rt]=rs[kk];
		if(l==r){
			val[rt]=k;
			return;
		}
		if(U<=mid) upload(ls[rt],l,mid,U,k);
		else upload(rs[rt],mid+1,r,U,k);
	}
	int query(int rt,int l,int r,int U){
		if(l==r) return val[rt];
		if(U<=mid) return query(ls[rt],l,mid,U);
		else return query(rs[rt],mid+1,r,U);
	}
}bin,siz;
int find(int x){
	while(bin.query(bin.root[ti],1,n,x)!=x) x=bin.query(bin.root[ti],1,n,x);
	return x;
}
void merge(int x,int y){
	int fx=find(x);
	int fy=find(y);
	if(fx==fy) return;
	int sizx=siz.query(siz.root[ti],1,n,fx),sizy=siz.query(siz.root[ti],1,n,fy);
	if(sizx<=sizy){
		bin.upload(bin.root[ti],1,n,fx,fy);
		siz.upload(siz.root[ti],1,n,fy,sizx+sizy);
	}
	else{
		bin.upload(bin.root[ti],1,n,fy,fx);
		siz.upload(siz.root[ti],1,n,fx,sizx+sizy);
	}
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	for(int i = 1;i <= n;i++) bin.st[i]=i,siz.st[i]=1;
	bin.build(bin.root[0],1,n);
	siz.build(siz.root[0],1,n);
	for(ti=1;ti<=m;ti++){
		int opt,x,y;
		cin>>opt;
		bin.root[ti]=bin.root[ti-1];
		siz.root[ti]=siz.root[ti-1];
		if(opt==1){
			cin>>x>>y;
			merge(x,y);
		}
		else if(opt == 2){
			cin>>x;
			bin.root[ti]=bin.root[x];
			siz.root[ti]=siz.root[x];
		}
		else{
			cin>>x>>y;
			cout<<(find(x) == find(y))<<'\n';
		}
	}
	return 0;
}
```
## LuoguP3834
```cpp
#include<iostream>
#include<algorithm>
#define int long long
#define mid ((l+r)>>1)
using namespace std;
const int N=2e5+10;
int n,m,x,root[N],tot,a[N],p,X[N];
struct Node{int ls,rs,cnt;}tr[N<<5];
void build(int &u,int l,int r){
	u=++tot;
	if(l==r) return;
	build(tr[u].ls,l,mid);
	build(tr[u].rs,mid+1,r);
}
void change(int &u,int l,int r,int U){
	tr[++tot]=tr[u];
	u=tot;
	if(l==r){
		tr[u].cnt++;
		return;
	}
	if(U<=mid) change(tr[u].ls,l,mid,U);
	else change(tr[u].rs,mid+1,r,U);
	tr[u].cnt=tr[tr[u].ls].cnt+tr[tr[u].rs].cnt;
}
int query(int u,int v,int l,int r,int k){
	if(l==r) return l;
	int s=tr[tr[u].ls].cnt-tr[tr[v].ls].cnt;
	//cerr<<s<<endl;
	if(s>=k) return query(tr[u].ls,tr[v].ls,l,mid,k);
	else return query(tr[u].rs,tr[v].rs,mid+1,r,k-s);
}
void print(int u,int l,int r){
	//cout<<l<<' '<<r<<' '<<tr[u].cnt<<'\n';
	if(l==r) return;
	if(tr[u].ls) print(tr[u].ls,l,mid);
	if(tr[u].rs) print(tr[u].rs,mid+1,r);
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<=n;i++){
		cin>>a[i];
		X[i]=a[i];
	}
	sort(X+1,X+n+1);
	p=unique(X+1,X+n+1)-X-1;
	for(int i=1;i<=n;i++) a[i]=lower_bound(X+1,X+p+1,a[i])-X;
	build(root[0],1,p);
	for(int i=1;i<=n;i++){
		root[i]=root[i-1];
		change(root[i],1,p,a[i]);
	}
	//print(root[n],1,p);
	while(m--){
		int l,r,k;
		cin>>l>>r>>k;
		cout<<X[query(root[r],root[l-1],1,p,k)]<<'\n';
	}
	return 0;
}
```
## LuoguP2633
```cpp
#include<iostream>
#include<vector>
#include<algorithm>
#define int long long
#define ls(x) (tr[x].ls)
#define rs(x) (tr[x].rs)
#define fa(x) (f[x][0])
#define mid ((l+r)>>1)
using namespace std;
const int N=1e5+10;
int n,m,a[N],X[N],dep[N],f[N][20],lg[N],root[N],p,tot,lst;
vector<vector<int> > v(N);
struct Node{
	int ls,rs,cnt;
}tr[N<<5];
void build(int &u,int l,int r){
	u=++tot;
	if(l==r) return;
	build(ls(u),l,mid);
	build(rs(u),mid+1,r);
}
void change(int &u,int l,int r,int U){
	tr[++tot]=tr[u];
	u=tot;
	if(l==r){
		tr[u].cnt++;
		return;
	}
	if(U<=mid) change(ls(u),l,mid,U);
	else change(rs(u),mid+1,r,U);
	tr[u].cnt=tr[ls(u)].cnt+tr[rs(u)].cnt;
}
void build_tree(int u){
	dep[u]=dep[fa(u)]+1;
	for(int i=1;i<=lg[dep[u]];i++) f[u][i]=f[f[u][i-1]][i-1];
	root[u]=root[fa(u)];
	change(root[u],1,p,a[u]);
	for(auto it:v[u]){
		if(it!=fa(u)){
			f[it][0]=u;
			build_tree(it);
		}
	}
}
int get_lca(int u,int v){
	if(dep[u]<dep[v]) swap(u,v);
	while(dep[u]>dep[v]) u=f[u][lg[dep[u]-dep[v]]];
	if(u==v) return u;
	for(int i=lg[dep[u]];i>=0;i--){
		if(f[u][i]!=f[v][i]) u=f[u][i],v=f[v][i];
	}
	return fa(u);
}
int query(int w,int x,int y,int z,int l,int r,int k){
	if(l==r) return l;
	int s=tr[ls(w)].cnt+tr[ls(x)].cnt-tr[ls(y)].cnt-tr[ls(z)].cnt;
	if(s>=k) return query(ls(w),ls(x),ls(y),ls(z),l,mid,k);
	else return query(rs(w),rs(x),rs(y),rs(z),mid+1,r,k-s);
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	lg[0]=-1;
	for(int i=1;i<=n;i++){
		cin>>a[i];
		X[i]=a[i];
		lg[i]=lg[i>>1]+1;
	}
	sort(X+1,X+n+1);
	p=unique(X+1,X+n+1)-X-1;
	for(int i=1;i<=n;i++) a[i]=lower_bound(X+1,X+p+1,a[i])-X;
	build(root[0],1,p);
	for(int i=1;i<n;i++){
		int x,y;
		cin>>x>>y;
		v[x].push_back(y);v[y].push_back(x);
	}
	build_tree(1);
	while(m--){
		int u,v,k;
		cin>>u>>v>>k;
		u^=lst;
		int lca=get_lca(u,v);
		lst=X[query(root[u],root[v],root[lca],root[fa(lca)],1,p,k)];
		cout<<lst<<'\n';
	}
	return 0;
}
```
## LuoguP3302
```cpp
#include<ctime>
#include<iostream>
#include<vector>
#include<algorithm>
using namespace std;
#define ls(x) tr[x].ls
#define rs(x) tr[x].rs
#define mid ((l+r)>>1)
double START=clock();
const int N=8e4+10;
int n,m,t,val[N],root[N],fa[N][18],lg[N],dep[N],X[N],p,tot,siz[N];
bool vis[N];
vector<vector<int> > v(N);
struct Tree{
    int ls,rs,cnt;
}tr[N*600];
void change(int &u,int l,int r,int k){
    tr[++tot]=tr[u];
    u=tot;
    if(l==r){
        tr[u].cnt++;
        return;
    }
    if(k<=mid) change(ls(u),l,mid,k);
    else change(rs(u),mid+1,r,k);
    tr[u].cnt=tr[ls(u)].cnt+tr[rs(u)].cnt;
}
void dfs(int u){
    vis[u]=1;
    siz[u]=1;
    dep[u]=dep[fa[u][0]]+1;
    root[u]=root[fa[u][0]];
    change(root[u],1,p,val[u]);
    for(int i=1;i<=17;i++) fa[u][i]=fa[fa[u][i-1]][i-1];
    for(auto it:v[u]){
        if(it==fa[u][0]) continue;
        fa[it][0]=u;
        dfs(it);
        siz[u]+=siz[it];
    }
}
void build(int &u,int l,int r){
    u=++tot;
    if(l==r) return;
    build(ls(u),l,mid);
    build(rs(u),mid+1,r);
}
int find_lca(int x,int y){
    if(dep[x]<dep[y]) swap(x,y);
    while(dep[x]>dep[y]) x=fa[x][lg[dep[x]-dep[y]]];
    if(x==y) return x;
    for(int i=lg[dep[x]];i>=0;i--){
        if(fa[x][i]!=fa[y][i]) x=fa[x][i],y=fa[y][i];
    }
    return fa[x][0];
}
int query(int x,int y,int z,int w,int l,int r,int k){
    if(l==r) return l;
    int s=tr[ls(x)].cnt+tr[ls(y)].cnt-tr[ls(z)].cnt-tr[ls(w)].cnt;
    if(s>=k) return query(ls(x),ls(y),ls(z),ls(w),l,mid,k);
    else return query(rs(x),rs(y),rs(z),rs(w),mid+1,r,k-s);
}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>n>>n>>m>>t;
    lg[0]=-1;
    for(int i=1;i<=n;i++){
        cin>>val[i];
        lg[i]=lg[i>>1]+1;
        X[i]=val[i];
    }
    sort(X+1,X+n+1);
    p=unique(X+1,X+n+1)-X-1;
    for(int i=1;i<=n;i++) val[i]=lower_bound(X+1,X+p+1,val[i])-X;
    while(m--){
        int x,y;
        cin>>x>>y;
        v[x].push_back(y);
        v[y].push_back(x);
    }
    build(root[0],1,p);
    for(int i=1;i<=n;i++){
        if(!vis[i]) dfs(i);
    }
    int lst=0;
    while(t--){
        char op;int x,y,k;
        cin>>op>>x>>y;
        x^=lst,y^=lst;
        if(op=='Q'){
            cin>>k;k^=lst;
            int lca=find_lca(x,y);
            //cerr<<query(root[x],root[y],root[lca],root[fa[lca][0]],1,p,k)<<'\n';
            lst=X[query(root[x],root[y],root[lca],root[fa[lca][0]],1,p,k)];
            cout<<lst<<'\n';
        }
        else{
            if(siz[x]<siz[y]) swap(x,y);
            v[x].push_back(y),v[y].push_back(x);
            siz[x]+=siz[y];fa[y][0]=x;
            dfs(y);
        }
    }
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```
## LuoguP4148
```cpp
#include<ctime>
#include<iostream>
#include<algorithm>
#define ls(x) tr[x].ls
#define rs(x) tr[x].rs
using namespace std;
double START=clock();
const int N=2e5+10;
int n,rt;
int top,rub[N],tot;
struct Point{int pos[2],w;}p[N];
struct Node{
    int mi[2],ma[2],sum,ls,rs,siz;
    Point tp;
}tr[N];
int newnode(){
    if(top) return rub[top--];
    else return ++tot;
}
void pushup(int u){
    for(int i=0;i<=1;i++){
        tr[u].mi[i]=tr[u].ma[i]=tr[u].tp.pos[i];
        if(ls(u)) tr[u].ma[i]=max(tr[u].ma[i],tr[ls(u)].ma[i]);
        if(rs(u)) tr[u].ma[i]=max(tr[u].ma[i],tr[rs(u)].ma[i]);
        if(ls(u)) tr[u].mi[i]=min(tr[u].mi[i],tr[ls(u)].mi[i]);
        if(rs(u)) tr[u].mi[i]=min(tr[u].mi[i],tr[rs(u)].mi[i]);        
    }
    tr[u].sum=tr[ls(u)].sum+tr[rs(u)].sum+tr[u].tp.w;
    tr[u].siz=tr[ls(u)].siz+tr[rs(u)].siz+1;
}
void pia(int u,int num){
    if(ls(u)) pia(ls(u),num);
    p[tr[ls(u)].siz+num+1]=tr[u].tp,rub[++top]=u;
    if(rs(u)) pia(rs(u),num+tr[ls(u)].siz+1);
}
int D;
bool operator < (Point a,Point b){
    return a.pos[D]<b.pos[D];
}
int rebuild(int l,int r,int d){
    if(l>r) return 0;
    int mid=(l+r)>>1,k=newnode();
    D=d;nth_element(p+l,p+mid,p+r+1);
    tr[k].tp=p[mid];
    tr[k].ls=rebuild(l,mid-1,d^1);
    tr[k].rs=rebuild(mid+1,r,d^1);
    pushup(k);
    return k;
}
void check(int &u,int d){
    if(tr[ls(u)].siz>tr[u].siz*0.72||tr[rs(u)].siz>tr[u].siz*0.72){
        pia(u,0);u=rebuild(1,tr[u].siz,d);
    }
}
void insert(int &u,Point k,int d){
    if(!u){u=newnode(),ls(u)=rs(u)=0,tr[u].tp=k,pushup(u);return;}
    if(k.pos[d]<=tr[u].tp.pos[d]) insert(ls(u),k,d^1);
    else insert(rs(u),k,d^1);
    pushup(u);
    check(u,d);
}
int in(int x1,int y1,int x2,int y2,int X1,int Y1,int X2,int Y2) {
	return (X1>=x1&&X2<=x2&&Y1>=y1&&Y2<=y2);
}
int out(int x1,int y1,int x2,int y2,int X1,int Y1,int X2,int Y2) {
	return (x1>X2||x2<X1||y1>Y2||y2<Y1);
}
int query(int u,int x1,int y1,int x2,int y2){
    if(!u) return 0;
    int res=0;
    if(in(x1,y1,x2,y2,tr[u].mi[0],tr[u].mi[1],tr[u].ma[0],tr[u].ma[1])) return tr[u].sum;
    if(out(x1,y1,x2,y2,tr[u].mi[0],tr[u].mi[1],tr[u].ma[0],tr[u].ma[1])) return 0;
    if(in(x1,y1,x2,y2,tr[u].tp.pos[0],tr[u].tp.pos[1],tr[u].tp.pos[0],tr[u].tp.pos[1])) res+=tr[u].tp.w;
    res+=query(ls(u),x1,y1,x2,y2)+query(rs(u),x1,y1,x2,y2);
    return res;
}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>n;
    int ans=0;
    while("ဪ"){
        int op;
        cin>>op;
        if(op==3) break;
        if(op==1){
            int x,y,a;
            cin>>x>>y>>a;
            insert(rt,(Point){x^ans,y^ans,a^ans},0);
        }
        else{
            int x1,x2,y1,y2;
            cin>>x1>>y1>>x2>>y2;
            ans=query(rt,x1^ans,y1^ans,x2^ans,y2^ans);
            cout<<ans<<'\n';
        }
    }
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```
## LuoguP3710
```cpp
#include<ctime>
#include<iostream>
#include<algorithm>
#define ls(x) tr[x].ls
#define rs(x) tr[x].rs
#define int long long 
using namespace std;
double START=clock();
const int N=1.5e5+10,MOD=998244353;
int n,m,cnt,rt,D,tot;
struct Change{int l,r,d,op,end;}c[N];
//0位置1时间
struct Node{
    int ls,rs,mi[2],ma[2],add,mul,pos[2],val;
    bool operator < (const Node &t) const{
        return pos[D]<t.pos[D];
    }    
}tr[N],q[N];
void mul(int u,int k){
    tr[u].val=tr[u].val*k%MOD;
    tr[u].add=tr[u].add*k%MOD;
    tr[u].mul=tr[u].mul*k%MOD;
}
void add(int u,int k){
    tr[u].val=(tr[u].val+k)%MOD;
    tr[u].add=(tr[u].add+k)%MOD;
}
void pushdown(int u){
    if(tr[u].mul!=1) mul(ls(u),tr[u].mul),mul(rs(u),tr[u].mul),tr[u].mul=1;
    if(tr[u].add) add(ls(u),tr[u].add),add(rs(u),tr[u].add),tr[u].add=0;
}
void pushup(int u){
    for(int i=0;i<=1;i++){
        tr[u].ma[i]=tr[u].mi[i]=tr[u].pos[i];
        if(ls(u)){
            tr[u].ma[i]=max(tr[u].ma[i],tr[ls(u)].ma[i]);
            tr[u].mi[i]=min(tr[u].mi[i],tr[ls(u)].mi[i]);
        }
        if(rs(u)){
            tr[u].ma[i]=max(tr[u].ma[i],tr[rs(u)].ma[i]);
            tr[u].mi[i]=min(tr[u].mi[i],tr[rs(u)].mi[i]);            
        }
    }
}
void build(int &u,int l,int r,int d){
    u=++tot;D=d;
    int mid=(l+r)>>1;
    nth_element(q+l+1,q+mid+1,q+r+1);
    tr[u]=q[mid],tr[u].mul=1;
    if(l<mid) build(ls(u),l,mid-1,d^1);
    if(r>mid) build(rs(u),mid+1,r,d^1);
    pushup(u);
}
bool out(int u,int x1,int y1,int x2,int y2){
    return x1>tr[u].ma[0]||x2<tr[u].mi[0]||y1>tr[u].ma[1]||y2<tr[u].mi[1];
}
bool in(int u,int x1,int y1,int x2,int y2){
    return x1<=tr[u].mi[0]&&x2>=tr[u].ma[0]&&y1<=tr[u].mi[1]&&y2>=tr[u].ma[1];
    //节点u代表的矩形是否包含于修改的矩形
}
bool check(int u,int x1,int y1,int x2,int y2){
    return x1<=tr[u].pos[0]&&x2>=tr[u].pos[0]&&y1<=tr[u].pos[1]&&y2>=tr[u].pos[1];
}
void add(int u,int x1,int y1,int x2,int y2,int k){
    if(out(u,x1,y1,x2,y2)) return;
    if(in(u,x1,y1,x2,y2)){
        add(u,k);//全部包含，整体修改
        return;
    }
    if(check(u,x1,y1,x2,y2)) tr[u].val=(tr[u].val+k)%MOD;//当前的点上正好有一个询问，修改询问的结果
    pushdown(u);
    if(ls(u)) add(ls(u),x1,y1,x2,y2,k);
    if(rs(u)) add(rs(u),x1,y1,x2,y2,k);
}
void mul(int u,int x1,int y1,int x2,int y2,int k){
    //与add同理
    if(out(u,x1,y1,x2,y2)) return;
    if(in(u,x1,y1,x2,y2)){
        mul(u,k);
        return;
    }
    if(check(u,x1,y1,x2,y2)) tr[u].val=tr[u].val*k%MOD;
    pushdown(u);
    if(ls(u)) mul(ls(u),x1,y1,x2,y2,k);
    if(rs(u)) mul(rs(u),x1,y1,x2,y2,k);    
}
int ans;
void query(int u,int x,int y){
    if(x>tr[u].ma[0]||x<tr[u].mi[0]||y>tr[u].ma[1]||y<tr[u].mi[1]) return;
    if(x==tr[u].pos[0]&&y==tr[u].pos[1]){
        ans=tr[u].val;
        return;
    }
    pushdown(u);
    if(ls(u)) query(ls(u),x,y);
    if(rs(u)) query(rs(u),x,y);
}
signed main(){
    //每个修改可能会被撤销，以位置为x轴，时间为y轴，则每个修改对一个矩形产生了影响
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>n>>m;
    for(int i=1;i<=m;i++){
        cin>>c[i].op;
        if(c[i].op<=2){
            cin>>c[i].l>>c[i].r>>c[i].d;
            c[i].d%=MOD;
            c[i].end=m;
            continue;
        }
        int p;cin>>p;
        if(c[i].op==4) c[p].end=i;//存在到end位置，默认为m
        if(c[i].op==3){
            c[i].end=p;
            q[++cnt].pos[0]=p,q[cnt].pos[1]=i;
        }
    }
    build(rt,1,cnt,0);//建树，把询问先插入到平面里，以后只修改带询问的点，没问到的不管
    for(int i=1;i<=m;i++){
        if(c[i].op==1) add(rt,c[i].l,i,c[i].r,c[i].end,c[i].d);//矩形左下角(l,i)，右上角(r,end)，因为第i个修改从时间i开始，结束于end
        if(c[i].op==2) mul(rt,c[i].l,i,c[i].r,c[i].end,c[i].d);
        if(c[i].op==3){
            query(rt,c[i].end,i);//查询平面上点(pos,i)的值，即pos在时间i时的值
            cout<<ans<<'\n';
        }
    }
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```
## LuoguP2801
```cpp
#include<iostream>
#include<cmath>
#include<algorithm>
#define int long long
const int N=1e6+10;
using namespace std;
int n,q,a[N],len,bel[N],ll[N],rr[N],cnt,b[N],tag[N];
void resort(int x){
	for(int i=ll[x];i<=rr[x];i++) b[i]=a[i];
	sort(b+ll[x],b+rr[x]+1);
}
void add(int l,int r,int k){
	if(bel[l]==bel[r]){
		for(int i=l;i<=r;i++) a[i]+=k;
		resort(bel[l]);
	}
	for(int i=l;i<=rr[bel[l]];i++) a[i]+=k;
	for(int j=ll[bel[r]];j<=r;j++) a[j]+=k;
	resort(bel[l]),resort(bel[r]);
	for(int i=bel[l]+1;i<bel[r];i++) tag[i]+=k;
}
int find(int x,int k){
	int l=ll[x],r=rr[x];
	while(l<=r){
		int mid=(l+r)>>1;
		if(b[mid]<k) l=mid+1;
		else r=mid-1;
	}
	return rr[x]-(l-1);
}
int query(int l,int r,int k){
	int res=0;
	if(bel[l]==bel[r]){
		for(int i=l;i<=r;i++){
			if(a[i]+tag[bel[l]]>=k) res++;
		}
		return res;
	}
	for(int i=l;i<=rr[bel[l]];i++) if(a[i]+tag[bel[l]]>=k) res++;
	for(int i=ll[bel[r]];i<=r;i++) if(a[i]+tag[bel[r]]>=k) res++;
	for(int i=bel[l]+1;i<bel[r];i++) res+=find(i,k-tag[i]);
	return res;
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>q;
	len=sqrt(n);
	cnt=n/len;if(n%len) cnt++;
	for(int i=1;i<=n;i++){
		cin>>a[i];b[i]=a[i];
		bel[i]=(i-1)/len+1;
	}
	for(int i=1;i<=cnt;i++) ll[i]=(i-1)*len+1,rr[i]=i*len;
	rr[cnt]=n;
	for(int i=1;i<=cnt;i++) sort(b+ll[i],b+rr[i]+1);
	for(int i=1;i<=q;i++){
		char c;int l,r,k;
		cin>>c>>l>>r>>k;
		if(c=='M') add(l,r,k);
		else cout<<query(l,r,k)<<'\n';
	}
	return 0;
}
```
## LuoguP3203
```cpp
#include <bits/stdc++.h>
using namespace std;
const int N=2e5+10;
int bel[N],L[N],R[N],num[N],jump[N],step[N],cnt,n;
void update(int l, int r) {
    for (int i=r;i>=l;i--){
        if (i+num[i]>R[bel[i]]){
            jump[i]=i+num[i];
            step[i]=1;
        } 
		else{
            jump[i]=jump[i+num[i]];
            step[i]=step[i+num[i]]+1;
        }
    }
}
int ask(int x){
    int ans=0;
    while(x<n){
        ans+=step[x];
        x=jump[x];
    }
    return ans;
}
int main(){
    cin>>n;
    for(int i=0;i<n;i++) cin>>num[i];
    cnt=sqrt(n);
    for(int i=0;i<n;i++) bel[i]=i/cnt;
    for(int i=0;i<=bel[n-1];i++) L[i]=i*cnt,R[i]=(i+1)*cnt-1;
    R[bel[n-1]]=n-1;
    update(0,n-1);
    int q;
    cin>>q;
    while(q--){
        int op,x,y;
        cin>>op>>x;
        if(op==1){
            cout<<ask(x)<<'\n';
        } 
		else{
            cin>>y;
            num[x]=y;
            update(L[bel[x]],R[bel[x]]);
        }
    }
}
```
## LuoguP3863
```cpp
#include<ctime>
#include<iostream>
#include<algorithm>
#include<cmath>
#define int long long
using namespace std;
double START=clock();
const int N=5e5+10;
int n,Q,a[N],cnt1,cnt2,ans[N];
int L[N],R[N],bel[N],add[N],cnt,B,b[N],order[N];
struct Change{
    int pos,tim,v;
}c[N];
struct Query{
    int pos,v,tim,id;
}q[N];
bool cmp1(Change x,Change y){return x.pos==y.pos?x.tim<y.tim:x.pos<y.pos;}
bool cmp2(Query x,Query y){return x.pos==y.pos?x.tim<y.tim:x.pos<y.pos;}
void add_(int l,int r,int k){
    if(bel[l]==bel[r]){
        for(int i=l;i<=r;i++) b[i]+=k;
        for(int i=L[bel[l]];i<=R[bel[l]];i++) order[i]=b[i];
        sort(order+L[bel[l]],order+R[bel[l]]+1);
    }
    else{
        for(int i=l;i<=R[bel[l]];i++) b[i]+=k;
        for(int i=L[bel[l]];i<=R[bel[l]];i++) order[i]=b[i];
        sort(order+L[bel[l]],order+R[bel[l]]+1);
        for(int i=L[bel[r]];i<=r;i++) b[i]+=k;
        for(int i=L[bel[r]];i<=R[bel[r]];i++) order[i]=b[i];
        sort(order+L[bel[r]],order+R[bel[r]]+1);
        for(int i=bel[l]+1;i<bel[r];i++) add[i]+=k;
    }
}
int query(int r,int k){
    int l=0,res=0;
    if(bel[l]==bel[r]){
        for(int i=l;i<=r;i++) if(b[i]+add[bel[l]]>=k) res++;
    }
    else{
        for(int i=l;i<=R[bel[l]];i++) if(b[i]+add[bel[l]]>=k) res++;
        for(int i=L[bel[r]];i<=r;i++) if(b[i]+add[bel[r]]>=k) res++;
        //cerr<<res<<endl;
        for(int i=bel[l]+1;i<bel[r];i++){
            int t=lower_bound(order+L[i],order+R[i]+1,k-add[i])-(order+L[i]);
            res+=(R[i]-L[i]+1)-t;
        }
    }
    return res;
}
signed main(){
	//freopen("P3863_15.in","r",stdin);
	//freopen("1.out","w",stdout);
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>n>>Q;
    B=sqrt(Q);
    cnt=Q/B;if(Q%B) cnt++;
    for(int i=1;i<=cnt;i++) L[i]=(i-1)*B+1,R[i]=min(i*B,Q);
    for(int i=1;i<=n;i++) cin>>a[i];
    for(int i=1;i<=Q;i++){
        bel[i]=(i-1)/B+1;
        int op;
        cin>>op;
        if(op==1){
            int l,r,x;
            cin>>l>>r>>x;
            c[++cnt1]=(Change){l,i,x};
            c[++cnt1]=(Change){r+1,i,-x};
        }
        else{
            int p,y;
            cin>>p>>y;
            q[++cnt2]=(Query){p,y,i,cnt2};
        }
    }
    sort(c+1,c+cnt1+1,cmp1);
    sort(q+1,q+cnt2+1,cmp2);
    int ch=1,qu=1;
    for(int i=1;i<=n;i++){//处理每个数
        add_(0,Q,a[i]-a[i-1]);
        while(c[ch].pos==i) add_(c[ch].tim,Q,c[ch].v),ch++;
        while(q[qu].pos==i){
            ans[q[qu].id]=query(q[qu].tim-1,q[qu].v);
            //if(a[i]>=q[qu].v) ans[q[qu].id]++;
            qu++;
        }
    }
    for(int i=1;i<=cnt2;i++) cout<<ans[i]<<'\n';
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;    
    return 0;
}
```
## CF1179C
```cpp
#include<iostream>
#define ls (u<<1)
#define rs (u<<1|1)
#define mid ((l+r)>>1)
#define int long long
using namespace std;
const int N=1e6+10;
int n,m,a[N],b[N],q,mi[N<<2],tag[N<<2];
void pushdown(int u){
	mi[ls]+=tag[u];mi[rs]+=tag[u];
	tag[ls]+=tag[u];tag[rs]+=tag[u];
	tag[u]=0;
}
void insert(int u,int l,int r,int L,int R,int k){
	if(L<=l&&r<=R){
		mi[u]+=k;tag[u]+=k;
		return;
	}
	pushdown(u);
	if(L<=mid) insert(ls,l,mid,L,R,k);
	if(R>mid) insert(rs,mid+1,r,L,R,k);
	mi[u]=min(mi[ls],mi[rs]);
}
int query(int u,int l,int r){
	if(mi[u]>0) return -1;
	if(l==r) return l;
	pushdown(u);
	if(mi[rs]<0) return query(rs,mid+1,r);
	if(mi[ls]<0) return query(ls,l,mid);
	return -1;
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<=n;i++){
		cin>>a[i];
		insert(1,1,N-1,1,a[i],-1);
	}
	for(int i=1;i<=m;i++){
		cin>>b[i];
		insert(1,1,N-1,1,b[i],1);
	}
	cin>>q;
	while(q--){
		int op,i,x;
		cin>>op>>i>>x;
		if(op==1){
			if(x>a[i]) insert(1,1,N-1,a[i]+1,x,-1);
			else insert(1,1,N-1,x+1,a[i],1);
            // 这里可以画图理解一下
            // 就是减去原本 a_i 的贡献，加上 x_i 的贡献，影响的就是不重叠的那段区间
			a[i]=x;
		}
		else{
			if(x>b[i]) insert(1,1,N-1,b[i]+1,x,1);
			else insert(1,1,N-1,x+1,b[i],-1);
			b[i]=x;			
		}
		cout<<query(1,1,N-1)<<'\n';
	}
	return 0;
}
```
## CF85D
```cpp
#include<iostream>
#define int long long
#define mid ((l+r)>>1)
#define ls(x) tr[x].ls
#define rs(x) tr[x].rs
using namespace std;
const int N=3e5+10,M=1e9;
int n,rt,tot;
struct Node{
	int cnt,sum[5],ls,rs;
}tr[N<<3];
void pushup(int u){
	tr[u].cnt=tr[ls(u)].cnt+tr[rs(u)].cnt;
	for(int i=0;i<5;i++){
		tr[u].sum[i]=tr[ls(u)].sum[i]+tr[rs(u)].sum[((i-tr[ls(u)].cnt)%5+5)%5];
	}
}
void add(int &u,int l,int r,int U){
	if(!u) u=++tot;
	if(l==r){
		tr[u].sum[1]=U;tr[u].cnt=1;
		return;
	}
	if(U<=mid) add(ls(u),l,mid,U);
	else add(rs(u),mid+1,r,U);
	pushup(u);
}
void del(int &u,int l,int r,int U){
	if(!u) u=++tot;
	if(l==r){
		tr[u].sum[1]=tr[u].cnt=0;
		return;
	}
	if(U<=mid) del(ls(u),l,mid,U);
	else del(rs(u),mid+1,r,U);
	pushup(u);	
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n;
	while(n--){
		string op;int x;
		cin>>op;
		if(op=="sum"){
			cout<<tr[1].sum[3]<<'\n';
			continue;
		}
		cin>>x;
		if(op=="add") add(rt,1,M,x);
		else if(op=="del") del(rt,1,M,x);
	}
	return 0;
}
```
## LuoguP3071
```cpp
#include<iostream>
#define int long long
#define ls u<<1
#define rs u<<1|1
#define mid ((l+r)>>1)
using namespace std;
const int N=5e5+10;
int n,m,ans;
struct Node{
	int lma,rma,ma,tag;
}tr[N<<2];
void pushup(int u,int l,int r){
	tr[u].ma=max(max(tr[ls].ma,tr[rs].ma),tr[ls].rma+tr[rs].lma);
	if(tr[ls].ma==mid-l+1) tr[u].lma=tr[ls].ma+tr[rs].lma;
	else tr[u].lma=tr[ls].lma;
	if(tr[rs].ma==r-mid) tr[u].rma=tr[rs].ma+tr[ls].rma;
	else tr[u].rma=tr[rs].rma;
}
void build(int u,int l,int r){
	tr[u].lma=tr[u].rma=tr[u].ma=r-l+1;
	tr[u].tag=-1;
	if(l==r) return;
	build(ls,l,mid);
	build(rs,mid+1,r);
	pushup(u,l,r);
}
void pushdown(int u,int l,int r){
	if(tr[u].tag==-1) return;
	if(tr[u].tag==1){//覆盖
		tr[ls].lma=tr[ls].rma=tr[ls].ma=0;
		tr[rs].lma=tr[rs].rma=tr[rs].ma=0;
	}
	else{//清空
		tr[ls].lma=tr[ls].rma=tr[ls].ma=mid-l+1;
		tr[rs].lma=tr[rs].rma=tr[rs].ma=r-mid;
	}
	tr[ls].tag=tr[rs].tag=tr[u].tag;
	tr[u].tag=-1;		
}
int query(int u,int l,int r,int k){
	pushdown(u,l,r);
	if(l==r) return l;
	if(tr[ls].ma>=k) return query(ls,l,mid,k);
	else if(tr[ls].rma+tr[rs].lma>=k) return mid-tr[ls].rma+1;
	else return query(rs,mid+1,r,k);
	//先选左边，再选中间，再选右边
}
void update(int u,int l,int r,int L,int R,bool op){
	if(L<=l&&r<=R){
		if(op==1) tr[u].lma=tr[u].rma=tr[u].ma=0;
		else tr[u].lma=tr[u].rma=tr[u].ma=r-l+1;
		tr[u].tag=op;
		return;
	}
	pushdown(u,l,r);
	if(L<=mid) update(ls,l,mid,L,R,op);
	if(R>mid) update(rs,mid+1,r,L,R,op);
	pushup(u,l,r);
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	freopen("seating.in","r",stdin);
	freopen("seating.out","w",stdout);
	cin>>n>>m;
	build(1,1,n);
	while(m--){
		char op;
		int a,b;
		cin>>op>>a;
		if(op=='A'){
			if(tr[1].ma<a) ans++;
			else{
				int pos=query(1,1,n,a);
				update(1,1,n,pos,pos+a-1,1);
			}
		}
		else{
			cin>>b;
			update(1,1,n,a,b,0);
		}
	}
	cout<<ans;
	return 0;
}
```

## LuoguP2756
```cpp
#include<ctime>
#include<iostream>
#include<queue>
#include<cstring>
#define int long long
using namespace std;
double START=clock();
const int N=500,INF=1e9;
int n,m,head[N],tot=1,dep[N],cur[N],s,t;
struct Edge{
    int to,nxt,f;
}e[50010];
void addedge(int u,int v,int c){
    e[++tot].nxt=head[u];
    e[tot].to=v;
    e[tot].f=c;
    head[u]=tot;
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
            if(e[i].f&&dep[v]==-1){
                dep[v]=dep[u]+1;
                cur[v]=head[v];
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
            e[i].f-=t;e[i^1].f+=t;
            flow+=t;
        }
    }
    return flow;
}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>m>>n;
    s=n+1,t=n+2;
    for(int i=1;i<=m;i++){
        addedge(s,i,1);
        addedge(i,s,0);
    }
    for(int i=m+1;i<=n;i++){
        addedge(i,t,1);
        addedge(t,i,0);
    }
    int u,v;
    while(cin>>u>>v){
        if(u==-1) break;
        addedge(u,v,1);
        addedge(v,u,0);
    }
    int ans=0;
    while(bfs()){
        int flow;
        while(flow=find(s,INF)) ans+=flow;
    }
    cout<<ans<<'\n';
    for(int i=1;i<=m;i++){
        for(int j=head[i];j;j=e[j].nxt){
            if(!e[j].f&&e[j].to<=n) cout<<i<<' '<<e[j].to<<'\n';
        }
    }
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```
## LuoguP3254
```cpp
#include<ctime>
#include<iostream>
#include<queue>
#include<cstring>
#define int long long
using namespace std;
double START=clock();
const int N=1000,INF=1e9;
int n,m,s,t,tot=1,dep[N],cur[N],head[N];
struct Edge{
    int to,nxt,f;
}e[100000];
void addedge(int u,int v,int c){
    e[++tot].nxt=head[u];e[tot].to=v;e[tot].f=c;
    head[u]=tot;
    e[++tot].nxt=head[v];e[tot].to=u;e[tot].f=0;
    head[v]=tot;
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
int sum;
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>m>>n;
    s=n+m+1,t=n+m+2;
    for(int i=1,r;i<=m;i++){
        cin>>r;sum+=r;
        addedge(s,i,r);
        for(int j=m+1;j<=n+m;j++){
            addedge(i,j,1);
        }        
    }
    for(int i=m+1,c;i<=n+m;i++){
        cin>>c;
        addedge(i,t,c);
    }
    int ans=0;
    while(bfs()){
        int flow;
        while(flow=find(s,INF)) ans+=flow;
    }
    cerr<<ans<<'\n';
    if(ans!=sum){
        cout<<0;
        return 0;
    }
    cout<<"1\n";
    for(int i=1;i<=m;i++){
        for(int j=head[i];j;j=e[j].nxt){
            if(e[j].to>m&&e[j].f==0) cout<<e[j].to-m<<' ';
        }
        cout<<'\n';
    }
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```
## LuoguP2891
```cpp
#include<ctime>
#include<iostream>
#include<cstring>
#include<queue>
#define int long long
using namespace std;
double START=clock();
const int N=50000,INF=1e9;
int n,f,d,head[N],dep[N],cur[N],s,t,tot=1;
struct Edge{
    int to,nxt,f;
}e[N];
void addedge(int u,int v,int c){
    e[++tot]={v,head[u],c};
    head[u]=tot;
    e[++tot]={u,head[v],0};
    head[v]=tot;
}
bool bfs(){
    queue<int> q;
    memset(dep,-1,sizeof(dep));
    q.push(s);dep[s]=0;cur[s]=head[s];
    while(q.size()){
        int u=q.front();q.pop();
        for(int i=head[u];i;i=e[i].nxt){
            int v=e[i].to;
            if(dep[v]==-1&&e[i].f){
                dep[v]=dep[u]+1;
                cur[v]=head[v];
                if(v==t)return 1;
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
        int v=e[i].to;
        cur[u]=i;
        if(dep[v]==dep[u]+1&&e[i].f){
            int t=find(v,min(e[i].f,limit-flow));
            if(!t) dep[v]=-1;
            e[i].f-=t,e[i^1].f+=t;
            flow+=t;
        }
    }
    return flow;
}
signed main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>n>>f>>d;
    s=f+n+n+d+1,t=s+1;
    for(int i=1;i<=f;i++) addedge(s,i,1);
    for(int i=f+n+n+1;i<s;i++) addedge(i,t,1);
    for(int i=f+1;i<=f+n;i++) addedge(i,i+n,1);
    for(int i=1,ff,dd,x;i<=n;i++){
        cin>>ff>>dd;
        for(int j=1;j<=ff;j++){
            cin>>x;
            addedge(x,i+f,1);
        }
        for(int j=1;j<=dd;j++){
            cin>>x;
            addedge(i+f+n,x+f+n+n,1);
        }
    }
    int r=0;
    while(bfs()){
        int flow;
        while(flow=find(s,INF)) r+=flow;
    }
    cout<<r;
    cerr<<"\nTime: "<<clock()-START<<"ms"<<endl;
    return 0;
}
```
## POJ3204
```cpp
#include<iostream>
#include<cstring>
#include<queue>
#define int long long
using namespace std;
const int N=510,M=10010,INF=1e9;
int n,m,s,t,dep[N],head[N],cur[N],tot=1;
bool vis_s[N],vis_t[N];
struct Edge{
	int to,nxt,f;
}e[M];
void addedge(int u,int v,int c){
	e[++tot]={v,head[u],c};
	head[u]=tot;
	e[++tot]={u,head[v],0};
	head[v]=tot;
}
bool bfs(){
	queue<int> q;
	memset(dep,-1,sizeof(dep));
	q.push(s);dep[s]=0;cur[s]=head[s];
	while(q.size()){
		int u=q.front();q.pop();
		for(int i=head[u];i;i=e[i].nxt){
			int v=e[i].to;
			if(dep[v]==-1&&e[i].f){
				dep[v]=dep[u]+1;
				cur[v]=head[v];
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
		int v=e[i].to;
		cur[u]=i;
		if(dep[v]==dep[u]+1&&e[i].f){
			int t=find(v,min(e[i].f,limit-flow));
			if(!t) dep[v]=-1;
			e[i].f-=t;e[i^1].f+=t;
			flow+=t;
		}
	}
	return flow;
}
void dfs(int u,bool st[],bool p){
	st[u]=1;
	for(int i=head[u];i;i=e[i].nxt){
		int j=i^p,v=e[i].to;
		if(e[j].f&&!st[v]) dfs(v,st,p);
	}
}
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	cin>>n>>m;
	s=1,t=n;
	while(m--){
		int a,b,c;
		cin>>a>>b>>c;
		addedge(++a,++b,c);
	}
	int r=0;
	while(bfs()){
		int flow;
		while(flow=find(s,INF)) r+=flow;
	}
	dfs(s,vis_s,0);
	dfs(t,vis_t,1);
	int ans=0;
	for(int i=2;i<=tot;i+=2){
		if(!e[i].f&&vis_s[e[i^1].to]&&vis_t[e[i].to]) ans++;
	}
	cout<<ans;
	return 0;
}
```
## LuoguP2274
```cpp
#include<iostream>
#include<cstring>
#include<queue>
#define int long long
using namespace std;
const int N=10010,INF=1e9;
int S,T,n,m,head[N],cur[N],dep[N],tot=1,sum;
int dx[]={0,1,0,-1},dy[]={1,0,-1,0};
struct Edge{int to,nxt,f;}e[100010];
int hsh(int a,int b){return (a-1)*m+b;}
void add(int u,int v,int c){
    e[++tot]={v,head[u],c};head[u]=tot;
    e[++tot]={u,head[v],0};head[v]=tot;
}
bool bfs(){
    queue<int> q;
    memset(dep,-1,sizeof(dep));
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
signed main(){
    cin>>n>>m;
    S=n*m+1,T=S+1;
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            int x;
            cin>>x;sum+=x;
            if(!((i+j)%2)){
                add(S,hsh(i,j),x);
                for(int k=0;k<4;k++){
                    int tx=i+dx[k],ty=j+dy[k];
                    if(tx==0||ty==0||tx>n||ty>m) continue;
                    add(hsh(i,j),hsh(tx,ty),INF);
                }
            }
            else add(hsh(i,j),T,x);

        }
    }
    int r=0;
    while(bfs()){
        int flow;
        while(flow=find(S,INF)) r+=flow;
    }
    cout<<sum-r;
    return 0;  
}
```
## BZOJ3275
```cpp
#include<iostream>
#include<queue>
#include<cstring>
#include<cmath>
using namespace std;
const int N=3010,INF=1e9;
int n,a[N],head[N],cur[N],dep[N],tot=1,S,T,sum;
struct Edge{int to,nxt,f;}e[50010];
void add(int u,int v,int c){
    e[++tot]={v,head[u],c};head[u]=tot;
    e[++tot]={u,head[v],0};head[v]=tot;
}
int gcd(int x,int y){return y==0?x:gcd(y,x%y);}
bool bfs(){
    queue<int> q;
    memset(dep,-1,sizeof(dep));
    q.push(S);dep[S]=0;cur[S]=head[S];
    while(q.size()){
        int u=q.front();q.pop();
        for(int i=head[u];i;i=e[i].nxt){
            int v=e[i].to;
            if(dep[v]==-1&&e[i].f){
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
        cur[u]=i;int v=e[i].to;
        if(dep[v]==dep[u]+1&&e[i].f){
            int t=find(v,min(e[i].f,limit-flow));
            if(!t) dep[v]=-1;
            e[i].f-=t;e[i^1].f+=t;flow+=t;
        }
    }
    return flow;
}
int main(){
    cin>>n;
    S=n+1,T=S+1;
    for(int i=1;i<=n;i++) cin>>a[i];
    for(int i=1;i<=n;i++){
        sum+=a[i];
        if(a[i]%2){
            add(S,i,a[i]);
            for(int j=1;j<=n;j++){
                int k=sqrt(a[i]*a[i]+a[j]*a[j]);
                if(gcd(a[i],a[j])==1&&k*k==a[i]*a[i]+a[j]*a[j]) add(i,j,INF);
            }
        }
        else add(i,T,a[i]);
    }
    int r=0;
    while(bfs()){
        int flow;
        while(flow=find(S,INF)) r+=flow;
    }
    cout<<sum-r;
    return 0;
}
```
## LuoguP4015
```cpp
#include<iostream>
#include<queue>
#include<cstring>
using namespace std;
const int N=510,INF=0x3f3f3f3f;
int m,n,S,T,head[N],dis[N],incf[N],pre[N],tot=1,cost;
bool vis[N];
struct Edge{int to,nxt,f,w;}e[50010];
void add(int u,int v,int c,int w){
    e[++tot]={v,head[u],c,w};head[u]=tot;
    e[++tot]={u,head[v],0,-w};head[v]=tot;
}
bool spfa(){
    queue<int> q;
    memset(vis,0,sizeof(vis));
    memset(dis,0x3f,sizeof(dis));
    q.push(S);dis[S]=0;vis[S]=1;incf[S]=INF;
    while(q.size()){
        int u=q.front();q.pop();
        vis[u]=0;
        for(int i=head[u];i;i=e[i].nxt){
            int v=e[i].to;
            if(e[i].f&&dis[v]>dis[u]+e[i].w){
                dis[v]=dis[u]+e[i].w;
                incf[v]=min(incf[u],e[i].f);
                pre[v]=i;
                if(!vis[v]) vis[v]=1,q.push(v);
            }
        }
    }
    return dis[T]!=INF;
}
void EK(){
    while(spfa()){
        cost+=dis[T]*incf[T];
        for(int i=T;i!=S;i=e[pre[i]^1].to){
            e[pre[i]].f-=incf[T];
            e[pre[i]^1].f+=incf[T];
        }
    }
}
int main(){
    cin>>m>>n;
    S=m+n+1,T=S+1;
    for(int i=1,x;i<=m;i++){
        cin>>x;
        add(S,i,x,0);
    }
    for(int i=1,x;i<=n;i++){
        cin>>x;
        add(i+m,T,x,0);
    }
    for(int i=1;i<=m;i++){
        for(int j=1,x;j<=n;j++){
            cin>>x;
            add(i,j+m,INF,x);
        }
    }
    EK();
    cout<<cost<<'\n';
    for(int i=2;i<=tot;i++){
        e[i].w*=-1;
        if(!(i%2))e[i].f+=e[i^1].f,e[i^1].f=0;
    }
    cost=0;
    EK();
    cout<<-cost;
    return 0;
}
```
## LuoguP4012
```cpp
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;
const int N=1e4+10,M=1e5+10,INF=0x3f3f3f3f;
int a,b,p,q,id[N][N],tot_,tot=1,pre[N],incf[N],dis[N],head[N],S,T;
bool vis[N];
struct Edge{int to,nxt,f,w;}e[M];
void add(int u,int v,int c,int w){
    e[++tot]={v,head[u],c,-w};head[u]=tot;
    e[++tot]={u,head[v],0,w};head[v]=tot;
}
bool spfa(){
    queue<int> q;q.push(S);
    memset(dis,0x3f,sizeof(dis));
    memset(vis,0,sizeof(vis));
    dis[S]=0;vis[S]=1;incf[S]=INF;
    while(q.size()){
        int u=q.front();q.pop();
        vis[u]=0;
        for(int i=head[u];i;i=e[i].nxt){
            int v=e[i].to;
            if(e[i].f&&dis[v]>dis[u]+e[i].w){
                dis[v]=dis[u]+e[i].w;
                incf[v]=min(incf[u],e[i].f);
                pre[v]=i;
                if(!vis[v]) vis[v]=1,q.push(v);
            }
        }
    }
    return dis[T]!=INF;
}
int EK(){
    int cost=0;
    while(spfa()){
        cost+=dis[T]*incf[T];
        for(int i=T;i!=S;i=e[pre[i]^1].to){
            e[pre[i]].f-=incf[T];
            e[pre[i]^1].f+=incf[T];
        }
    }
    return cost;
}
int main(){
    S=++tot_;T=++tot_;
    cin>>a>>b>>p>>q;
    for(int i=0;i<=p+1;i++){
        for(int j=0;j<=q+1;j++){
            id[i][j]=++tot_;
        }
    }
    for(int i=0;i<=p;i++){
        for(int j=0,x;j<q;j++){
            cin>>x;
            add(id[i][j],id[i][j+1],1,x);
            add(id[i][j],id[i][j+1],INF,0);

        }
    }
    for(int i=0;i<=q;i++){
        for(int j=0,x;j<p;j++){
            cin>>x;
            add(id[j][i],id[j+1][i],1,x);
            add(id[j][i],id[j+1][i],INF,0);
        }
    }
    while(a--){
        int k,y,x;
        cin>>k>>x>>y;
        add(S,id[x][y],k,0);
    }
    while(b--){
        int k,y,x;
        cin>>k>>x>>y;
        add(id[x][y],T,k,0);
    }
    cout<<-EK();
    return 0;
}
```
## LuoguP4174
```cpp
#include<iostream>
#include<queue>
#include<cstring>
using namespace std;
const int N=1e5+10,INF=1e9;
int n,m,p[N],head[N],cur[N],dep[N],tot=1,S,T,sum;
struct Edge{int to,nxt,f;}e[N<<2];
void add(int u,int v,int c){
    e[++tot]={v,head[u],c};head[u]=tot;
    e[++tot]={u,head[v],0};head[v]=tot;
}
bool bfs(){
    queue<int> q;q.push(S);
    memset(dep,-1,sizeof(dep));
    dep[S]=0;cur[S]=head[S];
    while(q.size()){
        int u=q.front();q.pop();
        for(int i=head[u];i;i=e[i].nxt){
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
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    cin>>n>>m;
    S=n+m+1,T=S+1;
    for(int i=1;i<=n;i++) cin>>p[i],add(i,T,p[i]);
    for(int i=1,a,b,c;i<=m;i++){
        cin>>a>>b>>c;
        add(i+n,a,INF);add(i+n,b,INF);
        add(S,i+n,c);sum+=c;
    }
    cout<<sum-Dinic();
    return 0;
}
```
## LuoguP5996
```cpp
#include<iostream>
#include<algorithm>
#include<set>
#define int long long
#define pii pair<int,int>
using namespace std;
const int N=2e5+10;
int n,m,w,h,sum;
struct Node{
	int x,y,v;
	bool operator < (const Node &t) const{return x<t.x;}
}a[N],b[N];
multiset<pii> s;
signed main(){
	cin>>n>>m>>w>>h;
	for(int i=1,x,y;i<=n;i++){
		cin>>x>>y>>a[i].v;
		a[i].x=x*h+y*w,a[i].y=x*h-y*w;//转换坐标
		sum+=a[i].v;
	}
	for(int i=1,x,y;i<=m;i++){
		cin>>x>>y>>b[i].v;
		b[i].x=x*h+y*w,b[i].y=x*h-y*w;
	}	
    //扫描线
	sort(a+1,a+n+1);sort(b+1,b+m+1);
	for(int i=1,j=1;i<=m;i++){
		while(j<=n&&a[j].x<=b[i].x) s.insert({a[j].y,a[j++].v});//将手办加入
		int limit=b[i].v;
		while(limit&&s.size()){
			auto it=s.lower_bound({b[i].y,0});//二分
			if(it==s.end()) break;
			pii u=*it;s.erase(it);
			int flow=min(limit,u.second);
			sum-=flow;u.second-=flow;limit-=flow;//增广流量
			if(u.second) s.insert(u);//手办没被榨干，则再次加入 set
		}
	}
	cout<<sum;
	return 0;
}
```
## LuoguP4043
```cpp
#include<iostream>
#include<cstring>
#include<queue>
using namespace std;
const int N=2010,INF=0x3f3f3f3f;
int d[N],head[N],tot=1,n,ans,S,T,incf[N],pre[N],dis[N];
bool vis[N];
struct Edge{int to,nxt,f,w;}e[200010];
void add(int u,int v,int c,int w){
    e[++tot]={v,head[u],c,w};head[u]=tot;
    e[++tot]={u,head[v],0,-w};head[v]=tot;
}
bool SPFA(){
    queue<int> q;q.push(S);
    memset(dis,0x3f,sizeof(dis));
    memset(vis,0,sizeof(vis));
    dis[S]=0;vis[S]=1;incf[S]=INF;
    while(q.size()){
        int u=q.front();q.pop();
        vis[u]=0;
        for(int i=head[u];i;i=e[i].nxt){
            int v=e[i].to;
            if(e[i].f&&dis[v]>dis[u]+e[i].w){
                dis[v]=dis[u]+e[i].w;
                incf[v]=min(incf[u],e[i].f);
                pre[v]=i;
                if(!vis[v]) q.push(v);
            }
        }
    }
    return dis[T]!=INF;
}
int EK(){
    int r=0;
    while(SPFA()){
        r+=dis[T]*incf[T];
        for(int i=T;i!=S;i=e[pre[i]^1].to){
            e[pre[i]].f-=incf[T];
            e[pre[i]^1].f+=incf[T];
        }
    }
    return r;
}
int main(){
    cin>>n;
    for(int i=1,k;i<=n;i++){
        cin>>k;
        for(int j=1,b,t;j<=k;j++){
            cin>>b>>t;
            d[i]--;d[b]++;ans+=t;
            add(i,b,INF,t);
        }
    }
    S=n+2,T=n+3;
    for(int i=2;i<=n;i++) add(i,n+1,INF,0);
    for(int i=1;i<=n;i++){
        if(d[i]>0) add(S,i,d[i],0);
        else if(d[i]<0) add(i,T,-d[i],0);
    }
    add(n+1,1,INF,0);
    cout<<ans+EK()<<endl;
    return 0;
}
```