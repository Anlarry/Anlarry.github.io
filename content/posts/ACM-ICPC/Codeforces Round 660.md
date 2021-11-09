---
title: "Codeforces Round #660"
date: 2020-08-12T01:01:00+08:00
tags: ["搬家"]
categories: ACM/ICPC
---

# Codeforces Round #660

> https://codeforces.com/contest/1388

## E

**题意**

平面上有一些线段，将他们投影到$x$轴上，使得彼此不想交(但是可以挨着)，设最左边的点和最右边的点的横坐标分别是$x_l, x_r$，
求$min\{x_r-x_l\}$

**Solution**

首先对于两个线段$s_1, s_2$, 并且$s_1.y < s_2.y$, 交叉连接他们的左右端点，得到一个向量集合$bound$，对投影向量限制限制。
因此先两两枚举，得到投影向量的可行的取值。

而且最优的投影向量，是集合$bound$中的某一项

因此，对剩下的可行的投影向量, 假设当前枚举的方向是$k_i$，计算

$$
    max\{x_j+y_jKi\}-min\{x_j+y_jk_i\}
$$

计算这个式子可以用[Convex hull trick](http://wcipeg.com/wiki/Convex_hull_trick), 然后不断更新`ans`

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
#include <list>
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
const int MAX = 1e6 + 50;
#endif
const int mod = 998244353;
void file_read(){
#ifdef DEBUG
    freopen64("in", "r", stdin);
#endif
}

struct Seg {
    double xl, xr, y;
    Seg(double xl, double xr, double y) : xl(xl), xr(xr), y(y) {}
};
vector<Seg> seg;
vector<pair<double, double>> bd;
void bound() {
    for(int i = 0; i < seg.size(); i++) {
        for(int j = i+1; j < seg.size(); j++) {
            if(seg[i].y == seg[j].y) continue;
            auto s1 = seg[i];
            auto s2 = seg[j];
            if(s1.y > s2.y) swap(s1, s2);
            double beta1 = (s1.xr - s2.xl) / (s2.y - s1.y);
            double beta2 = (s1.xl - s2.xr) / (s2.y - s1.y);
            bd.emplace_back(beta2, beta1);
        }
    }
}
void filter(vector<double> &theta) {
    double cur = -__64inf;
    for(auto x : bd) {
        if(x.first >= cur) {
            theta.push_back(x.first);
            if(cur > -__64inf) theta.push_back(cur);
        }
        cur = max(cur, x.second);
    }
    theta.push_back(cur);
}
double cross(pair<double, double> A, pair<double, double> B) {
    return (A.first - B.first) / (B.second - A.second);
}
template<typename Cmp_1, typename Cmp_2>
struct Convex {
    int top;
    vector<pair<double, double>> pts;
    vector<double> X;
    pair<double, double> stk[3000];
    Cmp_1 cmp_1;
    Cmp_2 cmp_2;
    Convex(vector<Seg> &seg) : cmp_1(Cmp_1()), cmp_2(Cmp_2()) {
        for(auto s : seg) {
            pts.emplace_back(s.xl, s.y);
            pts.emplace_back(s.xr, s.y);
        }
        sort(pts.begin(), pts.end(), [this](const pair<double, double> &A, const pair<double, double> &B){
            if(abs(A.second -  B.second) < 1e-8 )  return this->cmp_1(A.first, B.first);//return A.first < B.first;
            // return A.second > B.second;
            return this->cmp_2(A.second, B.second);
        });
        int i = 0;
        top = 0;
        stk[top++] = pts[0];
        while(i < pts.size() and abs(pts[i].second-pts[0].second) < 1e-8) i++;
        if(i == pts.size()) {
            X.push_back(-__64inf);
            return ;
        }
        stk[top++] = pts[i++];
        while(i < pts.size()) {
            int j = i;
            while(j < pts.size() and abs(pts[j].second-pts[i].second) < 1e-8) j++;
            if(j == pts.size()) break;
            auto cur = pts[j];
            while(top >= 2) {
                double cross_x = cross(stk[top-1], stk[top-2]);
                double cross_cur = cross(cur, stk[top-2]);
                if(cross_cur < cross_x) top--;
                else break;
            }
            stk[top++] = cur;
            i = j+1;
        }
        X.push_back(-__64inf);
        for(int i = 1; i < top; i++) {
            X.push_back(cross(stk[i-1], stk[i]));
        }
    }
    double query(double a) {
        int idx = lower_bound(X.begin(), X.end(), a)- X.begin();
        idx--;
        return stk[idx].first + stk[idx].second * a;
    }    
};
struct Less {
    bool operator () (double A, double B)  {
        return A < B;
    }
};
struct Great {
    bool operator () (double A, double B) {
        return A > B;
    }
};

int main(){
#ifdef DEBUG
    freopen64("in", "r", stdin);
#endif
    int n;
    scanf("%d", &n);
    set<double> Y;
    for(int i = 0; i < n; i++) {
        double xl, xr, y;
        scanf("%lf%lf%lf", &xl, &xr, &y) ;
        seg.emplace_back(xl, xr, y);
        Y.insert(y);
    }
    if(Y.size() == 1) {
        double Min = __64inf, Max = -__64inf;
        for(auto s : seg) {
            Min = min(s.xl, Min);
            Max = max(Max, s.xr);
        }
        printf("%.10f\n", Max-Min);
        return 0;
    }
    bound();
    vector<double> theta;
    sort(bd.begin(), bd.end());
    filter(theta);
    #ifdef DEBUG
        for(auto t : theta)  printf("%.2f ", t);
        puts("");
    #endif
    double ans = __64inf;
    Convex<Less, Great> left(seg);
    Convex<Great, Less> right(seg);
    for(auto x : theta) {
        double l = left.query(x);
        double r = right.query(x);
        ans = min(ans, r-l);
    }
    printf("%.10f\n", ans);
}
```

## A B C D 

> [A](https://codeforces.com/contest/1388/submission/88644307)
> [B](https://codeforces.com/contest/1388/submission/88645786/)
> [C](https://codeforces.com/contest/1388/submission/88678461/)
> [D](https://codeforces.com/contest/1388/submission/88747196/)