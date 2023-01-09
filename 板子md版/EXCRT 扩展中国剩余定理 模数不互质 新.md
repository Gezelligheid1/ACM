<div align = "center">EXCRT 扩展中国剩余定理 模数不互质 新</div>

```c++
ll exgcd(ll a, ll b, ll &x, ll &y)
{
    if (!b)
    {
        x = 1;
        y = 0;
        return a;
    }
    ll gcd = exgcd(b, a % b, x, y);
    ll t = x;
    x = y;
    y = t - a / b * y;
    return gcd;
}

ll excrt(ll r[], ll m[], int n)//x ≡ r(mod m)
{
    ll R = r[1], M = m[1];
    for (int i = 2; i <= n; i++)
    {
        ll x, y;
        ll d = exgcd(M, m[i], x, y);
        if ((r[i] - R) % d)return -1; //无解，可以改成assert((r[i] - R) % d == 0)
        ll lcm = M / d * m[i]; //需要保证m数组公倍数不爆ll
        R = (R + (r[i] - R) / d * (__int128) x * M) % lcm;
        M = lcm;
    }
    return (R % M + M) % M;
}
```

