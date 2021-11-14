---
title: CodeChef - PRIMEDST
date: 2020-03-01T12:09:00+08:00
tags: ["FFT", "搬家"]
categories: ACM/ICPC


---

# Prime Distance On Tree 

## 题意

> [Prime Distance On Tree](https://vjudge.net/problem/CodeChef-PRIMEDST)

给个树，从树上随机选取一对点$u,v$,求$\delta(u,v)$是素数的概率

## Solution

*可以从生成函数的角度考虑*

假设`rt`是一个树的树根，而且`rt`的深度是`d`，将树中节点的深度统计出来，记为$f_{rt，d}$，如果$u,v...$是`rt`的子节点，那么`rt`对答案的贡献就是生成函数中素数项的系数，那么问题就是怎么计算生成函数了

$$
    \sum_{u,v \in son(rt)} f_{u,1} * f_{v, 1}  
$$
这个式子中的素数项系数和与下式是相等的,计算$f$是很简单的
$$
\Big(f_{rt,0}^2 - \sum_{u \in son(rt) }f_{u,1}^2\Big) / 2
$$

为了确保复杂度不会太高，需要用点分治

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
const int MAX = 2e3 + 50;
#else
const int MAX = 1e5 + 50;
#endif
const int mod = 1e9 + 7;

#include <complex>
namespace Prime
{
    vector<int> prime;
    bool isprime[MAX<<1];
    void init(int n){
        mset(isprime, 1);
        isprime[0] = isprime[1] = 0;
        for(int i = 2; i < n; i++){
            if(isprime[i]){
                prime.push_back(i);
            }
            for(int j = 0; j < prime.size() and prime[j] * i < n; j++){
                isprime[i * prime[j]] = 0;
                if(i * prime[j] == 0) break;
            }
        }
    }
} // namespace namePrimPrime

namespace FFT
{
    complex<double> a[MAX<<2], b[MAX<<2];
    int rev[MAX];
    void fft(complex<double> *a, int n, int inv){
        int bit = 0;
        while((1<<bit) < n) bit++;
        for(int i = 0; i < n; i++){
            rev[i] = (rev[i>>1]>>1) | ((i & 1) << (bit-1));
            if(i < rev[i])
                swap(a[i], a[rev[i]]);
        }
        for(int mid = 1; mid < n; mid <<= 1){
            complex<double> wn(cos(PI/mid), inv*sin(PI/mid));
            for(int i = 0; i < n; i += (mid<<1)){
                complex<double> w(1, 0);
                for(int j = 0; j < mid; j++, w *= wn){
                    complex<double> u = a[i+j], t = w * a[i+j+mid];
                    a[i+j] = u + t, a[i+j+mid] = u - t;
                }
            }
        }
    } 
    void product(int n){
        fft(a, n, 1);
        fft(b, n, 1);
        for(int i = 0; i <= n; i++)
            a[i] *= b[i];
        fft(a, n, -1);
    }
    
} // namespace nameFFTFFT

vector<int> E[MAX];
bool vis[MAX];
// LL dep[MAX];
LL dep[MAX], dep_num[MAX];
int cnt;
int siz[MAX];
int dfsG(int u, int p, int& Min, int &rt, int n){
    siz[u] = 1;
    int Max = -1;
    for(int i = 0; i < E[u].size(); i++){
        int v = E[u][i];
        if(vis[v] or v == p) continue;
        siz[u] += dfsG(v, u, Min, rt, n);    
        Max = max(Max, siz[v]);
    }
    Max = max(Max, n - siz[u]);
    if(Max < Min){
        Min = Max, rt = u;
    }
    return siz[u];
}
int dfsN(int u, int p){
    int res = 1;
    for(int i = 0; i < E[u].size(); i++){
        int v = E[u][i];
        if(vis[v] or v == p) continue;
        res += dfsN(v, u);
    }
    return res;
}
int find_rt(int u){
    int Min = inf;
    int rt = -1;
    int n = dfsN(u, -1);
    dfsG(u, -1, Min, rt, n);
    return rt;
}
LL dfs(int u, int p, int d){
    // return max_dep
    dep[cnt++] = d;
    LL max_dep = d;
    for(int i = 0; i < E[u].size(); i++){
        int v = E[u][i];
        if(v == p or vis[v]) continue;
        max_dep = max(max_dep, dfs(v, u, d+1));
    } 
    return max_dep;
}
LL calcu(int u, int d){
    // mset(dep, 0);
    cnt = 0;
    dfs(u, -1, d);
    LL Max = 0;
    for(int i = 0; i < cnt; i++){
        dep_num[dep[i]]++;
        Max = max(Max, dep[i]);
    }
    LL res = 0;
    int N = 1;
    while(N <= Max * 2) N <<= 1;
    for(int i = 0; i < N; i++){
        if(i <= Max) FFT::a[i] = FFT::b[i] = complex<double>(dep_num[i], 0);
        else FFT::a[i] = FFT::b[i] = complex<double>(0, 0);
    }
    FFT::product(N);
    for(int i = 0; i < Prime::prime.size() and Prime::prime[i] <= 2*Max; i++){
        res += (LL)(FFT::a[Prime::prime[i]].real() / N + 0.5);
    }
    for(int i = 0; i < cnt; i++) dep_num[dep[i]]--;
    return res;
}
LL solve(int u){
    // vis[u] = 1;
    int rt = find_rt(u);
    vis[rt] = 1;
    LL res = calcu(rt, 0);
    for(int i = 0; i < E[rt].size(); i++){
        if(vis[E[rt][i]]) continue;
        res -= calcu(E[rt][i], 1);
        res += solve(E[rt][i]);
    }
    // for(int i = 0; i < E[rt].size(); i++){
    //     if(vis[E[rt][i]]) continue;
    //     res += solve(E[rt][i]);
    // }
    return res;
}
int main(){
#ifdef DEBUG
    freopen("in", "r", stdin);
#endif
    int n;
    scanf("%d", &n);
    Prime::init(MAX << 1);
    for(int i = 1; i < n; i++){
        int u, v;
        scanf("%d%d", &u, &v);
        E[u].push_back(v);
        E[v].push_back(u);
    }
    LL up = solve(1);
    LL down = (LL)n * (n-1);
    printf("%.7lf\n", 1.0 *  up / down);
    return 0;
}
```