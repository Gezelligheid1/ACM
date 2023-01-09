<div align = "center">强连通分量Kosaraju</div>

```c++
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <string>
#include <algorithm>
#include <vector>
#include <queue>
#include <map>
#include <set>
#include <stack>

using namespace std;
typedef long long ll;
const int N = 200005;
int a[N], vis[N], q[N], f[N];
int n, m, t;
struct Edge
{
    int to, w, prev;
} edge[2][N];
int head[2][N];
int cnt = 0;
map<int, ll> mp;

void init()
{
    t = cnt = 0;
    for (int i = 0; i <= n; i++)
        head[0][i] = head[1][i] = -1;
}

void add(int u, int v)
{
    edge[0][++cnt].to = v;
    edge[0][cnt].prev = head[0][u];
    head[0][u] = cnt;

    edge[1][cnt].to = u;
    edge[1][cnt].prev = head[1][v];
    head[1][v] = cnt;

}

void dfs1(int u)
{
    vis[u] = 1;
    for (int i = head[0][u]; i != -1; i = edge[0][i].prev)
    {
        if (!vis[edge[0][i].to])dfs1(edge[0][i].to);
    }
    q[++t] = u;
}

void dfs2(int u, int y)
{
    vis[u] = 0, f[u] = y, mp[y]++;
    for (int i = head[1][u]; i != -1; i = edge[1][i].prev)
    {
        if (vis[edge[1][i].to])dfs2(edge[1][i].to, y);
    }
}

int main()
{
    int i, j, k, g, p, u, v, w, x, y, T;
    cin >> T;
    while (T--)
    {
        scanf("%d%d", &n, &m);
        init();
        mp.clear();
        for (i = 1; i <= m; i++)
        {
            scanf("%d%d", &u, &v);
            add(u, v);
        }
        for (i = 1; i <= n; i++)
            if (!vis[i])dfs1(i);
        for (i = t; i >= 1; i--)
            if (vis[q[i]])dfs2(q[i], q[i]);
        ll ans = 0;
        for (auto &x:mp)
            ans += x.second * (x.second - 1) / 2;
        printf("%lld\n", ans);
    }
    return 0;
}
```

