<div align = "center">FWT</div>

$序列长度为2^k$

```c++
const int N = 200005;
int n, m, k;
ll A[N], B[N], a[N], b[N];
constexpr ll mod = 998244353;

ll fpow(ll x, ll r)
{
    ll ans = 1;
    while (r)
    {
        if (r & 1)ans = ans * x % mod;
        r >>= 1;
        x = x * x % mod;
    }
    return ans;
}

void in()
{
    for (int i = 0; i < n; i++)
        a[i] = A[i], b[i] = B[i];
}

void out()
{
    for (int i = 0; i < n; i++)
        printf("%d ", a[i]);
    puts("");
}

void mul(int n)
{
    for (int i = 0; i < n; i++)
        a[i] = a[i] * b[i] % mod;
}

void AND(ll *f, ll x, int n)//n是2的次幂
{
    for (int o = 2, k = 1; o <= n; o <<= 1, k <<= 1)
        for (int i = 0; i < n; i += o)
            for (int j = 0; j < k; j++)
                f[i + j] = (f[i + j] + f[i + j + k] * x) % mod;
}

void OR(ll *f, ll x, int n)
{
    for (int o = 2, k = 1; o <= n; o <<= 1, k <<= 1)
        for (int i = 0; i < n; i += o)
            for (int j = 0; j < k; j++)
                f[i + j + k] = (f[i + j + k] + f[i + j] * x) % mod;
}

void XOR(ll *f, ll x, int n)
{
    for (int o = 2, k = 1; o <= n; o <<= 1, k <<= 1)
        for (int i = 0; i < n; i += o)
            for (int j = 0; j < k; j++)
            {
                ll u = f[i + j] + f[i + j + k];
                ll v = f[i + j] - f[i + j + k] + mod;
                f[i + j] = u * x % mod;
                f[i + j + k] = v * x % mod;
            }
}

int main()
{
    int p, q, u, v, w, x, y, z, T;
    cin >> n;
    n = 1 << n;
    for (int i = 0; i < n; i++)
        scanf("%d", &A[i]);
    for (int i = 0; i < n; i++)
        scanf("%d", &B[i]);
    in(), OR(a, 1, n), OR(b, 1, n), mul(n), OR(a, mod - 1, n), out();
    in(), AND(a, 1, n), AND(b, 1, n), mul(n), AND(a, mod - 1, n), out();
    in(), XOR(a, 1, n), XOR(b, 1, n), mul(n), XOR(a, fpow(2, mod - 2), n), out();
    return 0;
}
```