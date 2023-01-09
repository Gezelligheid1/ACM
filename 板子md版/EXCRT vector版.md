<div align = "center">EXCRT vector版</div>

$ax+by=gcd(a,b)$，若$b\neq0$，求出来的$x,y$满足$|x|\le b,|y|\le a$

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

ll excrt(vector<ll> &r, vector<ll> &m)//x ≡ r(mod m)
{
    assert(r.size() == m.size());
    ll R = r[0], M = m[0];
    for (int i = 1; i < r.size(); i++)
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