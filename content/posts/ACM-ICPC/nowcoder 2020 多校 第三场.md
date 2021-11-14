---
title: nowcoder 2020 多校 第三场 
date: 2020-08-11T16:08:00+08:00
tags: ["搬家"]
categories: ACM/ICPC

---

# nowcoder 2020 多校 第三场 

## E Two Matchings

**题意**

可以简化题意为，每个点有个权重$w$，两个点$i,j$相连的代价$abs(w_i-w_j)$,找两个没有重叠的匹配使得代价最小

**Solution**

其实就是在将数字用长度为偶数的环连起来，求最小代价。进一步可以发现，环用长度为4或6，长度更长的环可以被分解达到更小的代价

```cpp
LL n;
LL a[MAX];
LL dp[MAX][2];
LL four(const vector<LL> &a){
    return 2LL * (a[3]-a[0]);
}
 
LL six(const vector<LL> &a){
    return 2LL * (a[5]-a[0]);
}
int main(){
#ifdef DEBUG
    freopen("in", "r", stdin);
#endif
    int T;
    scanf("%d", &T);
    while (T--)
    {
        cin >> n;
        for(int i = 0; i < n; i++)
            cin >> a[i];
        sort(a, a+n);
        for(LL i = 0; i <= n; i++ )
            dp[i][0] = dp[i][1] = __64inf;
        dp[3][0] = 2LL * (a[3]-a[0]);
        dp[5][0] = 2LL * (a[5]-a[0]);
        dp[7][0] = 2LL * (a[7]-a[4] + a[3] - a[0]);
        for(LL i = 9; i < n; i+=2){
            dp[i][0] = min(dp[i-4][0], dp[i-4][1]) + four(vector<LL>({a[i-3], a[i-2], a[i-1], a[i]}));
            dp[i][1] = min(dp[i-6][0], dp[i-6][1]) +
                six(vector<LL>({a[i-5], a[i-4], a[i-3], a[i-2], a[i-1], a[i]}));
        }
        printf("%d\n", min(dp[n-1][0], dp[n-1][1]));
    }
     
}
```

##  F   Fraction Construction Problem

**题意**

问有没有满足$\displaystyle\frac{c}{d}-\frac{e}{f}=\frac{a}{b}$的$c,d,e,f$

**Solution**

感觉是分类讨论，然后构造解

```cpp
void ex_gcd(LL a, LL b, LL &x, LL &y){
    if(b == 0){
        x = 1;
        y = 0;
        return;
    }
    ex_gcd(b, a % b, x, y);
    LL tmp = x;
    x = y;
    y = tmp - (a / b) * y;
}
 
LL p[MAX];
LL cnt[MAX];
vector<LL> prime;
bool isprime[MAX];
void init(){
    mset(isprime, 1);
    isprime[0] = isprime[1] = 0;
    for(int i = 2; i < MAX; i++){
        if(isprime[i]) {
            prime.push_back(i);
            cnt[i] = 1;
            p[i] = i;
        }
        for(int j = 0; j < prime.size() and i * prime[j] < MAX; j++){
            isprime[i * prime[j]] = 0;
            cnt[i * prime[j]] = i % prime[j] == 0 ? cnt[i] : cnt[i] + 1;
            p[i * prime[j]] = prime[j];
        }
    }
}
 
int main(){
#ifdef DEBUG
#else
    ios::sync_with_stdio(0);
    cin.tie(0);
#endif
    init();
    int T;
    cin >> T;
    while (T--)
    {
        LL a, b;
        cin >> a >> b;
        LL g = __gcd(a, b);
        if(g > 1){
            LL bb = b / g, aa = a / g;
            cout << aa + 1 << " " << bb << ' ' << 1 << ' ' << bb << '\n';
            continue;
        }
        LL tmp = b;
        if(cnt[b] <= 1) {
            cout << "-1 -1 -1 -1\n";
            continue;
        }
        LL d = 1;
         tmp = b;
        while(tmp % p[b] == 0) {
            tmp /= p[b];
            d *= p[b];
        }
        LL f = b / d;
        LL c, e;
        ex_gcd(f, d, c, e);
        if(c < 0) {
            LL t = - (c -d + 1) / d;
            c += t * d;
            e -= t * f;
        }
        e = -e;
        e *= a;
        c *= a;
        cout << c << ' ' << d << ' ' << e << ' ' << f << '\n';
    }
}
```

## G   Operating on a Graph

**题意**

给一个图，一开始每个点$i$,属于$i$类，每次操作会吧某个类$x$的相邻的类归为$x$,求最后每个点属于那个类

**Solution**

并查集模拟

```cpp
list<int> lk[MAX];
vector<int> G[MAX];
int fa[MAX];
int Find(int x) {
    return fa[x] == x ? x : fa[x] = Find(fa[x]);
}
int main(){
#ifdef DEBUG
    freopen64("in", "r", stdin);
#endif
    int T;
    scanf("%d", &T);
    while (T--)
    {
        int n, m;
        scanf("%d%d",&n, &m);
        for(int i = 1; i <= n; i++){
            G[i].clear();
            fa[i] = i;
            lk[i].clear();
            lk[i].push_back(i);
        }
        for(int i = 0; i < m; i++) {
            int u, v;
            scanf("%d%d", &u, &v);
            u++, v++;
            G[u].push_back(v);
            G[v].push_back(u);
        }
        int q;
        scanf("%d",&q);
        while (q--)
        {
            int o;
            scanf("%d", &o);
            o++;
            int siz = lk[o].size();
            if(Find(o) != o) continue;
            for(int i = 0; i < siz; i++) {
                int cur = lk[o].front();
                lk[o].pop_front();
                for(int j = 0; j < G[cur].size(); j++) {
                    int v = G[cur][j];
                    int pv = Find(v);
                    if(pv != o) {
                        fa[pv] = o;
                        lk[o].splice(lk[o].end(), lk[pv]);
                    }
                }
            }
        }
        for(int i = 1; i <= n; i++)
            printf("%d ", Find(i)-1);
        puts("");
    }
    return 0;
}
```