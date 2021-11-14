---
title: "Codeforces Round #663"
date: 2020-08-17T22:42:00+08:00
tags: ["搬家"]
categories: ACM/ICPC
---

# Codeforces Round #663

> http://codeforces.com/contest/1391

## C 

**题意**

对于一个排列$\{p_1, p_2, \dots, p_n\}$，对于每个数字$p_i$，向前找第一个大于$p_i$的$p_j$，$i$和$j$连一条边，向后同理。求多少种排列生成的图是有环的

**Solution**

- 对于$p_i$，如果存在$p_j>p_i(j<i)$, $p_k>p_i(k>i)$，那么就是存在环的
- 那么对于$\forall i$都不成立时，就没有环
- 此时就是序列的特点就是,数字$n$左边和右边向两边递减
- 因此排列数就是$n!-2^{n-1}$

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
#include <cstdlib>
#include <queue>
#include <iomanip>
#include <cassert>
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
const LL inf = 0x3f3f3f3f3f3f3f3f;
#ifdef DEBUG
const int MAX = 2e3 + 50;
#else
const int MAX = 2e6  + 50;
#endif
const int mod = 1e9+7;
void file_read(){
#ifdef DEBUG
    freopen("in", "r", stdin);
#endif
}
int main(){
    file_read();
    LL n;
    scanf("%lld\n", &n);
    LL fact = 1;
    for(LL i = 1; i <= n; i++) {
        fact = fact * i % mod;
    }
    LL pow2 = 1;
    for(int i = 1; i < n; i++) {
        pow2 = pow2 * 2 % mod;
    }
    LL res = (fact - pow2) % mod;
    if(res < 0) res += mod;
    printf("%lld\n", res);
}
```

## D

**题意**

给一个01矩阵，对于每个变成位偶数的正方形，1的个数必须时奇数个，求最小改变01的次数

**Solution**

- 首先，对于4*4的矩阵是无解的，因此，$n,m$必有一个小于4，不妨假设$n<4$, 且$n<m$
- 然后问题就可以dp，枚举状态转移
$$
    dp[i][mask_y] = min(dp[i-1][mask_x]+cost(i,mask_y), dp[i][mask_y])
$$

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
//#include <memory.h>
#include <cstdlib>
#include <queue>
#include <iomanip>
#include <cassert>
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
const int MAX = 2e6  + 50;
#endif
const int mod = 1e9+7;
void file_read(){
#ifdef DEBUG
    freopen("in", "r", stdin);
#endif
}

char s[10][MAX];
int dp[MAX][10];
int n, m;
int cost(int col, int mask){
    int res = 0;
    for(int i = 1; i <= n; i++) {
        int x = s[i][col] - '0';
        int y = mask & 1;
        res += x != y;
        mask >>= 1;
    }
    return res;
}
bool check(int x, int y) {
    int A[2][10];
    for(int i = 1; i <= n; i++) {
        int a = x & 1;
        int b = y & 1;
        A[0][i] = a;
        A[1][i] = b;
        x >>= 1;
        y >>= 1;
    }
    int res = A[0][1] ^ A[0][2] ^ A[1][1] ^ A[1][2];
    if(!res) return false;
    if(n == 2) return true;
    res = A[0][2] ^ A[0][3] ^ A[1][2] ^ A[1][3];
    return res;
}
int main(){
    file_read();
    scanf("%d%d", &n, &m);
    if(n == 1 || m == 1) {
        puts("0");
        return 0;
    }
    if(n >= 4 and m >= 4) {
        puts("-1");
        return 0;
    }
    if(n <= m) {
        for(int i = 1; i <= n; i++) scanf("%s", s[i]+1);
    }
    else {
        for(int i = 1; i <= n; i++) {
            char t[10];
            scanf("%s", t+1);
            for(int j = 1; j <= m; j++) {
                s[j][i] = t[j];
            }
        }
        swap(n, m);
    }
    // n <= m;
    mset(dp, 0x3f);
    int up = n == 2 ? 4 : 8;
    for(int i = 0; i < up; i++) {
        for(int j = 0; j < up; j++) {
            if(check(i, j))
                dp[2][j] = min(dp[2][j], cost(1, i)+cost(2, j));
        }
    }
    for(int i = 3; i <= m; i++) {
        for(int x = 0; x < up; x++) {
            for(int y = 0; y < up; y++) {
                if(check(x, y))
                    dp[i][y] = min(dp[i][y], dp[i-1][x]+cost(i, y));
            }
        }
    }
    int res = inf;
    for(int i = 0; i < up; i++ ) {
        res = min(res, dp[m][i]);
    }
    printf("%d\n", res);
}
```

