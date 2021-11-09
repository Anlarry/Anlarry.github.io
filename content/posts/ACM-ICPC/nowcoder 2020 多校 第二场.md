---
title: nowcoder 2020 多校 第二场
date: 2020-07-14T23:18:00+08:00
tags: ["搬家"]
categories: ACM/ICPC

---

# nowcoder 2020 多校 第二场

> https://ac.nowcoder.com/acm/contest/5667

## A All with Pairs

**题意** 

给$n$个串，定义$f(s,t)$为$s$前缀和$t$后缀最长的长度，求$\sum_i\sum_j f(s_i, s_j)^2$

**Solution**

先把每个串的后缀hash的值存下来，对每个串$s_i$,求$\sum_j f(s_i, s_j)^2$。对于$s_i$的每个前缀都可以查询hash求出
对应多少后缀，但是有可能一对$s_i, s_j$会有多个贡献，因此要用next数组去重

```cpp
const unsigned long long base = 131;
// vector<string> str;
string str[MAX];
unordered_map<unsigned long long, int> mp;
int nex[MAX];
int cnt[MAX];
void get_hash(const string &s){
    unsigned long long res = 0;
    unsigned long long p = 1;
    for(int i = s.size()-1; i >= 0; i--){
        // unsigned long long x = s[i] - 'a'+1;
        res += (s[i]-'a'+1) * p;
        p *= base;
        mp[res]++;
    }
}
void get_next(const string &t){
    nex[0] = -1;   
    int k = -1;
    for(int i = 1; i < t.size(); i++){
        while(k > -1 and t[k+1] != t[i])   
            k = nex[k];
        if(t[k+1] == t[i]) k++;
        nex[i] = k;
    }
}
int main(){
#ifdef DEBUG
    freopen("in", "r", stdin);
#endif
    int n;
    scanf("%d", &n);
    // cin >> n;
    for(int i = 0; i < n; i++){
        // string s;
        cin >> str[i];
        // str.push_back(s);
        get_hash(str[i]);
    }
    LL ans = 0;
    for(int i = 0; i < n; i++){
        unsigned long long cur_has = 0 ;
        for(int j = 0; j < str[i].size(); j++){
            cur_has = cur_has * base + (str[i][j] - 'a' + 1);
            // cnt[j] = mp[cur_has];
            auto it = mp.find(cur_has);
            if(it != mp.end()) cnt[j] = it->second;
            else cnt[j] = 0;
        }
        get_next(str[i]);
        for(int j = 1; j < str[i].size(); j++){
            if(nex[j] >= 0)
            cnt[nex[j]] -= cnt[j];
        }
        for(LL j = 0; j < str[i].size(); j++){
            ans = (ans + 1LL * cnt[j] * (j+1LL) % mod * (j+1LL) % mod) % mod;
        }
    }
    printf("%lld\n", ans);
    // cout << ans << '\n';
}
```

## B Boundary

**题意**

圆是过原点的，选一个能经过最多点的圆

```cpp
P pts[5000];
// map<tuple<LL, LL, LL, LL>, int> cnt;
vector<pair<double, double>> p;
void update(LL x1, LL y1, LL x2, LL y2){
    LL cir_x1 = (x1*x1 + y1*y1) * y2 - (x2*x2 + y2*y2) * y1;
    LL cir_x2 = 2 * (x1 * y2 - x2 * y1);
    LL cir_y1 = (x2*x2 + y2*y2) * x1 - (x1*x1 + y1*y1) * x2;
    LL cir_y2 = 2 * (x1 * y2 - x2 * y1);
 
    if(cir_x2 == 0) return ;
 
    p.push_back(make_pair(1.0 * cir_x1 / cir_x2, 1.0 * cir_y1 / cir_y2));
 
}
int main(){
#ifdef DEBUG
    freopen("in", "r", stdin);
#endif
    int n;
    scanf("%d", &n);
    for(int i = 1; i <= n; i++){
        scanf("%d%d", &pts[i].first, &pts[i].second);
    }
    if(n <= 2) {
        printf("%d\n", n);
        return 0;
    }
    for(int i = 1; i <= n; i++){
        for(int j = 1; j < i; j++){
            update(pts[i].first, pts[i].second, pts[j].first, pts[j].second);
        }
    }
    sort(p.begin(), p.end());
    if(p.size() == 0){
        printf("%d\n", 1);
        return 0;
    }
    pair<double, double> cur = p[0];
    int ans = 0, tmp = 0;
    for(int i = 0; i < p.size(); i++){
        if(p[i] == cur) {tmp++; ans = max(ans, tmp);}
        else {
            cur = p[i];
            tmp = 1;
            ans = max(ans, tmp);
        }
    }
    ans *= 2;
    int res ;
    for(int i = 1; i <= n; i++){
        int cur = i;
        if(cur * (cur-1) == ans) {
            res = cur;
            break;
        }
    }
    printf("%d\n", res);
}
```

## F Fake Maxpooling

**题意**

给一个矩阵，$A_{i,j}=lcm(i,j)$，在每个$k\times k$子矩阵中选出最大的元素求和

**Solution**

维护横竖的单调队列，或者暴力

