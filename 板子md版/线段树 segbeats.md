<div align = "center">线段树 segbeats</div>

长为$n$的序列$a$，有$m$个操作
$$
\begin{aligned}
&\ 0\ x\ y\ t:对于区间[x,y],a_i=min(a_i,t)\\
&\ 1\ x\ y:输出区间[x,y]中a_i的最大值\\
&\ 2\ x\ y:输出区间[x,y]中a_i的权值和
\end{aligned}
$$

```c++
const int N = 1000005;
int n, m, k;
int a[N];

struct point
{
    ll sum;
    int max, se_max, cnt;

    friend point operator+(const point &u, const point &v)
    {
        point ans;
        ans.sum = u.sum + v.sum;
        if (u.max == v.max)
        {
            ans.max = u.max;
            ans.se_max = std::max(u.se_max, v.se_max);
            ans.cnt = u.cnt + v.cnt;
        }
        else if (u.max > v.max)
        {
            ans.max = u.max;
            ans.se_max = std::max(u.se_max, v.max);
            ans.cnt = u.cnt;
        }
        else
        {
            ans.max = v.max;
            ans.se_max = std::max(v.se_max, u.max);
            ans.cnt = v.cnt;
        }
        return ans;
    }
} c[N << 2];

int lazy[N << 2];

inline void pushup(int k)
{
    c[k] = c[k << 1] + c[k << 1 | 1];
}

void build(int l, int r, int k)
{
    if (l == r)
    {
        c[k].max = c[k].sum = a[l];
        c[k].se_max = -1e9, c[k].cnt = 1;
        return;
    }
    lazy[k] = -1;
    int m = (l + r) >> 1;
    build(l, m, k << 1);
    build(m + 1, r, k << 1 | 1);
    pushup(k);
}

inline void pushtag(int k, ll v)
{
    if (v >= c[k].max)return;
    c[k].sum += (v - c[k].max) * c[k].cnt;
    c[k].max = lazy[k] = v;
}

inline void pushdown(int k)
{
    if (lazy[k] != -1)
    {
        pushtag(k << 1, lazy[k]);
        pushtag(k << 1 | 1, lazy[k]);
        lazy[k] = -1;
    }
}

void update(int L, int R, int l, int r, int k, ll v)
{
    if (c[k].max <= v)return;
    if (L <= l && r <= R && c[k].se_max < v)return pushtag(k, v);
    int m = (l + r) >> 1;
    pushdown(k);
    if (L <= m) update(L, R, l, m, k << 1, v);
    if (R > m) update(L, R, m + 1, r, k << 1 | 1, v);
    pushup(k);
}

ll query_sum(int L, int R, int l, int r, int k)
{
    if (L <= l && r <= R)return c[k].sum;
    int m = (l + r) >> 1;
    ll ans = 0;
    pushdown(k);
    if (L <= m)ans += query_sum(L, R, l, m, k << 1);
    if (R > m)ans += query_sum(L, R, m + 1, r, k << 1 | 1);
    return ans;
}

ll query_max(int L, int R, int l, int r, int k)
{
    if (L <= l && r <= R)return c[k].max;
    int m = (l + r) >> 1;
    ll ans = -1e9;
    pushdown(k);
    if (L <= m)ans = query_max(L, R, l, m, k << 1);
    if (R > m)ans = max(ans, query_max(L, R, m + 1, r, k << 1 | 1));
    return ans;
}

int main()
{
    int p, q, u, v, x, y, z, T;
    cin >> T;
    while (T--)
    {
        scanf("%d%d", &n, &m);
        for (int i = 1; i <= n; i++)
            scanf("%d", &a[i]);
        build(1, n, 1);
        while (m--)
        {
            int op;
            scanf("%d%d%d", &op, &x, &y);
            if (op == 0)
            {
                scanf("%d", &v);
                update(x, y, 1, n, 1, v);
            }
            else if (op == 1)printf("%d\n", query_max(x, y, 1, n, 1));
            else printf("%lld\n", query_sum(x, y, 1, n, 1));
        }
    }
    return 0;
}
```