## E

**题意**

给一个无向图连通图，有$n$个点，求下面任意一个，
- 长度不小于$\lceil n \rceil$
- 一个不小于$\lceil n \rceil$的点集，两两组成pair，pair之间的生成图边数不大于2

**Solution**

- 这是个构造题
- 考虑在图上跑dfs后得到的树
- 对于一个节点，除了树边，不会存在向旁边连的边，只有连向祖先的边
- 如果存在一个深度不小于$\lceil n \rceil$，答案就存在了
- 否则，对于每个深度两两pair，对于任意的pair的生成图都是满足要求的。对于每一层，最多只剩一个，而且最多有$\lfloor n\rfloor$,因此点集的大小不小于$n-\lfloor n \rfloor$

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
//#include <memory.h>
#include <cstdlib>
#include <queue>
#include <iomanip>
#include <cassert>
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
const int MAX = 2e6  + 50;
#endif
const int mod = 1e9+7;
void file_read(){
#ifdef DEBUG
    freopen("in", "r", stdin);
#endif
}

vector<int> G[MAX];
bool vis[MAX];
int dep[MAX];
vector<int> dep_set[MAX];
int p[MAX];
int pr[MAX];
void dfs(int u, int parent, int d) {
    dep[u] = d;
    vis[u] = 1;
    p[u] = parent;
    for(auto v : G[u]) {
        if(vis[v]) continue;
        dfs(v, u, d+1);
    }
}
int main(){
    file_read();
    int T;
    scanf("%d", &T);
    while (T--)
    {
        int n, m;
        scanf("%d%d", &n, &m);
        memset(vis, 0, sizeof(bool) * (n +50));
        memset(dep, 0, sizeof(int) * (n+50));
        memset(p, 0, sizeof(int) * (n+50));
        for(int i = 1; i <= n; i++) G[i].clear();
        for(int i = 1; i <= n; i++) dep_set[i].clear();
        for(int i = 1; i <= m; i++) {
            int u, v;
            scanf("%d%d", &u, &v);
            G[u].push_back(v);
            G[v].push_back(u);
        }
        dfs(1, -1, 1);
        int Max = -1, id = -1;
        for(int i = 1; i <= n; i++) {
            if(dep[i] > Max)  {
                Max = dep[i];
                id = i;
            }
        }
        if(Max * 2 >= n) {
            puts("PATH");
            vector<int> ans;
            int u = id;
            while(u != -1) {
                ans.push_back(u);
                u = p[u];
            }
            printf("%d\n", ans.size());
            for(auto x: ans) printf("%d ", x);
            puts("");
            continue;
        }
        for(int i = 1; i <= n; i++) {
            dep_set[dep[i]].push_back(i);
        }
        puts("PAIRING");
        vector<P> ans;
        for(int k = 1; k <= Max; k++) {
            for(int i = 0; i+1 < dep_set[k].size(); i+=2) {
                ans.emplace_back(dep_set[k][i], dep_set[k][i+1]);
            }
        }
        printf("%d\n", ans.size());
        for(auto p : ans ) printf("%d %d\n", p.first, p.second);
    }
    return  0;
}
```

## A B

> [A](https://codeforces.com/contest/1391/submission/90216735/)
> [B](https://codeforces.com/contest/1391/submission/90217550/)