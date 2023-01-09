<div align = "center">dsu on tree维护多项式</div>

```c++
const int N = 200005;
int n, m, k;
int a[N];
poly F[N], G[N];
vector<int> t[N];

void dfs2(int u, int topu)
{
    t[topu].emplace_back(u);
    vector<poly> V;

    for (auto v:vec[u])
    {
        if (v == son[u])continue;//如果是平常存边方式应是v==f[u]||v==son[u]
        dfs2(v, v);
        V.emplace_back(F[v]);
    }

    function<poly(int, int)> solve = [&](int l, int r)
    {
        if (l == r)return V[l];
        int m = (l + r) >> 1;
        return solve(l, m) * solve(m + 1, r);
    };

    if (V.empty())G[u] = poly{1};
    else G[u] = solve(0, V.size() - 1);

    if (son[u])dfs2(son[u], topu);//注意是topu

    if (u == topu)
    {
        function<pair<poly, poly>(int, int)> solve = [&](int l, int r)
        {
            if (l == r)return pair<poly, poly>{G[t[topu][l]], poly{0, 1}};
            int m = (l + r) >> 1;
            auto[AL, BL]=solve(l, m);
            auto[AR, BR]=solve(m + 1, r);
            return pair<poly, poly>{AL * AR, BL * AR + BR};
        };
        reverse(t[topu].begin(), t[topu].end());//需要reverse
        auto[A, B]=solve(0, t[topu].size() - 1);
        F[u] = A + B;
    }
}

int main()
{
    int p, q, u, v, w, x, y, z, T;
    cin >> n;
    for (int i = 2; i <= n; i++)
        scanf("%d", &u), vec[u].emplace_back(i);
    dfs1(1);
    dfs2(1, 1);
    F[1].resize(n + 1);
    for (int i = 1; i <= n; i++)
        printf("%lld\n", F[1][i]);
    return 0;
}
```

