```c++
ll fand(ll x, ll r, const ll mod)
{
    ll result = 0;
    while (r)
    {
        if (r & 1)result = (result + x) % mod;
        r >>= 1;
        x = x * 2 % mod;
    }
    return result;
}

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

ll excrt(ll a[], ll m[], int n)//a为余数,m为模数
{
    int i;
    ll x, y, c, gcd, k1;
    ll res = a[0];//res为前k-1个方程的解.
    ll M = m[0];//M为前k-1个数的lcm.
    for (i = 1; i < n; i++)
    {
        c = (a[i] - res % m[i] + m[i]) % m[i];
        gcd = exgcd(M, m[i], x, y);
        ll m1 = m[i] / gcd;
        if (c % gcd)return -1;//无解情况
        k1 = (fand(x, c / gcd, m1) + m1) % m1;
        res += k1 * M;
        M = M / gcd * m[i];
    }
    return res;//非负整数解中的最小值
}
```

