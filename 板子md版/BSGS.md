<div align = "center">BSGS</div>

```c++
const int N = 200005;
ll n, m, k;

ll bsgs(ll a, ll b, const ll p)//a^x = b(mod p) a^(i*t-j) = b(mod p)要求gcd(a,p)=1
{
    a %= p, b %= p;
    if (b == 1)return 0;
    if (!a)return b == 0 ? (p == 1 ? 0 : 1) : -1;//特判a==0 以及p==1时需考虑0^0等于什么，考虑a取模之前是否为0
    unordered_map < ll, ll > mp;
    ll t = sqrt(p) + 1;
    ll tmp = 1;
    for (ll i = 0; i < t; i++)
    {
        mp[b * tmp % p] = i;
        tmp = tmp * a % p;
    }
    a = tmp;//即a^t
    ll val = 1;
    for (ll i = 0; i <= t; i++)
    {
        if (i)val = val * a % p;
        ll j = mp.find(val) == mp.end() ? -1 : mp[val];
        if (j >= 0 && i * t >= j)return i * t - j;
    }
    return -1;
}

int main()
{
    ll i, j, g, p, q, u, v, w, x, y, z, T;
    ll a, b;
    scanf("%lld%lld%lld", &p, &a, &b);//注意这里p在前面!!!
    ll ans = bsgs(a, b, p);
    if (ans == -1)puts("no solution");
    else printf("%lld\n", ans);
    return 0;
}
```

