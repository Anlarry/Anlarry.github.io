---
title: "Gym 102460L Largest Quadrilateral "
date: 2020-08-27T00:01:00+08:00
tags: ["搬家", "计算几何"]
categories: ACM/ICPC
---

# Gym 102460L Largest Quadrilateral 

> [Largest Quadrilateral](https://codeforces.com/gym/102460/) 

## 题意

给$n$个点从中选出四个点，使得面积最大

## Solution

- 首先，肯定是求凸包，要求的点一定在凸包上。
- 不难联想到凸包对每个边求最大三角形面积的问题，也就是旋转卡壳。
- 可以将问题转化为，对凸包的每一个对角线$A_iA_j$，求最大面积的两个三角形，$\triangle{A_iA_jP}$, $\triangle{A_iA_jQ}$, 然后就可以枚举对角线，旋转卡壳算最大面积

> **细节**
> - 输出的格式
> - 凸包上应该留下共线的点

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
const int MAX = 1e6  + 50;
#endif
const int mod = 1e9+9;
void file_read(){
#ifdef DEBUG
    freopen("in", "r", stdin);
    // freopen("out", "w", stdout);
#endif
}

template<typename type>
struct Vec
{
    type x, y;
    Vec() {}
    Vec(type x, type y) : x(x), y(y) {}
    friend istream & operator >> (istream &in, Vec &A) {
        in >> A.x >> A.y;
        return in;
    }    
    friend Vec operator - (const Vec &A, const Vec &B) {
        return Vec(A.x-B.x, A.y-B.y);
    }
    friend Vec operator + (const Vec &A, const Vec &B) {
        return Vec(A.x + B.x, A.y + B.y);
    }
    friend type det(const Vec &A, const Vec &B) {
        return A.x * B.y - A.y * B.x;
    }
    friend type dot(const Vec &A, const Vec &B) {
        return A.x * B.x + A.y * B.y;
    }
    friend bool operator < (const Vec &A, const Vec &B) {
        if(A.x != B.x) return A.x < B.x;
        return A.y < B.y;
    }  
    friend type area(const Vec &A, const Vec &B) {
        return abs(det(A, B));
    }
    friend type operator == (const Vec &A, const Vec &B) {
        return A.x == B.x and A.y == B.y;
    }
};

template<typename type>
vector<Vec<type>> convex_hull(vector<Vec<type>> &pt) {
    sort(pt.begin(), pt.end());
    int n = pt.size();
    vector<Vec<type>> res(2*n);
    int k = 0 ;
    for(int i = 0; i < n; i++) {
        while(k > 1 and det(res[k-1]-res[k-2], pt[i]-res[k-1]) < 0) // <=会wa
            k--;
        res[k++] = pt[i];
    }
    for(int i = n-2, t = k; i >= 0; i--) {
        while(k > t and det(res[k-1]-res[k-2], pt[i]-res[k-1]) < 0) // <=会wa
            k--;
        res[k++] = pt[i];
    }
    res.resize(k-1);
    return res;
}

struct ModI
{
    int i, n;
    ModI(int n ) : i(0), n(n) { assert(n > 0) ;} 
    ModI(int i, int n) : i(i%n), n(n) { assert(n > 0); }
    ModI operator ++ ( int ) {
        ModI row = ModI(i, n);
        i = (i + 1) % n;
        return row;
    }
    ModI operator + (int x) {
        ModI res = ModI(i, n);
        res.i = (res.i + x) % n;
        return res;
    }
    int operator = (int x) {
        return i = x;
    }
    bool operator < (int x) const {
        return i < x;
    }
    bool operator == (const ModI &other) const {
        return i  == other.i;
    }
    operator int () {
        return i;
    }
};

template<typename type>
type area(const Vec<type> &A, const Vec<type> &B, const Vec<type> &C) {
    return area(A-B, A-C);
}

template<typename type>
type rotateCalipers(vector<Vec<type>> pt) {
    int n = pt.size();
    type res = 0;
    for(int i = 0; i < pt.size(); i++) {
        ModI p1 = ModI(i+1, n);
        ModI p2 = ModI(i+3, n);
        for(ModI j = ModI(i+2, n); j+1 != i; j++) {
            while(p1+1 != j and area(pt[p1], pt[i], pt[j]) < area(pt[p1+1], pt[i], pt[j])) 
                p1 ++;
            if(j == p2) p2++;
            while(p2+1 != i and area(pt[p2], pt[i], pt[j]) < area(pt[p2+1], pt[i], pt[j]))
                p2 ++;
            auto cur = area(pt[p1], pt[i], pt[j]) + area(pt[p2], pt[i], pt[j]);
            res = max(res, cur);
        }
    }
    return res;
}

void out(LL ans) {
    if(ans & 1) {
        printf("%lld.5\n", ans >> 1);
    }
    else {
        printf("%lld\n", ans >> 1);
    }
}

int main() {
    file_read();
    int T;
    scanf("%d", &T);
    while (T--)
    {
        int n;
        scanf("%d", &n);
        vector<Vec<LL>> pt(n);
        for(int i = 0; i < n; i++) cin >> pt[i];
        auto ch = convex_hull(pt);
        if(ch.size() < 3) {
            printf("0\n");
            continue;
        }
        if(ch.size() == 3) {
            LL ans = 0;
            LL A = area(ch[0], ch[1], ch[2]);
            for(auto p : pt) {
                if(p == ch[0] or p == ch[1] or p == ch[2]) continue;
                auto a = area(p, ch[1], ch[2]);
                a = min(a, area(p, ch[0], ch[2]));
                a = min(a, area(p, ch[0], ch[1]));
                ans = max(ans, A-a);
            }
            out(ans);
            continue;
        }
        LL res = rotateCalipers(ch);
        out(res);
    }
    return 0;
}
```
