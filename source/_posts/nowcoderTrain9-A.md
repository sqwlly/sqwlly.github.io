---
title: 牛客多校第九场A(fwt-异或卷积)
comments: true
date: 2018-08-24 23:46:37
tags: [牛客,fwt,acm]
categories: [数论,位运算卷积]
---
# 题目描述
{% note default %}
Niuniu has recently learned how to use Gaussian elimination to solve systems of linear equations.
Given n and a[i], where n is a power of 2, let's consider an n x n matrix A.

The index of A[i][j] and a[i] are numbered from 0.
The element A[i][j] satisfies A[i][j] = a[i xor j],
https://en.wikipedia.org/wiki/Bitwise_operation#XOR

Let p = 1000000007.
Consider the equation 
A x = b (mod p)
where A is an n x n matrix, and x and b are both n x 1 row vector.

Given n, a[i], b[i], you need to solve the x.
For example, when n = 4, the equations look like
A[0][0]*x[0] + A[0][1]*x[1] + A[0][2]*x[2] + A[0][3]*x[3] = b[0] (mod p)
A[1][0]*x[0] + A[1][1]*x[1] + A[1][2]*x[2] + A[1][3]*x[3] = b[1] (mod p)
A[2][0]*x[0] + A[2][1]*x[1] + A[2][2]*x[2] + A[2][3]*x[3] = b[2] (mod p)
A[3][0]*x[0] + A[3][1]*x[1] + A[3][2]*x[2] + A[3][3]*x[3] = b[3] (mod p)
and the matrix A can be decided by the array a.

It is guaranteed that there is a unique solution x for these equations. {% endnote %}
<!-- more -->
# Input
{% note default %} 
The first line contains an integer, which is n.
The second line contains n integers, which are the array a.
The third line contains n integers, which are the array b.

1 <= n <= 262144
p = 1000000007
0 <= a[i] < p
0 <= b[i] < p
{% endnote %} 
# Output
{% note default %}
The output should contains n lines.
The i-th(index from 0) line should contain x[i].
x[i] is an integer, and should satisfy 0 <= x[i] < p.
{% endnote %} 
# 问题分析
{% note default %}
首先，我们通过观察题目得到$$A\times x = b \pmod p$$
将矩阵$A$展开
$$ \left[
\begin{array}{ccc}
  a_0&a_1&a_2&a_3\\\\
  a_1&a_0&a_3&a_2\\\\
  a_2&a_3&a_0&a_1\\\\
  a_3&a_2&a_1&a_0
\end{array}
\right] $$
我们可以发现这个矩阵左上角和右下角相同，左下角和右上角相同，矩阵再扩大亦是如此。
并且可以看出矩阵的全部信息只和第一行有关，恰好第一行就是数组$a$，所以实际上这是一个$fwt$(异或卷积)。
所以我们可以将问题转换成：给出向量$a$和$b$，$fwt(a)\cdot fwt(x) = fwt(b)$，求原向量$x$。

那么我们就可以做一次$fwt(a)$，做一次$fwt(b)$，然后$x[i] = \frac{b[i]}{a[i]}$，再做一次逆$fwt(x)$就好了啊。
{% endnote %} 
# 代码
```c++
#include <cstdio>
typedef long long LL;
const int N = 262150, p = 1e9+7;
LL a[N], b[N], x[N], n, inv2;
void fwt(LL *a) {
    for (int i = 1; 1 << i <= n; i++) {
        for (int j = 0; j < n; j += 1 << i) {
            for (int k = 0; k < 1 << (i - 1); k++) {
                LL x = a[j + k];
                LL y = a[j + k + (1 << (i - 1))];
                a[j + k] = (x + y) % p;
                a[j + k + (1 << (i - 1))] = (x + p - y) % p;
            }
        }
    }
}

void fwt(LL *a,int t) {
    for (int i = 1; i < n; i <<= 1) {
        for (int j = 0; j < n; j += (i << 1))
            for (int k = j; k < i + j; k++) {
                LL x = a[k], y = a[k + i];
                a[k] = (x + y) % p, a[k + i] = (x - y + p) % p;
                if (t)a[k] = a[k] * inv2 % p, a[k + i] = a[k + i] * inv2 % p;
            }
    }

}

LL Pow(LL a,LL n) {
    LL ans = 1;
    while (n) {
        if (n & 1) ans = a * ans % p;
        a = a * a % p;
        n >>= 1;
    }
    return ans % p;
}

int main() {
    scanf("%lld", &n);
    inv2 = Pow(2, p - 2);
    for (int i = 0; i < n; ++i)
        scanf("%lld", a + i);
    for (int i = 0; i < n; ++i)
        scanf("%lld", b + i);
    fwt(a);
    fwt(b);
    for (int i = 0; i < n; ++i) {
        x[i] = b[i] * Pow(a[i], p - 2) % p;
    }
    fwt(x, 1);//逆fwt
    for (int i = 0; i < n; ++i)
        printf("%lld\n", x[i]);
//    实际上我们如果要算逆fwt也可以这样求
//    for (int i = 0; i < n; ++i)
//        printf("%lld\n", x[i] * Pow(n, p - 2) % p);
    return 0;
}
```
