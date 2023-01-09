<div align = "center">FWT_XOR 科技版</div>

```c++
const int N = 1100005;
int n, m, k;
ll a[N], b[N], val[25];
ll mod;

ll fpow(ll x, ll r)
{
    ll result = 1;
    while (r)
    {
        if (r & 1)result = (__int128) result * x % mod;
        r >>= 1;
        x = (__int128) x * x % mod;
    }
    return result;
}

void XOR(ll *f, int n, int type)
{
    for (int o = 2, k = 1; o <= n; o <<= 1, k <<= 1)
        for (int i = 0; i < n; i += o)
            for (int j = 0; j < k; j++)
            {
                ll u = f[i + j] + f[i + j + k];
                ll v = f[i + j] - f[i + j + k];
                if (u >= mod)u -= mod;
                if (v < 0)v += mod;
                f[i + j] = u;//如果改成加减号优化取模需要先对f数组预处理取模
                f[i + j + k] = v;
            }
    if (type == -1)
        for (int i = 0; i < n; i++)
            f[i] /= n;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    int p, q, u, v, w, x, y, z, T;
    ll t;
    cin >> m >> t >> mod;
    n = 1 << m;
    mod *= n;//!!!
    for (int i = 0; i < n; i++)
        cin >> a[i], a[i] %= mod;//预处理取模
    for (int i = 0; i <= m; i++)
        cin >> val[i], val[i] %= mod;
    for (int i = 0; i < n; i++)
        b[i] = val[__builtin_popcount(i)];
    XOR(a, n, 1), XOR(b, n, 1);
    for (int i = 0; i < n; i++)
    {
        b[i] = fpow(b[i], t);
        a[i] = (__int128) a[i] * b[i] % mod;
    }
    XOR(a, n, -1);
    for (int i = 0; i < n; i++)
        cout << a[i] << '\n';
    return 0;
}
```