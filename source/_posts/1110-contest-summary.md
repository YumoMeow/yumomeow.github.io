---
title: 11.11 模拟赛总结
date: 2025-11-11 19:41:00
excerpt: mhx布置的模拟赛题。。
categories: 
    - 总结
tags: 
    - 数据结构
    - 树状数组
    - 动态规划
    - 数学
comment: 'utterances'
---

[file](/files/1111down.zip)

100+80+40=220pts，很幸运地没挂分。

T1 的意思就是给你一个数列，让你求所有区间的非严格次大值之和，很容易想到枚举次大值，把它左右两边第一个比它大的数分别作为最大值，并找出来满足他俩作为区间内最大的两个数的极大子区间算一下贡献就行了。找区间的话你选左边作为最大值，右端点直接是右边第一个比它大的数减去一就行了，左端点暴力扩展。看上去暴力扩展复杂度比较高，但能证明它是跟 $O(n)$ 差不多的，每次走的步数都很少，所以能过。一开始在想各种神秘的分治 dp 乱七八糟的做法，突然想到直接这样写也能过，就这么写了。

小细节，你在找数的时候左边要找第一个大于它的数，右边只需找第一个大于等于它的数，这样就不会算重复了。

```cpp
#include<iostream>
using namespace std;
const int N=2e5+10,MOD=998244353;
int n,L[N],R[N],s[N],top;
long long a[N];
int solve(){
    top=s[0]=0;
    cin>>n;
    for(int i=1;i<=n;i++){
        cin>>a[i];
        while(top&&a[s[top]]<a[i]) top--;
        L[i]=s[top];
        s[++top]=i;
    }
    top=0;s[0]=n+1;
    for(int i=n;i>=1;i--){
        while(top&&a[s[top]]<=a[i]) top--;
        R[i]=s[top];
        s[++top]=i;
    }
    long long ans=0;
    for(int i=1;i<=n;i++){
        int l,r,p1,p2;
        if(L[i]){
            r=R[i]-1,l=L[i];
            p1=L[i],p2=i;
            while(a[l-1]<a[i]&&l>1) l--;
            (ans+=(a[i]+MOD)%MOD*(p1-l+1)%MOD*(r-p2+1)%MOD)%=MOD;            
        }
        if(R[i]!=n+1){
            l=L[i]+1,r=R[i];
            p1=i,p2=R[i];
            while(a[r+1]<=a[i]&&r<n) r++;
            (ans+=(a[i]+MOD)%MOD*(p1-l+1)%MOD*(r-p2+1)%MOD)%=MOD;
        }
    }
    cout<<ans<<'\n';
    return 0;
}
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    freopen("polka.in","r",stdin);
    freopen("polka.out","w",stdout);
    int c,t;
    cin>>c>>t;
    while(t--) solve();
    return 0;
}
```

T2 很神奇。给你一个 $n*n$ 的字符矩阵让你找从上往下或者从左往右的 `XYD` 子串，字串间不能重合问最多能找到几个。先说我考试的时候做法，一看数据范围有 80 分是 $n\le 500$ 的，感觉网络流能做，于是最后就只拿了网络流这 80 分。具体来说，把每个点拆成入点和出点连容量为 $1$ 的边保证只能用一次，再把 XYD 分成三列，X 连原点 D 连汇点，中间相邻的字母连边。比如 Y 上面是 X 就连一条 X 指向 Y 的。但这样有个问题，就是拐弯的那种也会算进去，于是你只需要在原矩阵上找出来所有符合要求的 `XYD` 直接加到图上就能避免了，甚至比上面那样建边简单一点。建出来显然答案就等于最大流。

正解是 dp。原题给了一个部分分是所有 $i+j$ 相等的点 $(i,j)$ 都是同一个字符，也就是对角线和与它平行的斜线上都是同一个字符。这个部分分很好拿，然后你可以获得一些启示。你观察到两个 `XYD` 有可能冲突当且仅当它俩的 `Y` 在同一条斜线上，才有可能形成诸如下面的这两种冲突。

![](/img/1110-summary/p1.png?400x)

这样你就可以**斜着 DP**！！对于每条斜线，设 $f_{i,0/1/2}$ 表示当前在斜线上的第 $i$ 个点，这个点不放 / 横着放 / 竖着放。$f_{i,1}$ 有值当且仅当这个点和他左右两个点能形成一个 `XYD`，$f_{i,2}$ 同理。这样你就可以转移。

$$
f_{i,0}=max(f_{i-1,0},f_{i-1,1},f_{i-1,2})\\
f_{i,1}=max(f_{i-1,0},f_{i-1,1})+1\\
f_{i,2}=max(f_{i-1,0},f_{i-1,2})+1
$$
就过了。

```cpp
#include<iostream>
#include<cstring>
using namespace std;
const int N=3010;
int n,dp[N][3];
char mp[N][N];
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    freopen("match.in","r",stdin);
    freopen("match.out","w",stdout);
    cin>>n;
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            cin>>mp[i][j];
        }
    }
    int ans=0;
    for(int p=1;p<n<<1;p++){
        memset(dp,0,sizeof(dp));
        int ma=0;
        for(int i=1;i<p&&i<=n;i++){
            int j=p-i;if(j>n) continue;
            dp[i][0]=max(dp[i-1][0],max(dp[i-1][1],dp[i-1][2]));
            if(mp[i][j]=='Y'&&mp[i-1][j]=='X'&&mp[i+1][j]=='D'){
                dp[i][1]=max(dp[i-1][0],dp[i-1][1])+1;
            }
            if(mp[i][j]=='Y'&&mp[i][j-1]=='X'&&mp[i][j+1]=='D'){
                dp[i][2]=max(dp[i-1][2],dp[i-1][0])+1;
            }
            ma=max(max(ma,dp[i][0]),max(dp[i][1],dp[i][2]));
        }
        ans+=ma;
    }
    cout<<ans;
    return 0;
}
```

