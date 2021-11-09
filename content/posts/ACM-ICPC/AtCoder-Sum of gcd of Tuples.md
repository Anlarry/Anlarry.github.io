---
title: AtCoder-Sum of gcd of Tuples
date: 2020-04-25T00:41:00+08:00
tags: ["数论", "搬家"]
categories: ACM/ICPC
---

# AtCoder-Sum of gcd of Tuples (Hard)

## 题意

>https://atcoder.jp/contests/abc162/tasks/abc162_e

求$\sum\gcd(a_1,a_2,\cdots,a_n)$,其中$a_i\in[1,K]$

## Solution

直接计算肯定是不好计算的，可以考虑按$gcd$的值进行分类，问题就转化为一个计数问题

> $\displaystyle \gcd(a,b)=d\Rightarrow\gcd(\frac{a}{d},\frac{b}{d})=1$
> 
> $\displaystyle \{\gcd(a_1,\cdots, a_n)=d的数量\}=\{\gcd(\frac{a_1}{d},\cdots,\frac{a_n}{d})=1的数量\}$

那么，

$$
Ans=\sum_{d=1}^{K}dF(\lfloor\frac{K}{d}\rfloor, N)
$$

其中$F(K,N)$表示$\gcd(a_1,\cdots,a_N)=1$的个数，$a_i\in[1,K]$

可以用容斥算出$\displaystyle F(K,N)=K^N-\sum_{i=1}^{K}F(\lfloor\frac{K}{i}\rfloor)$

```c++
#include <cstdio>
#include <stack>
#include <set>
#include <cmath>
#include <map>
#include <time.h>
#include <vector>
#include <iostream>
#include <string>
#include <cstring>
#include <algorithm>
#include <memory.h>
#include <cstdlib>
#include <queue>
#include <iomanip>
// #include <unordered_map>
#define P pair<int, int>
#define LL long long
#define LD long double
#define PLL pair<LL, LL>
#define mset(a, b) memset(a, b, sizeof(a))
#define rep(i, a, b) for (int i = a; i < b; i++)
#define PI acos(-1.0)
#define random(x) rand() % x
#define debug(x) cout << #x << " " << x << "\n"
using namespace std;
const int inf = 0x3f3f3f3f;
const LL __64inf = 0x3f3f3f3f3f3f3f3f;
#ifdef DEBUG
const int MAX = 1e6 + 50;
#else
const int MAX = 1e6 + 50;
#endif
const int mod = 1e9 + 7;

LL N,K;
LL f[MAX];
LL fact[MAX];
inline LL add(LL x, LL y){
    LL res = x + y;
    return res >= mod ? res - mod : res;
}
inline LL qpow(LL x, LL n){
    LL res = 1;
    while (n)
    {
        if(n &1)
            res = res * x % mod;
        x = x * x % mod;
        n >>= 1;
    }
    return res;
}
LL F(LL K, LL N){
    if(f[K]) return f[K];
    LL &res =f[K];
    if(K==1){
        return res = 1;
    }
    // res = qpow(K, N);
    res = fact[K];
    for(LL i = 2, j; i <= K; i=j+1){
        j = K/(K/i);
        // res = add(res, mod-F(K/i, N));
        LL tmp = (j-i+1LL) * F(K/i, N) % mod;
        res = add(res, mod-tmp);                                                                    
    }
    return res;
}
int main(){
#ifdef DEBUG
    freopen("in", "r", stdin);
#endif
    scanf("%lld%lld", &N, &K);
    for(LL i = 1; i <= K; i++)
        fact[i] = qpow(i, N);
    LL ans = 0;
    f[1] = 1LL;
    for(LL k= 1; k <= K; k++){
        F(k, N);
    }
    for(LL i = 1, j; i <= K; i++){
        ans +=  f[K/i] % mod * i % mod;
        if(ans >= mod)
            ans -= mod;
    }
    printf("%lld\n", ans);
}

```