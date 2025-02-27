---
title: 2.27模拟赛总结
date: 2025-02-27 18:21:00
excerpt: wyq布置的模拟赛题。。
categories: 
    - 模拟赛总结
tags: 
    - 动态规划
    - 最短路
    - 分层图
    - LCA
---

写了 150 分，赛时策略有问题。

想到了 B 题的正解，代码能力不行调了一个多小时没调出来，才去写的 D 题。写了 40 分结果忘了取模全挂完了。。

补题暂时补到 350 分

## A. 序列异或
#### 题意
给定一个数列，求任意选出四个数异或和为 $0$ 的方案数。
#### 题解
把四个数分成两组，每组中两个数异或起来答案一定是一样的。枚举 $i,j$，能与它组成答案的一共有 $cnt_{a_i\oplus a_j}$ 个。为避免重复计算，先枚举 $i$ 后面能与它组答案的数 $j$，计算完再将 $i$ 前面能与它组答案的数加到 $cnt$ 里。
#### 代码
```cpp
#include<iostream>
#define int long long
using namespace std;
int n,a[5010],ans,b[2000010];
signed main(){
	ios::sync_with_stdio(0);
	cin.tie(0);cout.tie(0);
	freopen("xor.in","r",stdin);
	freopen("xor.out","w",stdout);
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

## B. 自驾游
#### 题意
给定有向图和起点终点 $s,t$，图上每条边长度为 $d$ 米，初始速度 $v$ 是 $1$ 米每秒。规定通过一条边需要的时间是 $\lceil \frac{d}{v} \rceil$，有 $p$ 个维修站，在维修站 $p_i$ 可以任意次花费 $c_i$ 的时间将速度翻倍。有一部分边是坑坑洼洼的，经过它以后速度会重新变回 $1$，求从 $s$ 到 $t$ 的最短时间。
#### 题解
速度只会是 $2$ 的次幂，所以考虑按照速度建分层图，边权为通过时间。每种速度建一层.因为 $2^{20}$ 已经超过最大边权，再加速每条边也至少需要一秒通过，所以建 $20$ 层即可。

对于维修站，它可以将速度翻倍，所以有维修站的每个点，两层之间建一条指向下一层，边权为 $c$ 的边即可。

总结下来，设 $(u,i)$ 表示第 $i$ 层的点 $u$，有三种连边：
- 通过平整的道路：$(u,i)\to (v,i)$，边权 $\lceil \frac{d}{2^i}\rceil$。
- 通过不平整的道路：$(u,i)\to (v,0)$，边权 $\lceil \frac{d}{2^i}\rceil$。
- 维修站加速：$(u,i)\to (u,i+1)$，边权 $c$。

建出来分层图跑最短路即可。实现细节中要注意点的表示，用 pair 表示点和层数，维护 $head,vis,dis$ 数组时映射到一个值上。直接在建完第一层图后建 $20$ 层图，每条边复制以下就行。赛时没写出来这个。
#### 代码
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
	freopen("trip.in","r",stdin);
	freopen("trip.out","w",stdout);
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
## C. 车马象
#### 题意
给定 $a\times b$ 大小的棋盘，在位置 $(c,d)$ 放一个车/马/象，问走 $e$ 步能有几种不同的路径。共 $m(m\le 50)$ 个询问。
#### 题解
矩阵优化递推写的不详细没看懂，补了 80 分。

对于 $e\le 10000$ 的 60 分，可以 dp 解决。设 $f_{i,j,k}$ 表示走 $k$ 步有几种不同的路径到达 $(i,j)$，枚举 $k$，对于每个 $k$ 再枚举棋盘上的每个点从上一层转移过来即可。

马和象每个点只能从 $4$ 或者 $8$ 个点转移过来，暴力枚举即可。对于车则可以从 第 $i$ 行和第 $j$ 列的每一个点转移过来，为了避免重复遍历，使用数组记录每一层每行和每列分别的和 $sumr$ 与 $sumc$。注意位置 $(i,j)$ 不能带来贡献，所以将它减去。
$$
f_{i,j,k}=sumr_{i,k-1}+sumc_{j,k-1}-2\times f_{i,j,k-1}。
$$

对于只有车的 20 分，容易发现答案就是 $(n+m-2)^e$，推导过程与上面的 dp 类似，这个直接快速幂就能求解。同样的马与象的递推式如果写成矩阵的形式也能快速幂可惜我不会。

至少前 60 分的 dp 有充足时间的话能考虑出来，但是赛时直接没看这个题。
#### 代码
80分。
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
			continue;
		}
		f[c][d][0]=1;
		h[c][0]++,w[d][0]++;
		for(int i=1;i<=e;i++){
			for(int j=1;j<=a;j++){
				for(int k=1;k<=b;k++){
					if(op==0){//车
						f[j][k][i]=(h[j][i-1]+w[k][i-1]-2*f[j][k][i-1]+MOD)%MOD;
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
				}
			}
		}
	}
	return 0;
}
```
## D. 距离
#### 题意
给定一棵树每个点有两个权值 $a$ 和 $b$，对于每个结点 $u$ 求
$$
\sum_{x\in subtree_u}\sum_{y\in subtree_u}\min\{|a_x-a_y|,|b_x-b_y|\}
$$
对 $1e9+7$ 取模。
#### 题解
赛时写了 40 分做法，枚举每个点 $u$，在它的子树内再分别枚举两个点 $x,y$，暴力求解答案。时间复杂度 $O(n^3)$。

没取模寄了。

考虑一个 $O(n^2\log n)$ 的做法，在上一个做法中我们对每个点都枚举了子树内的两个点。可以反过来思考每两个点对哪些结点有贡献。产生贡献的充要条件是它俩在同一个子树内。显然只要是以 $lca$ 及以上的结点为根的子树都可以同时被包含，我们只需要在 $lca$ 处记录贡献，再从叶子向上合并就行。可以拿 70 分。

#### 代码
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
	freopen("distance.in","r",stdin);
	freopen("distance.out","w",stdout);
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