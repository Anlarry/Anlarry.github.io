---
title: "Codeforces Round #664"
date: 2020-09-01T19:40:00
tags: ["搬家"]
categories: ACM/ICPC
---

# Codeforces Round #664

> https://codeforces.com/contest/1395

## A

**题意**

给四种颜色的球，可以把一个红色一个蓝色一个绿色染成白色，问能不能变成回文串

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
#include <unordered_map>
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
const int MAX = 2e6 + 500;
#endif
const int mod = 1e9 + 7;
void file_read() {
#ifdef DEBUG
	freopen("in", "r", stdin);
	// freopen("out", "w", stdout);
#endif
}

int main() {
	file_read();
	int T;
	LL a, b, c, d;
	scanf("%d", &T);
	while (T--)
	{
		cin >> a >> b >> c >> d;
		LL tot = a + b + c + d;
		if(tot & 1) {
			int odd = (a & 1) + (b & 1) + (c & 1) + (d & 1);
			if(odd == 1) {
				puts("Yes");
				continue;
			}
			if(a > 0 and b > 0 and c > 0) {
				a--, b--, c--, d+=3;
				odd = (a & 1) + (b & 1) + (c & 1) + (d & 1);
				if(odd == 1) {
					puts("Yes");
					continue;
				}
			}
			puts("No");
			continue;
		}	
		else {
			int odd = (a & 1) + (b & 1) + (c & 1) + (d & 1);
			if(odd == 0 ) {
				puts("Yes");
				continue;
			}
			if(a > 0 and b > 0 and c > 0 and d > 0) {
				a--, b--, c--, d += 3;
				odd = (a & 1) + (b & 1) + (c & 1) + (d & 1);
				if(odd == 0) {
					puts("Yes");
					continue;
				}
			}			
			puts("No");
		}
	}
	return 0;
}
```

## B

**题意**

在棋盘上走，类似于车，求遍历所有格子一次的方案

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
#include <unordered_map>
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
const int MAX = 2e6 + 500;
#endif
const int mod = 1e9 + 7;
void file_read() {
#ifdef DEBUG
	freopen("in", "r", stdin);
	// freopen("out", "w", stdout);
#endif
}

void print(int x, int y) {
	printf("%d %d\n", x, y);
}

bool vis[105][105];
int main() {
	file_read();
	int n, m, x, y;
	int X, Y;
	cin >> n >> m >> x >> y;
	X = x, Y = y;
	print(x, y--);
	while(y >= 1) {
		print(x, y--);
	}
	y = Y+1;
	while(y <= m) {
		print(x, y++);
	}
	y = m;
	for(int i = x-1; i >= 1; i--) {
		if(y == m){
			for(; y >= 1; y--)
				print(i, y);
			y = 1;
		}
		else if(y == 1) {
			for(; y <= m; y++)
				print(i, y);
			y = m;
		}
	}
	for(int i = x+1; i <= n; i++) {
		if(y == m){
			for(; y >= 1; y--)
				print(i, y);
			y = 1;
		}
		else if(y == 1) {
			for(; y <= m; y++)
				print(i, y);
			y = m;
		}
	}
	return 0;
}
```

## C 

**题意**

给两个序列$a$,$b$, 对每个$a_i$可以选择一个$b_j$,$c_i=a_i \& b_j$,求最小的$c_1|c_2|\dots|c_n$

**Solution**

从高位到低位，使得每一位尽可能为0，维护对于每个$a_i$,可以用的$b_j$

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
#include <unordered_map>
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
const int MAX = 2e6 + 500;
#endif
const int mod = 1e9 + 7;
void file_read() {
#ifdef DEBUG
	freopen("in", "r", stdin);
	// freopen("out", "w", stdout);
#endif
}

