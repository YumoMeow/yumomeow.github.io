---
title: 题解：CF1009G - Allowed Letters
date: 2025-03-24 09:18:00
excerpt: 图论 二分图
categories: 
    - 题解
tags: 
    - 图论
    - 二分图
---
### 题意
给定一个长为 $n$ 的串，字符集 `a` 到 `f`。你可以重排这个串，满足指定 $m$ 个位置上只能放特定的字符，求字典序最小的串。无解输出 `Impossible`。

### 题解
考虑网络流，每个位置向可能出现的字符连边，跑最大流看能否流满即可，但这样很难找到字典序最小的匹配。

显然字典序问题都可以贪心考虑，对每一位尽量放更小的字符，**判断剩下的位置能否满足条件**，考虑解决这个问题。以位置为左部，原串的字符为右部建立二分图，实际上是在求一个二分图完美匹配，对于这个问题我们有 Hall 定理：
> 对于二分图 $G=(A,B,E),|A|\le |B|$，定义 $f(S)$ 表示与点集 $S(S\subseteq A)$ 中的点有连边的点的数量，则 $G$ 存在完美匹配的充要条件是 $\forall S\subseteq A,f(S)\ge |S|$。

因为字符集只有 `a...f`，枚举所有子集只有 $2^6$ 种情况，对于每个 $i$ 枚举并判断即可。关于 $f$ 值，从后往前枚举则考虑后 $i$ 个字符的 $f$ 值可以从考虑后 $i+1$ 个字符的 $f$ 值转移而来。

复杂度 $O(6\times 2^6n)$。

### 代码
```cpp
#include<iostream>
#include<cstring>
using namespace std;
constexpr int N=1e5+10;
int n,m,cnt[6],sum[64],f[N][64];
bool lim[N][6];
string s,ans;
signed main(){
    cin>>s; 
    n=s.size();
    for(auto c:s) cnt[c-'a']++;
    for(int i=0;i<64;i++){
        for(int j=0;j<=5;j++){
            if(i>>j&1) sum[i]+=cnt[j];
        } 
    }
    cin>>m;
    memset(lim,1,sizeof(lim));
    for(int i=1;i<=m;i++){
        int p; 
        cin>>p>>s;
        for(int j=0;j<=5;j++) lim[p][j]=0;
        for(auto c:s) lim[p][c-'a']=1;//每个位置能放的字符
    }
    for(int i=n;i>=1;i--){
        for(int j=0;j<64;j++){
            f[i][j]=f[i+1][j];
            for(int k=0;k<=5;k++){
                if((j>>k&1)&&lim[i][k]){
                    f[i][j]++;
                    break;//预处理 f 值
                }          
           }
        } 
    }
    for(int i=1;i<=n;i++){
        bool fll=0;
        for(int j=0;j<=5;j++){
            if(!lim[i][j]) continue;
            bool fl=1;
            for(int k=0;k<64;k++){
                if(k>>j&1){
                    if(f[i+1][k]<sum[k]-1){ 
                        fl=0; 
                        break; //hall 定理判断
                    }
                }
                else if(f[i+1][k]<sum[k]){ 
                    fl=0; 
                    break; 
                }                
            }
            if(fl) {
                fll=1; ans.push_back(j+'a');
                for(int k=0;k<64;k++) if(k>>j&1) sum[k]--;
                break;
            }
        }
        if(!fll) cout<<"Impossible\n",exit(0);
    }
    cout<<ans;
    return 0;
}
```
