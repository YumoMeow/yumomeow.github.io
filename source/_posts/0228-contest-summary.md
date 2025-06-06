---
title: 2.28 模拟赛总结
date: 2025-02-28 18:22:00
excerpt: wyq布置的模拟赛题。。
categories: 
    - 总结
tags: 
	- 数据结构
	- 线段树
	- 分块
---

## [A. CF1179C - Serge and Dining Room](https://codeforces.com/problemset/problem/1179/C)
**线段树**
> 有 $n$ 道菜和 $m$ 个学生，每个菜有价格 $a_i$，每个学生有 $b_i$ 元。学生按顺序买菜，每个人会买能买得起的最贵的菜，买不起就离开队伍。
>
> 每次修改某道菜的价格或者某个学生的钱数，求所有学生买完菜后剩下最贵的菜，如果不剩就输出 $-1$。

学生的顺序并不会影响答案。考虑两个学生 $i,j$，$b_i<b_j$，不管是 $i$ 先买还是 $j$ 先买都不会影响到他们买到的菜，最终答案也不会被影响。

实际上我们要求的就是一个最大的 $a_i$，使得买得起这盘菜的学生人数小于价格大于等于 $a_i$ 的菜的数量，这样大于等于 $a_i$ 的菜（除了这一个）都会被买走，剩下最大的就是 $a_i$。对于那些被买走的价格比 $a_i$ 小的菜，我们并不关心。

形式化地说，对于每个 $i$，设 $\ge i$ 的 $b_j$ 数量为 $f_i$， $\ge i$ 的 $a_j$ 数量为 $g_i$，那么我们要求的就是最大的满足 $f_i-g_i<0$ 的 $i$。

考虑加入一个 $a_i$ 对于 $f-g$ 会有什么影响。显然所有 $\le i$ 的 $g_i$ 会 $+1$，$(f_i-g_i)$ 相应减一。加入一个 $b_i$ 时，所有 $\le i$ 的 $f_i$ 会 $+1$，$f_i-g_i$ 相应加一。

建立一个线段树，对每个价格 $i$ 维护 $(f_i-g_i)$。加入$a_i,b_i$ 时就是前缀加减操作，修改时就是区间加减。查询时，只需要查最大的 $\le 0$ 的下标就可以。

**[标程](https://yumomeow.github.io/2025/02/24/std/#CF1179C)**

## [B. CF85D - Sum of Medians](https://codeforces.com/problemset/problem/85/D)
**线段树**

挺简单的一道题。。赛时没想出来

> 给定一个有序集合 $a$，有插入和删除操作，以及求
> $$
> \sum_{i\mod 5=3}^{i\le k} a_i
> $$

每次插入一个数，大于它的位置的数就会都左移一位（感性理解），那么我们不妨用线段树维护值域内每一个可能的值，每一个区间内记录 $\mod 5$ 余数为 $0-4$ 的每一个位置的和，点 $i$ 代表的区间余数为 $j$ 的和记为 $sum_{i,j}$，查询的时候查 $sum_{1,3}$ 即可。再维护一个 $cnt$ 代表区间内有值的位置有多少个，就可以写出 $pushup$ 的转移：
$$
cnt_u=cnt_{ls(u)}+cnt_{rs(u)} \\
sum_{u,i}=sum_{ls(u),i}+sum_{rs(u),(i-cnt_{ls(u)})\mod 5}
$$
不难理解。

这样插入和删除操作就变为了单点修改，改完 $pushup$ 就行。

值域为 $1e9$，要动态开点，注意空间。

**[标程](https://yumomeow.github.io/2025/02/24/std/#CF85D)**

## [C. LuoguP3071 - Seating](https://www.luogu.com.cn/problem/P3071)
**线段树**
> 有 $n$ 个座位，支持每次求有没有连续的 $p$ 个座位，若有将最左端的 $p$ 个座位占用，没有就扔掉。还要求支持清空区间 $[a,b]$ 内的座位，所有操作完成后输出有几个 $p$ 被扔掉了。

要求的是最长的空子区间大小是否 $\ge p$。这个与最大子段和的维护类似，分别维护包含区间左端点的最大长度 $lma$，包含右端点的最大长度 $rma$，以及整个区间的最大长度 $ma$。

对于 $lma$，如果左区间全空则为左区间长度加右区间的 $lma$，否则为左区间的 $lma$。$rma$ 同理。

$ma$ 取 $\max(ma_{ls},ma_{rs},lma_{rs}+rma_{ls})$ 即可。

维护一个懒标记，记录区间被清空/被占满。每次要查询最左端的 $p$ 个座位，所以查询时要先查左边，再查中间，最后查右区间。
```cpp
int query(int u,int l,int r,int k){
	pushdown(u,l,r);
	if(l==r) return l;
	if(tr[ls].ma>=k) return query(ls,l,mid,k);
	else if(tr[ls].rma+tr[rs].lma>=k) return mid-tr[ls].rma+1;
	else return query(rs,mid+1,r,k);
	//先选左边，再选中间，再选右边
}
```
**[标程](https://yumomeow.github.io/2025/02/24/std/#LuoguP3071)**


