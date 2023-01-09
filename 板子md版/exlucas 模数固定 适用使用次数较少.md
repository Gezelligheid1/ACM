<div align = "center">exlucas 模数固定 适用使用次数较少</div>

```c++
namespace exlucas {
    constexpr ll P = 999911658;
    vector<pair<ll, ll>> factor = []
    {
        ll now = P;
        vector<pair<ll, ll>> factor;
        for (ll i = 2; i * i <= now; i++)
        {
            if (now % i)continue;
            ll tmp = 1;
            while (now % i == 0)
                now /= i, tmp *= i;
            factor.emplace_back(i, tmp);
        }
        if (now > 1)factor.emplace_back(now, now);
        return factor;
    }();

    ll fpow(ll x, ll r, const ll P)
    {
        ll result = 1;
        while (r)
        {
            if (r & 1)result = result * x % P;
            r >>= 1;
            x = x * x % P;
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

    inline ll inv(ll a, const ll P)
    {
        ll x, y;
        exgcd(a, P, x, y);
        return (x % P + P) % P;
    }

    ll F(ll n, const ll P, const ll PK)//求阶乘
    {
        if (n == 0)return 1;
        ll ans = 1;
        for (ll i = 1; i < PK; i++)
            if (i % P)ans = ans * i % PK;
        ans = fpow(ans, n / PK, PK);//!!!
        for (ll i = 1; i <= n % PK; i++)
            if (i % P)ans = ans * i % PK;
        return F(n / P, P, PK) * ans % PK;
    }

    ll G(ll n, const ll P)
    {
        ll ans = 0;
        while (n)
            ans += n / P, n /= P;
        return ans;
    }

    ll Binom(ll n, ll m, const ll P, const ll PK)
    {
        if (n < m || m < 0)return 0;
        ll f1 = F(n, P, PK), f2 = F(m, P, PK), f3 = F(n - m, P, PK);
        ll cnt = G(n, P) - G(m, P) - G(n - m, P);
        return f1 * inv(f2, PK) % PK * inv(f3, PK) % PK * fpow(P, cnt, PK) % PK;
    }

    ll crt(vector<ll> &r, vector<ll> &m)//x ≡ r(mod m)
    {
        assert(r.size() == m.size());
        ll M = 1, ans = 0;
        for (auto x:m)
            M *= x;
        for (int i = 0; i < r.size(); i++)
            ans = (ans + (__int128) (M / m[i]) * inv(M / m[i], m[i]) % M * r[i]) % M;
        return (ans + M) % M;
    }

    ll exlucas(ll n, ll m)//根号P预处理
    {
        vector<ll> R, M;
        R.reserve(factor.size());
        M.reserve(factor.size());
        for (auto[x, y]:factor)
        {
            R.emplace_back(Binom(n, m, x, y));
            M.emplace_back(y);
        }
        return crt(R, M);
    }
}
```