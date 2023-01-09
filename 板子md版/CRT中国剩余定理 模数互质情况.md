<div align = "center">CRT中国剩余定理 模数互质情况</div>

```c++
void exgcd(ll a, ll b, ll &x, ll &y)//ax + by = gcd(a,b)
{
    if (!b)
    {
        x = 1;
        y = 0;
        return;
    }
    exgcd(b, a % b, x, y);
    ll t = x;
    x = y;
    y = t - a / b * y;
}

ll crt(int a[], int m[], int n)//x ≡ a(mod m)
{
    ll M = 1;
    for (int i = 1; i <= n; i++)
        M = M * m[i];
    ll ans = 0;
    for (int i = 1; i <= n; i++)
    {
        ll x, y;
        ll mt = M / m[i];
        exgcd(mt, m[i], x, y);
        ans = (ans + (__int128) mt * x % M * a[i]) % M;
    }
    return (ans + M) % M;
}
```

