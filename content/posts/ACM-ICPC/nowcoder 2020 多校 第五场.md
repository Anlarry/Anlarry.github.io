---
title: nowcoder 2020 多校 第五场
date: 2020-08-07T16:34:00+08:00
tags: ["搬家"]
categories: ACM/ICPC

---

# nowcoder 2020 多校 第五场

> https://ac.nowcoder.com/acm/contest/5670

## B Graph

> tire树，最小生成树

**题意**：

给一个带有边权的树，可以删除或添加边，但要保证：
- 图联通
- 环上边权异或为0

**Solution**

不管怎么操作，两点路径上边权的异或值是固定的，于是问题就转化成一个最小生成树问题。每次选出不联通的点集$S_1$, $S_2$,
将他们联通的代价是 

$$
    \min\limits_{u\in S_1 v\in S_2} \{\mathord{dis}(u,v)\}
$$

可以通过tire树实现这个过程，就是tire树合并子节点，复杂度为$O(n\log n)$

```cpp
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
#include <bitset>
#include <unordered_map>
#include <assert.h>
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
const int MAX = 1e3 + 50;
#else
const int MAX = 3e6 + 50;
#endif
const int mod = 10007;

LL pow2[100];
vector<P> G[MAX];
int dis[MAX];
struct Tire{
    int nex[MAX][2];
    int siz[MAX];
    int tot ;
    LL ans;
    void init(){
        ans = 0;
        siz[0] = 0;
        mset(nex[0], 0);
        tot = 1;
    }
    void add(int x, int d) {
        int rt = 0;
        for(int i = d; i >= 0; i--){
            int e = x & pow2[i] ? 1 : 0;
            if(!nex[rt][e]) {
                mset(nex[tot], 0);
                siz[tot] = 0;
                nex[rt][e] = tot ++;
            }
            rt = nex[rt][e];
            siz[rt] ++;
        }
    }
    LL query(int val, int u, int d) {
        int rt = u;
        LL res = val;
        for(int i = d; i >= 0; i--){
            if(res & pow2[i]) {
                if(nex[rt][1]) {
                    res ^= pow2[i];
                    rt = nex[rt][1];
                }
                else rt = nex[rt][0];
            }
            else {
                if(nex[rt][0]) {
                    rt = nex[rt][0];
                }
                else {
                    res ^= pow2[i];
                    rt = nex[rt][1];
                }
            }
        }
        return res;
    }
    LL merge(int l, int r, int val, int d, int row_d){
        if(!nex[l][0] and !nex[l][1]) {
            return query(val, r, row_d);
        }
        LL res = inf;
        if(nex[l][0]) res = min(res, merge(nex[l][0], r, val, d-1, row_d));
        if(nex[l][1]) res = min(res, merge(nex[l][1], r, val^pow2[d], d-1, row_d));
        return res;
    }
    void dfs(int u, int val, int d) {
        if(nex[u][0]) dfs(nex[u][0], val, d-1);
        if(nex[u][1]) dfs(nex[u][1], val ^ pow2[d], d-1);
        if(!nex[u][0] or !nex[u][1]) 
            return;
        int l = nex[u][0], r = nex[u][1];
        if(siz[l] > siz[r]) swap(l, r);
        ans += 1LL * merge(l, r, 0, d-1, d-1) + pow2[d];

    }
}tire;
void dfs(int u, int p, int d){
    dis[u] = d;
    for(int i = 0; i < G[u].size(); i++){
        int v = G[u][i].first;
        if(v == p) continue;
        dfs(v, u, d^G[u][i].second);
    }
}
int main(){
#ifdef DEBUG
    freopen64("in", "r", stdin);
#endif
    pow2[0] = 1;
    for(int i = 1; i < 32; i++) pow2[i] = pow2[i-1] << 1;
    int n;
    scanf("%d", &n);
    for(int i = 1; i < n; i++) {
        int u, v, c;
        scanf("%d%d%d", &u, &v, &c);
        u++, v++;
        G[u].emplace_back(v, c);
        G[v].emplace_back(u, c);
    }
    dfs(1, -1, 0);
    tire.init();
    for(int i = 1; i <= n; i++) 
        tire.add(dis[i], 29);
    tire.dfs(0, 0, 29);  
    printf("%lld\n", tire.ans);
    return 0;
}
```
## D Drop Voicing

> 最长上升子序列

**题意**

给一个序列$p_1\dots p_n$，可以进行两种操作，进行连续的第一个操作耗费一个代价

- 把$p_{n-1}$放到最前面
- $p_1$放到最后面

求把序列变成升序的最小代价

**Solution**

结合两种操作其实就是把一个元素$p_i$移到一个位置，需要一个代价。于是变成求LIS

```cpp
int n;
int a[MAX];
int b[MAX];
int solve(){
    mset(b, 0);
    int ans = 0;
    for(int i = 1; i <= n; i++) {
        int idx = lower_bound(b, b+ans, a[i]) - b;
        b[idx] = a[i];
        if(idx == ans) ans ++;
    }
    // cout << n - ans << "\n";
    return ans;
}
int main(){
#ifdef DEBUG
    freopen64("in", "r", stdin);
#else 
    ios::sync_with_stdio(0);
    cin.tie(0);
#endif
    cin >> n;
    for(int i = 1; i <= n; i++) cin >> a[i];
    int ans = 0;
    for(int i = 0; i < n; i++) {
        rotate(a+1, a+2, a+n+1);
        ans = max(ans, solve());
    }
    cout << n - ans << "\n";
    return 0;
}
```

## E Bogo Sort

**题意**

给一个置换，求有多少种排列可以通过这个置换变成升序

```python
from math import gcd

def lcm(a:int, b:int) ->int:
    return a * b // gcd(a, b)

n = int(input())
a = [0] + list(map(int, input().split()))
vis = [False for i in range(len(a))]

ans = 1
for i in range(1, n+1):
    x = a[i]
    if vis[x] : continue
    cur = 0
    while not vis[x] :
        vis[x] = True
        cur += 1
        x = a[x]
    ans = lcm(ans, cur)
print(ans)

```