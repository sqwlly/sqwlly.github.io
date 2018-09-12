---
title: ACM-ICPC 2018 徐州赛区网络预赛 H Ryuji doesn't want to study(线段树 两种做法)
comments: false
date: 2018-09-12 13:23:10
tags: [ICPC,线段树]
categories: 
- [ICPC]
- [线段树]
---
# [Ryuji doesn't want to study](https://nanti.jisuanke.com/t/31460)
# 问题分析
我们可以做一个前缀和$sum\{a[i]\}$和一个前缀和$sum\{(n-i)*a[i]\}$(看起来就像一个三角形，我们这里称之为$triangle[i]$)，如图所示。
<!--more-->
![这里写图片描述](https://img-blog.csdn.net/20180909231038833?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0V0ZXJuYWxseTgzMTE0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
假设我们要求区间$[2,5] = 2\cdot4+3\cdot3+4\cdot2+5\cdot1$，那么，我们通过图中可以发现答案就是梯形的数值减去平行四边形的数值。即$$query(2,5) = (triangle[5]-triangle[1]) -(7-5)\cdot(sum[5]-sum[1])$$
因此我们只需要用线段树维护两个前缀和即可。

------------------------
这里蒻再补充一个做法。就是线段树维护前缀和的区间和。
我们将题里的要求分解一下。
假设仍然要求$query(2,5)$，
分解开来就会发现$$\begin{align}
 query(2,5) & =  (2 + 3 + 4 + 5 )+ (2 + 3 + 4)+(2+3)+(2) \\\\
 & =  (sum[5]-sum[1]) + (sum[4]-sum[1])+(sum[3]-sum[1])+(sum[2]-sum[1]) \\\\
 & = \sum_{i=2}^5 sum(i)-(5-3+1)\cdot sum(1)
\end{align}$$
所以题目所求变为$$
	query(l,r) = \sum_{i=l}^rsum(i)-(r-l+1)\cdot sum(l-1)
$$

# 代码(第一种做法)

```c++
#include <cstdio>
using namespace std;
typedef long long LL;
const int N = 1e6+4;
int n,q,a[N];
struct data {
    int l, r;
    LL val, triangle;
} tr[N<<1];

inline void pushup(int rt) {
    tr[rt].val = tr[rt << 1].val + tr[rt << 1 | 1].val;
    tr[rt].triangle = tr[rt << 1].triangle + tr[rt << 1 | 1].triangle;
}

void build(int k,int s,int t) {//建树
    tr[k].l = s;
    tr[k].r = t;
    if (s == t) {
        tr[k].val = a[s];
        tr[k].triangle = a[s] * 1LL * (n - s + 1);
        return;
    }
    int mid = (s + t) >> 1;
    build(k << 1, s, mid);
    build(k << 1 | 1, mid + 1, t);
    pushup(k);
}

LL query(int k,int x,int y,int len) {//区间求和
    int L = tr[k].l, R = tr[k].r;
    if (x == L && y == R)
        return tr[k].triangle - 1LL * len * tr[k].val;
    int mid = (L + R) >> 1;
    if (y <= mid)
        return query(k << 1, x, y, len);
    else if (x > mid)
        return query(k << 1 | 1, x, y, len);
    else
        return query(k << 1, x, mid, len) + query(k << 1 | 1, mid + 1, y, len);
}

void update(int rt,int L,int R,int x,LL v) {
    if (L == R) {
        tr[rt].triangle = (n - x + 1) * 1LL * v;
        tr[rt].val = v;
        return;
    }
    int mid = (L + R) >> 1;
    if (x <= mid)
        update(rt << 1, L, mid, x, v);
    else
        update(rt << 1 | 1, mid + 1, R, x, v);
    pushup(rt);
}

int main() {
    scanf("%d%d", &n, &q);
    for (int i = 1; i <= n; i++)
        scanf("%d", &a[i]);
    build(1, 1, n);
    int op, x, y;
    for (int i = 1; i <= q; i++) {
        scanf("%d%d%d", &op, &x, &y);
        if (op == 2)
            update(1, 1, n, x, y);
        else
            printf("%lld\n", query(1, x, y, n - y));
    }
    return 0;
}
```
# 代码(第二种做法)
```c++
#include <cstdio>
#define L tree[rt].l
#define R tree[rt].r
typedef long long LL;
using namespace std;
const int N = 1e6+4;
int a[N],n,q;
LL sum[N];
struct data {
        int l,r;
        LL Inc,nSum;
} tree[N];

void pushup(int rt)
{
        tree[rt].nSum = tree[rt<<1].nSum+tree[rt<<1|1].nSum;
}

void pushdown(int rt) {
        if(tree[rt].Inc) {
                int mid = (L+R)>>1;
                tree[rt<<1].Inc += tree[rt].Inc;
                tree[rt<<1|1].Inc += tree[rt].Inc;
                tree[rt<<1].nSum += tree[rt].Inc*(mid-L+1);
                tree[rt<<1|1].nSum += tree[rt].Inc*(R-mid);
                tree[rt].Inc = 0;
        }
}

void build(int rt,int x,int y) {//建树
        tree[rt].l = x; tree[rt].r = y;
        if(x == y) {
                tree[rt].Inc = 0;
                tree[rt].nSum = sum[x];
                return;
        }
        int mid = (x + y) >> 1;
        build(rt<<1,x,mid);
        build(rt<<1|1,mid+1,y);
        pushup(rt);
}

LL query(int rt,int x,int y){//区间求和
        if(x == L && y == R)
                return tree[rt].nSum;
        pushdown(rt);
        int mid=(L+R)>>1;
        if(y<=mid)
                return query(rt<<1,x,y);
        else if(x>mid)
                return query(rt<<1|1,x,y);
        else
                return query(rt<<1,x,mid)+query(rt<<1|1,mid+1,y);
}

void update(int rt,int x,int y,LL v)
{
        if(x == L && R == y) {
                tree[rt].nSum += v*1LL*(R-L+1);
                tree[rt].Inc += v;
                return;
        }
        pushdown(rt);
        int mid = (L+R)>>1;
        if(y <= mid)
                update(rt << 1, x, y,v);
        else if(x > mid)
                update(rt << 1 | 1, x, y,v);
        else{
                update(rt << 1, x, mid,v);
                update(rt << 1 | 1, mid+1, y,v);
        }
        pushup(rt);
}


int main(){
        scanf("%d%d",&n,&q);
        for(int i=1; i<=n; i++) {
                scanf("%d",&a[i]);
                sum[i] = sum[i-1] + a[i];
        }
        build(1,1,n);
        int op,x,y;
        for(int i=1; i<=q; i++) {
                scanf("%d%d%d",&op,&x,&y);
                if(op==2) {
                        update(1,x,n,1LL*(y-a[x]));
                        a[x] = y;
                }
                else{
                        if(x==1) {
                                printf("%lld\n", query(1,x,y));
                        }else
                                printf("%lld\n", query(1,x,y)-1LL*(y-x+1)*query(1,x-1,x-1));
                }
        }
        return 0;
}

```
