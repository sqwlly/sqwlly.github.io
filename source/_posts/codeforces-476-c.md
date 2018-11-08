---
title: CodeForces - 476C-Dreamoon and Sums(数学思维)
comments: true
date: 2018-11-07 00:49:26
tags: [Codeforces,数论]
categories: 
- [Codeforces]
- [数论]
---
# [C. Dreamoon and Sums](http://codeforces.com/problemset/problem/476/C)
题解：设$$x = q\cdot b + r \tag{1}$$则有

$$div(x,b) = q ,\: mod(x,b) = r\in[1,b-1]\tag{2}$$
$$\frac{(1)}{r} =>  \frac{x}{r} = \frac{q\cdot b}{r} + 1 \tag{3}$$
<!--more-->
题目所求$$\frac{div(x,b)}{mod(x,b)}=>\frac{q}{r}=k\in[1,a]\tag{4}$$
可得$$x = (3) \cdot r = (k\cdot b+1) \cdot r\tag{5}$$
所以$$\sum{x} = \sum{(k\cdot b+ 1)} \cdot\sum{r}\tag{6}$$
$$\sum{x} = \frac{b\cdot (b - 1)}{2}\left[ (b+1)\cdot a + \frac{a\cdot (a - 1)}{2}\cdot b \right]\tag{7}$$

# 代码

```c
#include<bits/stdc++.h>
typedef long long LL;
using namespace std;
const int mod = 1e9+7;
int main()
{
#ifndef ONLINE_JUDGE
    freopen("input.in","r",stdin);
#endif
	LL a,b;
	cin>>a>>b;
	LL t = ((b + 1) * a ) % mod;
	LL tmp = (a * (a - 1) / 2) % mod;
	tmp = tmp * b % mod;
	tmp = (tmp + t) % mod;
	t = (b - 1) * b / 2 % mod;
	cout<<(t * tmp) % mod<<endl;
    return 0;
}

```

