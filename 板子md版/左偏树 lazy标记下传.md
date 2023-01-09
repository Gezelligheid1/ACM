<div align = "center">左偏树 lazy标记下传</div>

```c++
const int N = 300005;
int n, m, k;
ll a[N], add[N], mul[N], h[N], op[N], val[N];
int rt[N], lc[N], rc[N], dist[N], f[N], c[N];
vector<int> vec[N];

inline void pushdown(int k)
{
    if (mul[k] != 1)
    {
        if (lc[k])
            mul[lc[k]] *= mul[k], add[lc[k]] *= mul[k], a[lc[k]] *= mul[k];
        if (rc[k])
            mul[rc[k]] *= mul[k], add[rc[k]] *= mul[k], a[rc[k]] *= mul[k];
        mul[k] = 1;
    }
    if (add[k])
    {
        if (lc[k])
            add[lc[k]] += add[k], a[lc[k]] += add[k];
        if (rc[k])
            add[rc[k]] += add[k], a[rc[k]] += add[k];
        add[k] = 0;
    }
}

int merge(int x, int y)
{
    if (!x || !y)return x | y;
    if (a[x] > a[y])swap(x, y);
    pushdown(x), pushdown(y);
    rc[x] = merge(rc[x], y);
    if (dist[lc[x]] < dist[rc[x]])swap(lc[x], rc[x]);
    dist[x] = dist[rc[x]] + 1;
    return x;
}

inline int pop(int x)
{
    pushdown(x);
    return merge(lc[x], rc[x]);
}

int ans1[N], ans2[N];
int d[N];

void dfs(int u)
{
    d[u] = d[f[u]] + 1;
    for (auto v:vec[u])
    {
        dfs(v);
        if (op[v] == 0)
        {
            a[rt[v]] += val[v];
            add[rt[v]] += val[v];
        }
        else
        {
            a[rt[v]] *= val[v];
            mul[rt[v]] *= val[v];
            add[rt[v]] *= val[v];
        }
        rt[u] = merge(rt[u], rt[v]);
    }
    while (rt[u] && a[rt[u]] < h[u])
    {
        ans2[rt[u]] = d[c[rt[u]]] - d[u];
        rt[u] = pop(rt[u]);
        ans1[u]++;
    }
}

int main()
{
    int p, q, u, v, w, x, y, z, T;
    dist[0] = -1;//别忘了！！！
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
        scanf("%lld", &h[i]);
    for (int i = 2; i <= n; i++)
    {
        scanf("%d %lld %lld", &f[i], &op[i], &val[i]);
        vec[f[i]].emplace_back(i);
    }
    for (int i = 1; i <= m; i++)
    {
        mul[i] = 1;
        scanf("%lld%d", &a[i], &c[i]);
        if (!rt[c[i]])rt[c[i]] = i;
        else rt[c[i]] = merge(rt[c[i]], i);
    }
    dfs(1);
    while (rt[1])
    {
        ans2[rt[1]] = d[c[rt[1]]];
        rt[1] = pop(rt[1]);
    }
    for (int i = 1; i <= n; i++)
        printf("%d\n", ans1[i]);
    for (int i = 1; i <= m; i++)
        printf("%d\n", ans2[i]);
    return 0;
}
```