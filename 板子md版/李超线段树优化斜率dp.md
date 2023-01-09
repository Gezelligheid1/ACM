<div align = "center">李超线段树优化斜率dp</div>

动态开点时$cnt$一定不要重复使用！！！

李超线段树维护最小值，不需要浮点数运算

$c[k] = 0$的情况要好好考虑！！！可能一开始就会插入一条斜率为0的直线
$$
dp_i=\min_{0\le j<i}{dp_j+w_{j+1}l_i}
$$

```c++
const int N = 1000005;
int n, m, k;
struct line
{
    ll k, b;
} s[N];
int c[N << 2];//存编号
int cnt;

inline int cmp(ll x, ll y)
{
    if (x - y > 0)return 1;
    else if (x - y < 0)return -1;
    else return 0;
}

inline ll f(int id, ll x)
{
    if (id == 0)return 1e18;//要看是否允许0
    else return s[id].k * x + s[id].b;
}

void update(int l, int r, int k, int u)
{
    int &v = c[k];
    int m = (l + r) >> 1;
    if (cmp(f(u, m), f(v, m)) == -1)swap(u, v);
    int bl = cmp(f(u, l), f(v, l)), br = cmp(f(u, r), f(v, r));
    if (bl == -1)update(l, m, k << 1, u);
    if (br == -1)update(m + 1, r, k << 1 | 1, u);
}

void insert(int L, int R, int l, int r, int k, int u)
{
    if (L <= l && r <= R)
    {
        update(l, r, k, u);
        return;
    }
    int m = (l + r) >> 1;
    if (L <= m)insert(L, R, l, m, k << 1, u);
    if (R > m)insert(L, R, m + 1, r, k << 1 | 1, u);
}

ll query(int l, int r, int k, int x)
{
    //这边可以加一个!c[k]的判断保护机制
    ll ans = c[k] ? f(c[k], x) : 1e18;
    if (l == r)return ans;
    int m = (l + r) >> 1;
    if (x <= m)ans = min(ans, query(l, m, k << 1, x));
    else ans = min(ans, query(m + 1, r, k << 1 | 1, x));
    return ans;
}

struct point
{
    int w, l;
} t[N];
ll dp[N];

int main()
{
    int p, q, u, v, w, x, y, z, T;
    cin >> n;
    for (int i = 1; i <= n; i++)
        scanf("%d%d", &t[i].w, &t[i].l);
    sort(t + 1, t + n + 1, [&](point u, point v)
    {
        if (u.w != v.w)return u.w > v.w;
        else return u.l > v.l;
    });
    vector<point> vec{t[0], t[1]};
    int last = t[1].l;
    for (int i = 2; i <= n; i++)
        if (t[i].l > last)
        {
            vec.emplace_back(t[i]);
            last = t[i].l;
        }
    int len = 1e6;
    for (int i = 1; i < vec.size(); i++)
    {
        s[++cnt] = {vec[i].w, dp[i - 1]};
        insert(0, len, 0, len, 1, cnt);
        dp[i] = query(0, len, 1, vec[i].l);
    }
    cout << dp[vec.size() - 1];
    return 0;
}
```

