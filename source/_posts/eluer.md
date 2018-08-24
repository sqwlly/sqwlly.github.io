---
title: 关于欧拉定理的一些知识
date: 2018-08-23 09:29:47
tags: [数论,欧拉定理,欧拉降幂]
categories: [数论,欧拉定理]
---

# [欧拉定理](https://baike.baidu.com/item/%E6%AC%A7%E6%8B%89%E5%AE%9A%E7%90%86/891345?fr=aladdin)
$$a^{\varphi(n)}\equiv 1(mod\:n),\:gcd(a,n)=1$$

对于正整数$n$，代表小于等于$n$的与$n$互质的数的个数，记作$\varphi(n)$。
比如$\varphi(6)=2$，因为与$6$互质并且小于等于$6$的正整数有$1,5$。
# 扩展欧拉定理(降幂公式)
$$  a^b \equiv
\begin{cases}
a^{b \%\varphi(p)},  & \text{gcd(a,p)$\:$=$\:$1}\\\\  
a^b, & \text{gcd(a,p)$\:$$\not=$$\:$1,$\: b\:$< $\varphi$(p)}\\\\
a^{b \%\varphi(p)+\varphi(p)},&\text{gcd(a,p)$\:$$\not=$$\:$1,$\: b\:$$\geq$ $\varphi$(p)}\end{cases}
\pmod p$$  

除此之外呢，欧拉定理有以下几个性质，
$1.\:$如果$n$为某一素数$p$，则有$\varphi(p)=p-1$。因为$p$为素数，因子只有$1$和$p$，而$p$和$p$不互质，所以$ \varphi(p)=p-1$。

实际上呢，欧拉定理是费马小定理的一种推广，我们利用性质$1$就可以很容易证明。
费马小定理：$a^{p-1}\equiv 1(mod\:p),gcd(a,p)=1$。因为$p$为质数，所以有$\varphi(p)=p-1$，代入欧拉定理即可。
推论：$a^p\equiv a(mod\:p)$，如果$a$能被$p$整除。

$2.\:$如果$n$为某一素数$p$的幂次，则有$\varphi(p^a)=(p-1)\cdot p^{a-1}$。因为比$p^a$小的正整数有$p^a-1$个，能被$p$整除的数有$p^{a-1}-1$个（将$1\to p^a-1$之间$p$的倍数筛去），所以$\varphi(p^a)=p^a-1-(p^{a-1}-1)=(p-1)\cdot p^{a-1}$。

$3.\:$如果$n$为任意两个正整数$a$和$b$的乘积（$a$和$b$互质），那么有$\varphi(a\cdot b)=\varphi(a)\cdot \varphi(b)$。我们设$x=\phi(i)$（即和$a\cdot b$互质的数），那么就有
$$(S):\begin{cases}
x_1\equiv t_1(mod\:a) \:(gcd(t_1,a)=1)\\
x_2\equiv t_2(mod\:b) \:(gcd(t_2,b)=1)
\end{cases}$$那么我们根据[中国剩余定理](https://baike.baidu.com/item/%E5%AD%99%E5%AD%90%E5%AE%9A%E7%90%86/2841597?fromtitle=%E4%B8%AD%E5%9B%BD%E5%89%A9%E4%BD%99%E5%AE%9A%E7%90%86&fromid=11200132&fr=aladdin)可知，对于任意$t_1,t_2$，方程组$(S)$的解在区间$[1,a\cdot b)$有唯一解与之对应。$t_1$的取值有$\varphi(a)$个，$t_2$的取值有$\varphi(b)$个。
所以$\varphi(a\cdot b)=\varphi(a)\cdot \varphi(b)$。

$4.\:$设$n=p_1^{e_1}\cdot p_2^{e_2}\cdot p_3^{e_3}......p_k^{e_k}$（$p_i$为素数），则有
$\varphi(n)=n\cdot(1-\frac{1}{p_1})\cdot(1-\frac{1}{p_2})\cdot(1-\frac{1}{p_3})......(1-\frac{1}{p_k})$。
根据性质$2$和性质$3$就可以很好的推出：因为$p_i$都为素数，所以每一个$p_i$都是互质的，所以同样$p_i^{e_i}$也是互质的。因此由性质$2$：$\varphi(p_i^{e_i})=p_i^{e_i}-1-(p_i^{e_i-1}-1)=p_i^{e_i}\cdot (1-\frac{1}{p_i})$，和性质$3$：$\varphi(p_1^{e_1}\cdot p_2^{e_2}\cdot p_3^{e_3}......p_k^{e_k})=\varphi(p_1^{e_1})\cdot \varphi(p_2^{e_2})\cdot \varphi(p_3^{e_3})......\varphi(p_k^{e_k})$，可以推出$\varphi(n)=n\cdot(1-\frac{1}{p_1})\cdot(1-\frac{1}{p_2})\cdot(1-\frac{1}{p_3})......(1-\frac{1}{p_k})$。

# 欧拉函数的线性筛法
根据如下三个性质可以完成线性筛法。
$1.\:\:\varphi(p)=p-1$
$2.\:\:\varphi(p\cdot i)=p\cdot \varphi(i)\:\:(p\%i=0)$
$3.\:\:\varphi(p\cdot i)=(p-1)\cdot \varphi(i)\:\:(p\%i\not=0)$
(具体证明就不证啦啦～作为模板使用就好啦^_^)

```C++
void initPhi(int n)
{
	phi[1] = 1; //φ(1) = 1
	for(int i = 2; i <= n; ++i){
		if(!vis[i]){
			phi[i] = i - 1; //性质1
			prime[++cnt] = i;
		}
		for(int j = 1; j <= cnt; ++j){
			if(i * prime[j] > n) break;
			vis[i * prime[j]] = 1;
			if(i % prime[j] == 0){ //性质2
				phi[i * prime[j]] = phi[i] * prime[j];
				break;
			}
			phi[i * prime[j]] = phi[i] * phi[prime[j]]; //性质3
		}
	}
}

```

