---
title: Educational Codeforces Round 53 (Rated for Div. 2)D(模拟)
date: 2018-10-27 00:29:20
tags: [模拟]
comments: false
categories:
- [Codeforces]
- [模拟]
---
# [D. Berland Fair](https://codeforces.com/contest/1073/problem/D)
题解：考虑到每次都会有重复过程。因此我们首先可以算出一轮下来的花费和收获，然后算出这一次会循环多少轮，再给$T$对一轮的花费取模就可以算出剩下的钱。以此反复，继续算下一次的花费和收获。
<!--more-->
# 代码

```c
#include<bits/stdc++.h>
typedef long long LL;
using namespace std;
const int N = 200100;
LL T;
int a[N], n;
int main()
{
#ifndef ONLINE_JUDGE
    freopen("input.in","r",stdin);
#endif
	scanf("%d %lld",&n,&T);
	LL Min = 1e18;
	for(int i = 0; i < n; ++i){
		scanf("%d",&a[i]);
		Min = min(Min,1LL*a[i]);
	}
	LL ans = 0;
	for( ; Min <= T; ){
		LL cnt = 0, ret = 0;
		for(int i = 0; i < n; ++i){
			if(a[i] <= T)
				T -= a[i], ret += a[i], cnt++;
		}
		ans += T / ret * cnt + cnt;
		T %= ret;
	}
	printf("%lld\n",ans);
    return 0;
}

```