```cpp
int k;
struct stk
{
    P stack[5050];
    int lenth;
    int start_idx;
    int front;
    stk() : lenth(0), start_idx(0), front(0) {}
    void push(int x, int idx){
        while(!empty() and top() < x) {
            pop();
        }
        stack[lenth++] = {x, idx};
        start_idx = idx;
    }
    int max(){
        while(!empty() and stack[front].second < start_idx - k + 1) 
            front ++; 
        return stack[front].first;
    }
    int top(){
        return stack[lenth-1].first;
    }
    void pop(){
        lenth--;
    }
    bool empty(){
        return lenth == front;
    }
    void clear(){
        lenth = 0;
        front = 0;
        start_idx = 0;
    }
};


inline int A(int x, int y){
    return x * y / __gcd(x, y);
}

stk col_stk[5050];
stk row_stk;
int main(){
#ifdef DEBUG
    freopen64("in", "r", stdin);
#endif
    int n, m;
    scanf("%d%d%d", &n, &m, &k);
    for(int i = 1; i <= m; i++){
        for(int j = 1; j < k; j++){
            col_stk[i].push(A(i, j), j);          
        }
    }
    LL ans = 0;
    for(int i = 1; i <= n-k+1; i++){
        for(int j = 1; j <= m; j++){
            col_stk[j].push(A(i+k-1,j), i+k-1);
        }
        for(int j = 1; j < k; j++){
            row_stk.push(col_stk[j].max(), j);
        }
        for(int j = k; j <= m; j++){
            row_stk.push(col_stk[j].max(), j);
            ans += (LL) row_stk.max();
        }
        row_stk.clear();
    }
    printf("%lld\n", ans);
    return 0;
}
```

## G Greater and Greater

**题意**

给一个长度为$n$的序列A, 和一个长度位$m$的序列B，求A有多少位置对应元素都大于B

**Solution**

考虑bitset，$f(i, j)$表示序列A位置$i$,序列B位置$j$，后面的元素A对应大于B，要求的就是$\sum f(i, 1)$

```
   A : 1  4  2  8  5  7
   B : 2  3  3
```

比如$f(3, 1)$可以通过$f(4, 2)$转移过来，$f(3, 2)$可以用$f(4, 3)$转移过来，$f(3, 3)$直接比较A[3],B[3]，可以发现这些转移都要比较A[3],B[i]

所以对每个A[i]求一个新的bitset\ S，A[i]大于B[j]第$j$位就为1

转移就是 $f_i=(f_{i+1}>>1 | I_m) \& S_i$


```c++
int a[MAX], b[MAX];
P A[MAX], B[MAX];
vector<bitset<N>> bits;
int idx[MAX];

int main(){
#ifdef DEBUG
    freopen64("in", "r", stdin);
#endif
    int n, m;
    scanf("%d%d", &n, &m);
    for(int i = 1; i <= n; i++) {
        scanf("%d", &a[i]);
        A[i] = make_pair(a[i], i);
    }
    for(int i = 1; i <= m; i++){
        scanf("%d", &b[i]);
        B[i] = make_pair(b[i], i);
    }
    sort(A+1, A+1+n);
    sort(B+1, B+1+m);
    int i = 1, j = 0;
    bitset<N> cur;
    bits.push_back(cur);
    while(i <= n){
        bool new_bit = 0;
        while(j < m && A[i].first >= B[j+1].first){
            new_bit = 1;
            cur[B[j+1].second] = 1;
            j++;
        }
        if(new_bit) {
            bits.push_back(cur);
        }
        idx[A[i].second] = bits.size()-1;
        i++;
    }
    bitset<N> f;
    int ans = 0;
    for(int i = n; i >= 1; i--){
        f >>= 1;
        f[m] = 1;
        f &= bits[idx[i]];
        ans += f[1];
    }
    printf("%d\n", ans);
}
```

## J Just Shuffle

**题意**


要求一个置换$P$，使得$\{1,2,\dots,n\}$在$P$置换$k$次后成为排列$A$

> k是素数

**Solution**

就是说$A=P^k$,已知$A,k$，求$P$。把$A$分解成$(cir_1)(cir_2)\dots$，对每个循环$cir$,我们知道$P^k(cir_i)=cir_{i+1}$。

我们想让$cir_i$置换$sk$次，让$sk\  mod\  len(cir)=1$，就可以求出$P(cir_i)$

```cpp
int a[MAX];
int ans[MAX];
bool vis[MAX];
void extend_Euclid(int a, int b, int &x, int &y)
{
    if(b == 0){
        x = 1;
        y = 0;
        return;
    }
    extend_Euclid(b, a % b, x, y);
    int tmp = x;
    x = y;
    y = tmp - (a / b) * y;
}
int main(){
#ifdef DEBUG
    freopen("in", "r",stdin);
#endif
    int n, k;
    scanf("%d%d", &n, &k);
    for(int i = 1; i <= n; i++){
        scanf("%d", &a[i]);
    }
    for(int i = 1; i <= n; i++){
        if(vis[i]) continue;
        vector<int> cir;
        int x = i;
        while(!vis[x]) {
            cir.push_back(x);
            vis[x] = 1;
            x = a[x];
        }
        int s = k % cir.size();
        int y = 1;
        int w, m;
        extend_Euclid(s, cir.size(), w, m);
        if(w < 0) {
            int ww = -w;
            int tmp = (ww + cir.size() -1) / cir.size();
            w += tmp * cir.size();
        }
        y = w;
        // while(s * y % cir.size() != 1){
        //     y ++;
        //     // printf("%d", s * y % cir.size());
        // }
        for(int j = 0; j < cir.size(); j++){
            ans[cir[j]] = cir[(j+y)%cir.size()];
        } 
    }
    for(int i = 1; i <= n; i++)
        printf("%d ", ans[i]);
    puts("");
    return 0;
}
```