int n, m;
LL a[MAX], b[MAX];
bool ok[205][205];
bool check(LL x) {
	bool res = true;
	for(int i = 1; i <= n; i++) {
		bool cur = false;
		for(int j = 1; j <= m; j++) {
			if(!ok[i][j]) continue;
			if((a[i] & b[j] & x) == 0) {
				cur = true;
				break;
			} 
		}
		res &= cur;
	}
	if(res) {
		for(int i = 1; i <= n; i++) {
			for(int j = 1; j <= m; j++) {
				if((a[i] & b[j] & x) != 0) {
					ok[i][j] = false;
				}
			}
		}
	}
	return res;
}
int main() {
	file_read();
	scanf("%d%d", &n, &m);
	for(int i = 1; i <= n; i++) cin >> a[i];
	for(int i = 1; i <= m; i++) cin >> b[i];
	LL ans = 0;
	mset(ok, 1);
	for(LL i = 32; i >= 0; i--) {
		if(!check(1LL<<i)) {
			ans |= (1LL<<i);
		}
	}
	cout << ans << "\n";
	return 0;
}
```

## D

**题意**

给以序列$a$,可以选$n$次数字，如果数字大于$m$,那么后$d$次就不能选，求选出的数字的最大值

**Solution**

枚举选的数字小于等于$m$的个数，可以选择超过$m$的数字就是固定的，选择最大的

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
#include <unordered_map>
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
const int MAX = 2e6 + 500;
#endif
const int mod = 1e9 + 7;
void file_read() {
#ifdef DEBUG
	freopen("in", "r", stdin);
	// freopen("out", "w", stdout);
#endif
}

LL a[MAX];
LL A[MAX], B[MAX];
LL sa[MAX], sb[MAX];
int main() {
	file_read();
	LL n, d, m;
	cin >> n >> d >> m;
	for(int i = 1; i <= n; i++) cin >> a[i];
	sort(a+1, a+1+n, [](LL x, LL y) {
		return x > y;
	});
	int tot1 = 0, tot2 = 0;
	for(int i = 1; i <= n; i++) {
		if(a[i] <= m) A[++tot1] = a[i];
		else B[++tot2] = a[i];
	}
	if(tot1 == 0) {
		LL nn = (n+d) / (d + 1);
		LL ans = 0;
		for(int i = 1; i <= nn; i++) {
			ans += B[i];
		}
		cout << ans << "\n";
		return 0;
	}
	if(tot2 == 0) {
		LL ans = 0;
		for(int i = 1; i <= n; i++) {
			ans += A[i];
		}
		cout << ans << "\n";
		return 0;
	}
	for(int i = 1; i <= tot1; i++) sa[i] = sa[i-1] + A[i];
	for(int i = 1; i <= tot2; i++) sb[i] = sb[i-1] + B[i];
	LL ans = 0;
	for(int i = 0; i <= tot1; i++) {
		LL cur = sa[i];
		LL nn = (n-i+d) / (d+1);
		ans = max(cur+sb[nn], ans);
	}
	cout << ans << "\n";
	return 0;
}
```

## E 

**题意**

给一个图$G=(V,E)$,选择一个tuple $(c_1,\dots, c_k)$作为遍历图的规则，出度为$i$的点的下一个节点是第$c_i$小的边指向的点，问有多少种tuple可以把图遍历一遍

**Solution**

- 不能发现其实最后的路径是一个环，可以对每个点的下一个节点所组成的集合是$V$，因此可以通判断过这是否成立来检查一个tuple
- 另外可以将点按出度分类，维护$S[i][j]$表示出度为$i$的点，选第$j$小的边所到达的点的集合
- 可以用hash判断集合是否相等，有可能会冲突

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
#include <unordered_map>
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
const int MAX = 2e6 + 500;
#endif
const int mod = 1e9 + 7;
void file_read() {
#ifdef DEBUG
	freopen("in", "r", stdin);
	// freopen("out", "w", stdout);
#endif
}

