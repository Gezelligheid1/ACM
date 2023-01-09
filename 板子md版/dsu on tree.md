<div align = "center">dsu on tree</div>

```c++
const int N = 200005;
int n, m, k;
int a[N];
int sz[N], son[N], cnt[N];
vector<int> vec[N];
ll ans[N];
ll sum, mx;

void dfs1(int u, int fa)
{
    sz[u] = 1;
    for (auto v :vec[u])
    {
        if (v == fa)continue;
        dfs1(v, u);
        sz[u] += sz[v];
        if (sz[son[u]] < sz[v])son[u] = v;
    }
}

void clear(int u, int fa)
{
    cnt[a[u]]--;
    for (auto v:vec[u])
    {
        if (v == fa)continue;
        clear(v, u);
    }
}

void calc(int u, int fa, int Son)
{
    cnt[a[u]]++;
    if (mx < cnt[a[u]])mx = cnt[a[u]], sum = a[u];
    else if (mx == cnt[a[u]])sum += a[u];
    for (auto v:vec[u])
    {
        if (v == fa || v == Son)continue;//calc由于是dfs2中的节点继承重儿子,所以这里不要遍历它的重儿子!!!
        calc(v, u, Son);
    }
}

void dfs2(int u, int fa)
{
    for (auto v:vec[u])
    {
        if (v == fa || v == son[u])continue;
        dfs2(v, u);
        clear(v, u);
        sum = mx = 0;
    }
    if (son[u])dfs2(son[u], u);
    calc(u, fa, son[u]);
    ans[u] = sum;
}

int main()
{
    int p, q, u, v, x, y, z, T;
    cin >> n;
    for (int i = 1; i <= n; i++)
        scanf("%d", &a[i]);
    for (int i = 1; i < n; i++)
        scanf("%d%d", &u, &v), vec[u].emplace_back(v), vec[v].emplace_back(u);
    dfs1(1, 0);
    dfs2(1, 0);
    for (int i = 1; i <= n; i++)
        printf("%lld ", ans[i]);
    return 0;
}
```