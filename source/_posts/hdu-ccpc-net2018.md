---
title: CCPC网络预选赛1010(hdu)
date: 2018-08-25 23:21:29
tags: [树状数组,离散化]
categories: [树状数组]
---

# [巨大的传送门](http://acm.hdu.edu.cn/showproblem.php?pid=6447)

# 问题分析
题意：从$(0,0)$到$(10^9,10^9)$，每次只能往$(x+1,y),(x+1,y+1),(x,y+1)$三个方向走，期间有$n$个村庄，只有当从$(x-1,y-1)$走到当前村庄才会获得$v_k$元，问最后到达目标时的可以获得的最大钱数。
<!--more-->  
首先我们想到的就是$dp$，但是因为区间太大了，所以需要离散化一下，然后发现一般dp的复杂度是$n^2$的，肯定是要超时的，所以我们可以利用树状数组或者线段树去维护区间最大值，这样我们就可以$\Theta(nlog_n)$的复杂度求解了。
  
大致就是先按$x$排序，然后因为我们要用树状数组去维护这个区间最大值，所以考虑到$x$相同的时候，我们要尽量取$y$大的，所以排序的第二$key$就按照$y$从大到小排序。然后排完序就给$y$去离散化一下，根据离散化好的序列去$dp$。
# 代码
```c++
#include <bits/stdc++.h>

#define lowbit(x) ((x)&(-(x)))
#define max(a, b) (((a)>(b))?(a):(b))
using namespace std;
const int N = 1e5 + 10;
struct node {
    int x, y, w;
} a[N];
int bit[N], shit[N];

bool cmp(const node &a, const node &b) {
    if (a.x == b.x)
        return a.y > b.y;
    return a.x < b.x;
}

void update(int x, int v) {
    for (int i = x; i < N; i += lowbit(i))
        bit[i] = max(v, bit[i]);
}

int query(int d) {
    int ans = 0;
    for (int i = d; i; i -= lowbit(i))
        ans = max(ans, bit[i]);
    return ans;
}

int main() {
    int T, n, x, y, w;
    scanf("%d", &T);
    while (T--) {
        memset(bit, 0, sizeof bit);
        vector<int> id;
        scanf("%d", &n);
        for (int i = 0; i < n; ++i) {
            scanf("%d%d%d", &x, &y, &w);
            a[i].x = x, a[i].y = y, a[i].w = w;
            id.push_back(y);
        }
        sort(id.begin(), id.end());
        id.erase(unique(id.begin(), id.end()), id.end());
        sort(a, a + n, cmp);
        for (int i = 0; i < n; ++i)
            shit[i] = lower_bound(id.begin(), id.end(), a[i].y) - id.begin() + 1;
        int love = 0;
        for (int i = 0; i < n; ++i) {
            //dp[i] = max{dp[0, i - 1]}+a[i].w;
            int fuck = query(shit[i] - 1) + a[i].w;
            update(shit[i], fuck);
            love = max(love, fuck);
        }
        printf("%d\n", love);
    }
}
```