#define ULL unsigned long long
const int mod2 = 998244353;
vector<P> G[MAX];
ULL s[100][100];
LL g[100][100];
LL t[100][100];
bool vis[MAX];
ULL val[MAX];
ULL TARGET1 = 1;
LL TARGET2 = 1;
LL TARGET3 = 1;
int n, m, k;
int ans;
void dfs(int u) {
	vis[u] = 1;
	for(int i = 0; i < G[u].size(); i++) {
		int v = G[u][i].first;
		s[G[u].size()][i+1] *= (ULL)val[v];
		g[G[u].size()][i+1] = g[G[u].size()][i+1] * val[v] % mod;
		t[G[u].size()][i+1] = t[G[u].size()][i+1] * val[v] % mod2;
		if(vis[v]) continue;
		dfs(v);
	}
}
void dfs2(int x, ULL cur1, LL cur2, LL cur3) {
	if(x > k) {
		if(cur1 == TARGET1 and cur2 == TARGET2 and cur3 == TARGET3) ans ++;
		return ;
	}
	for(int i = 1; i <= x; i++) {
		dfs2(x+1, cur1*s[x][i], cur2*g[x][i]%mod, cur3 * t[x][i] % mod2);
	}
}
int main() {
	srand((int)time(0));
	file_read();
	for(int i = 0; i < 100; i++) for(int j = 0; j < 100; j++) s[i][j] = g[i][j] = t[i][j] = 1;
	scanf("%d%d%d", &n, &m, &k);
	for(int i = 1; i <= n; i++) val[i] = rand() % mod;
	for(ULL i = 1; i <= n; i++) TARGET1 = TARGET1 * val[i], TARGET2 = TARGET2 * val[i] % mod, TARGET3 = TARGET3 * val[i] % mod2;
	for(int i = 1; i <= m; i++) {
		int u, v, w;
		scanf("%d%d%d", &u, &v, &w);
		G[u].push_back({v, w});
	}
	for(int i = 1; i <= n; i++) {
		sort(G[i].begin(), G[i].end(), [](const P&A, const P&B){
			return A.second < B.second;
		});
	}
	dfs(1);
	dfs2(1, 1, 1, 1);
	printf("%d\n", ans);
	return 0;
}
```

## F

**题意**

定义串只有`N`和`B`，有四种操作，给$s_1, \dots, s_n$，求一个串$t$使得和$s$的最大距离最小

**Solution**

- 不难发现字符串对应一个坐标，对于一个$x,y$可以移动上下左右，或者右上，左下
- 对于一个点，在固定距离内可以访问的点是一个六边形
- 要找一个最小半径的六边形覆盖所有的点
- 因此二分半径，计算六边形中心的可行区域
- 最后在可行区域选一个作为$t$,不能是空串

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
#include <unordered_map>
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
const LL __inf64 = 0x3f3f3f3f3f3f3f3f;
#ifdef DEBUG
const int MAX = 2e3 + 50;
#else
const int MAX = 2e6 + 500;
#endif
const int mod = 1e9 + 7;
void file_read() {
#ifdef DEBUG
	freopen("in", "r", stdin);
	// freopen("out", "w", stdout);
#endif
}

vector<P> pt;
int xL, xR, yL, yR, zL, zR;
bool check(int mid) {
	xL = -inf, xR = inf, yL = -inf, yR = inf, zL = -inf, zR = inf;
	for(auto p : pt) {
		xL = max(xL, p.first-mid);
		xR = min(xR, p.first+mid);
		yL = max(yL, p.second-mid);
		yR = min(yR, p.second+mid);
		zL = max(zL, p.first-p.second-mid);
		zR = min(zR, p.first-p.second+mid);
	}
	if(xL > xR || yL > yR || zL > zR) return false;
	yL = max(yL, xL-zR);
	yR = min(yR, xR-zL);
	if(yL > yR) return false;
	return true;
}
int main() {
	file_read();
	int n;
	cin >> n;
	for(int i = 1; i <= n; i++) {
		string s;
		cin >> s;
		int x = 0, y = 0;
		for(auto c : s) {
			if(c == 'B') x++;
			else y++;
		}
		pt.emplace_back(x, y);
	}
	int l = 0, r = inf;
	while(l < r) {
		int mid = (l + r) >> 1;
		if(check(mid)) r = mid;
		else l = mid + 1;
	}
	// cout << check(l) << "\n";
	check(l);
	int ansx, ansy;
	for(int x = max(0, xL); x <= xR; x++) {
		int curyL = max(yR, x-zR);
		int curyR = min(yR, x-zL);
		if(curyL > curyR) continue;
		if(x > 0 || curyR > 0) {
			ansx = x;
			ansy = curyR;
			break;
		}		
	}
	cout << l << "\n";
	while(ansx--) printf("B");
	while(ansy--) printf("N");
	return 0;
}
```