T3 题意如下。定义一个数 $a$ 是好的，当且仅当存在非负整数 $x,y(y>1)$ 使得 $a=x^y$。给定 $T$ 组询问，每次给定 $a,b,c,d$，你需要求出满足以下条件的数对 $(i,j)$ 的个数：$i\in [a,b],j\in [c,d],lcm(i,j)$ 是好的。

赛时暴力拿了 40 分，直接讲正解。

因为 $T,a,b,c,d$ 的范围都是 $2\times 10^5$，所以你可以换个角度考虑，先把所有满足 $lcm(a,b)$ 是好的的 $a,b$ 数对求出来放到一个二维平面上，剩下的就是一个二维数点问题可以拿一个树状数组扫描线来做。然后你发现这个数对并不是很多，是 $O(n)$ 级别的，你就开始想怎么求。

把 $a,b$ 分别质因数分解，得到

$$
a=p_1^{\alpha_1}\times p_2^{\alpha_2}\times \dots\\
b=p_1^{\beta_1}\times p_2^{\beta_2}\times \dots\\
lcm(a,b)=p_1^{\max(\alpha_1,\beta_1)}\times p_2^{\max(\alpha_2,\beta_2)}\times \dots
$$

那么，$lcm(a,b)$ 是好的只需所有的 $\max(\alpha,\beta)$ 有一个大于 $1$ 的 $gcd$，这样你就可以同时把它提出来放到整个式子的指数的位置上，显然就满足要求了。

所以你可以 dfs，传进去四个参数 $p,x,y,g$ 表示当前到第 $p$ 个质数，指数的最大公约数为 $g$，然后你枚举下一步的指数再保证 $gcd>1$ 就可以求出来所有满足的点对了，再做一遍扫描线就行。

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
const int N=1e6,M=1010;
int q,cnt,cnt1,p[M],pw[M][21];
int bit[N+5],ans[N+5];
bool vis[M];
struct Line{
    int x,y1,y2,id,tag;
    bool operator < (const Line &t) const{
        return x<t.x;
    }
}l[(N<<1)+5];
struct Good{
    int x,y;
    bool operator < (const Good &t) const{
        return x<t.x;
    }
}gd[N<<2];
int lowbit(int x){return x&-x;}
void add(int x){while(x<=N) bit[x]++,x+=lowbit(x);}
int query(int x){
    int res=0;
    while(x>0) res+=bit[x],x-=lowbit(x);
    return res;
}
int gcd(int x,int y){
    if(!y) return x;
    return gcd(y,x%y);
}
void dfs(int u,int x,int y,int g){
    if(g==1) return;
    if(u>cnt){
        gd[++cnt1]=(Good){x,y};
        return;
    }
    int maxx=0,maxy=0;
    while(x*pw[u][maxx+1]<=N) maxx++;
    while(y*pw[u][maxy+1]<=N) maxy++;
    if(max(maxx,maxy)<=1){
        gd[++cnt1]=(Good){x,y};
        return;
    }
    for(int i=0;i<=maxx;i++){
        for(int j=0;j<=maxy;j++){
            dfs(u+1,x*pw[u][i],y*pw[u][j],gcd(g,max(i,j)));
        }
    }
}
int main(){
    ios::sync_with_stdio(0);
    cin.tie(0);cout.tie(0);
    freopen("good.in","r",stdin);
    freopen("good.out","w",stdout);
    for(int i=2;i<=1000;i++){
        if(!vis[i]) p[++cnt]=i;
        for(int j=1;j<=cnt&&i*p[j]<=1000;j++){
            vis[i*p[j]]=1;
            if(!(i%p[j])) break;
        }
    }
    for(int i=1;i<=cnt;i++){
        pw[i][0]=1;
        for(int j=1;j<=20;j++){
            pw[i][j]=pw[i][j-1]*p[i];
            if(pw[i][j]>N) break;
        }
    }
    dfs(1,1,1,0);
    cin>>q;
    for(int i=1,x1,y1,x2,y2;i<=q;i++){
        cin>>x1>>x2>>y1>>y2;
        l[i]=(Line){x1-1,y1,y2,i,-1};
        l[i+q]=(Line){x2,y1,y2,i,1};
    }
    sort(gd+1,gd+cnt1+1);
    sort(l+1,l+q*2+1);
    int pp=1,qq=1;
    for(int i=0;i<=N;i++){
        while(gd[pp].x==i&&pp<=cnt1) add(gd[pp++].y);
        while(l[qq].x==i){
            ans[l[qq].id]+=l[qq].tag*(query(l[qq].y2)-query(l[qq].y1-1));
            qq++;
        } 
    }
    for(int i=1;i<=q;i++) cout<<ans[i]<<'\n';
    return 0;
}
```

出现了以下问题，一是 T2 没仔细去观察部分分的做法，导致没有想起来斜着 dp 的方法，其实部分分能给我们很大的启发。二是 T3 看着这么绕就没仔细想直接写的暴力，对质因数分解和公约数等数学技巧还有欠缺，而且缺乏对题目的转换能力。其实你转换成一个二维数点就挺好做了。