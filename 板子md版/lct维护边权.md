<div align = "center">lct维护边权</div>

```c++
const int N = 200005;
int n, m, k;

struct lct
{
    int c[N], g[N], f[N], son[N][2], rev[N], stk[N];
#define ls son[x][0]
#define rs son[x][1]

    int cmp(int x, int y)
    {
        return c[x] > c[y] ? x : y;
    }

    int is_root(int x)
    {
        return son[f[x]][0] != x && son[f[x]][1] != x;
    }

    void pushup(int x)
    {
        g[x] = cmp(x, cmp(g[ls], g[rs]));
    }

    void pushrev(int x)
    {
        swap(ls, rs);
        rev[x] ^= 1;
    }

    void pushdown(int x)
    {
        if (rev[x])
        {
            if (ls)pushrev(ls);
            if (rs)pushrev(rs);
            rev[x] = 0;
        }
    }

    int get(int x) { return son[f[x]][1] == x; }

    void rotate(int x)
    {
        int y = f[x], z = f[y], k = get(x);
        if (!is_root(y))son[z][son[z][1] == y] = x;
        son[y][k] = son[x][!k], f[son[x][!k]] = y;
        son[x][!k] = y, f[y] = x, f[x] = z;
        pushup(y), pushup(x);
    }

    void splay(int x)
    {
        int now = x, top = 0;
        while (!is_root(now))
            stk[++top] = now, now = f[now];
        stk[++top] = now;
        while (top)pushdown(stk[top--]);
        for (int fa; fa = f[x], !is_root(x); rotate(x))
            if (!is_root(fa))rotate(get(fa) == get(x) ? fa : x);
    }

    void access(int x)
    {
        for (int y = 0; x; x = f[y = x])
            splay(x), rs = y, pushup(x);
    }

    void makeroot(int x)
    {
        access(x), splay(x);
        pushrev(x);
    }

    int findroot(int x)
    {
        access(x), splay(x);
        while (ls)pushdown(x), x = ls;
        splay(x);
        return x;
    }

    void split(int x, int y)
    {
        makeroot(x);
        access(y), splay(y);
    }

    void link(int x, int y)
    {
        makeroot(x);
        if (findroot(y) != x)f[x] = y;
    }

    void cut(int x, int y)
    {
        makeroot(x);
        if (findroot(y) == x && f[y] == x && !son[y][0])
        {
            f[y] = son[x][1] = 0;
            pushup(x);
        }
    }

#undef ls
#undef rs
} t;

struct point
{
    int u, v, a, b, id;
};
vector<point> vec;
tuple<int, int, int> vis[N];

int main()
{
    int p, q, u, v, w, x, y, z, T;
    cin >> n >> m;
    for (int i = 1; i <= m; i++)
        scanf("%d%d%d%d", &u, &v, &x, &y), vec.push_back({u, v, x, y, i + n}), vis[i + n] = {u, v, y};
    int ans = 1e9;
    sort(vec.begin(), vec.end(), [&](point u, point v)
    {
        return u.a < v.a;
    });
    for (auto[u, v, a, b, id]:vec)
    {
        if (t.findroot(u) != t.findroot(v))
        {
            t.c[id] = b;
            t.link(u, id);
            t.link(v, id);
        }
        else
        {
            t.split(u, v);
            int ID = t.g[v];
            auto[x, y, z]=vis[ID];
            if (z > b)
            {
                t.cut(x, ID);
                t.cut(y, ID);
                t.c[id] = b;
                t.link(u, id);
                t.link(v, id);
            }
        }
        if (t.findroot(1) == t.findroot(n))
        {
            t.split(1, n);
            ans = min(ans, a + t.c[t.g[n]]);
        }
    }
    if (ans != 1e9)
        cout << ans;
    else puts("-1");
    return 0;
}
```