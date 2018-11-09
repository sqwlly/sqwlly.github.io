---
title: ZOJ 4062 Plants vs. Zombies(二分+贪心) 2018ACM-ICPC青岛现场赛E
comments: true
date: 2018-11-08 23:00:41
tags: [贪心,二分]
categories:
- [贪心]
- [二分]
---
# [Plants vs. Zombies](http://acm.zju.edu.cn/onlinejudge/showProblem.do?problemId=5819)
题解：首先肯定可以想到走过去再回来是肯定浪费步数的，最多的只能是相邻的两个植物来回，大致就呈一个$Z$字形走过，最后要注意最后一步停在哪里。其次最大化最小值，二分即可。(深深吐槽$\%$，$2000ms$刚好卡过$2333333$，然后再交就$tle$，最后改成$ceil$才稳的)
<!--more-->
# 代码

```c
#include<bits/stdc++.h>
typedef long long LL;
using namespace std;
const int N = 100100;
int a[N], n;
LL plant[N], m;
bool love(LL minH)
{
	memset(plant, 0, sizeof plant);
	for(int i = 1; i <= n; ++i) {
		if(m > 0) {
			plant[i] += a[i];
			m--;
		}
		if(m <= 0) break;
		if(plant[i] >= minH) continue;

		if(minH > plant[i]) {
			LL cnt = 1LL * ceil((minH - plant[i]) * 1.0 / a[i]);
			if(cnt * 2 <= m) {
				m -= cnt * 2;
				plant[i] += a[i] * cnt;
				plant[i + 1] += a[i + 1] * cnt;
			}else break;
			if(m <= 0) break;
		}
	}
	for(int i = 1; i <= n; ++i)
		if(plant[i] < minH) return 0;
	return 1;
}

int main()
{
#ifndef ONLINE_JUDGE
    freopen("input.in","r",stdin);
#endif
	int T;
	scanf("%d",&T);
	while(T--) {
		scanf("%d %lld",&n,&m);
		memset(a, 0, sizeof a);
		LL l = 1, r = 0, ans = 0, t = m;
		for(int i = 1; i <= n; ++i) {
			scanf("%d", a + i);
			r = max(r, a[i] * m);
		}
		if(m == 0) {
			puts("0");
			continue;
		}
		while(l <= r) {
			LL mid = l + (r - l) / 2;
			m = t;
			if(love(mid)) {
				ans = mid;
				l = mid + 1;
			}else{
				r = mid - 1;
			}
		}
		printf("%lld\n", ans);
	}

    return 0;
}

```

