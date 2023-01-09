<div align = "center">Lucas</div>

```c++
const int N = 200005;
int n, m, k;
ll fac[N], ifac[N];
ll mod;

ll fpow(ll x, ll r)
{
    ll result = 1;
    while (r)
    {
        if (r & 1)result = result * x % mod;
        r >>= 1;
        x = x * x % mod;
    }
    return result;
}

inline ll C(int n, int m)
{
    return fac[n] * ifac[m] % mod * ifac[n - m] % mod;
}

ll lucas(int n, int m)
{
    if (!m)return 1;
    return C(n % mod, m % mod) * lucas(n / mod, m / mod) % mod;
}

int main()
{
    int i, j, t, g, p, q, u, v, w, x, y, z, T;
    cin >> T;
    while (T--)
    {
        scanf("%d%d%lld", &n, &m, &mod);
        fac[0] = 1;
        for (i = 1; i < mod; i++)
            fac[i] = fac[i - 1] * i % mod;
        ifac[mod - 1] = fpow(fac[mod - 1], mod - 2);
        for (i = mod - 1; i; i--)
            ifac[i - 1] = ifac[i] * i % mod;
        printf("%lld\n", lucas(n + m, n));
    }
    return 0;
}
```

