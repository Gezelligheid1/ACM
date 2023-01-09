<div align = "center">Pollard_Rho</div>

```c++
const int N = 200005;
int n, m, k;
ll Prime[11] = {2, 61, 97, 7, 13, 17, 23, 29}, Ans = -1;//注意每次调用都要重置成-1

inline ll mul(ll a, ll b, const ll mod)
{
    return __int128(a) * b % mod;
}

ll fpow(ll x, ll r, const ll mod)
{
    ll ans = 1;
    while (r)
    {
        if (r & 1)ans = mul(ans, x, mod);
        r >>= 1;
        x = mul(x, x, mod);
    }
    return ans;
}

inline bool test(ll p, ll x)
{
    ll r = 0, d = x - 1;
    while (!(d & 1)) d >>= 1, ++r;
    for (ll i = fpow(p, d, x), j; r; --r)
    {
        j = mul(i, i, x);
        if (j == 1)
        {
            if (i == 1 || i == x - 1)
                return 1;
            return 0;
        }
        i = j;
    }
    return 0;
}

#define ctz __builtin_ctzll

inline ll gcd(ll a, ll b)//一般情况可以用库函数gcd
{
    if (!a || !b) return a | b;
    int t = ctz(a | b);
    a >>= ctz(a);
    do
    {
        b >>= ctz(b);
        if (a > b)swap(a, b);
        b -= a;
    } while (b);
    return a << t;
}

inline int Miller_Rabin(ll x)
{
    if (x == Prime[1] || x == Prime[0]) return 1;
    if (!(x % Prime[1]) || !(x % Prime[0])) return 0;
    if (test(Prime[1], x) ^ 1) return 0;
    if (test(Prime[0], x) ^ 1) return 0;
    if (x == Prime[2] || x == Prime[3]) return 1;
    if (!(x % Prime[2]) || !(x % Prime[3])) return 0;
    if (test(Prime[2], x) ^ 1) return 0;
    if (test(Prime[3], x) ^ 1) return 0;
    if (x == Prime[4] || x == Prime[5]) return 1;
    if (!(x % Prime[4]) || !(x % Prime[5])) return 0;
    if (test(Prime[4], x) ^ 1) return 0;
    if (test(Prime[5], x) ^ 1) return 0;
    if (x == Prime[6] || x == Prime[7]) return 1;
    if (!(x % Prime[6]) || !(x % Prime[7])) return 0;
    if (test(Prime[6], x) ^ 1) return 0;
    if (test(Prime[7], x) ^ 1) return 0;
    return 1;
}

inline ll Irand(ll x)
{
    return 1ll * ((rand() << 15 ^ rand()) << 30 ^ (rand() << 15 ^ rand())) % x;
}

inline ll qwq(ll x)
{
    ll C = Irand(x);
    ll t1 = Irand(x), t2 = mul(t1, t1, x) + C; // 1
    ll dif = abs(t1 - t2), G = gcd(x, dif);
    while (G == 1)
    {
        t1 = mul(t1, t1, x) + C;
        if (t1 >= x) t1 -= x;
        t2 = mul(t2, t2, x) + C, t2 = mul(t2, t2, x) + C;
        if (t2 >= x) t2 -= x;
        dif = abs(t1 - t2), G = gcd(x, dif);
    }
    return G;
}

inline void pollard_rho(ll x)
{
    if (x == 1) return;
    if (Miller_Rabin(x))
    {
        Ans = max(Ans, x);
        return;
    }
    ll y = x;
    while (y == x) y = qwq(x);
    pollard_rho(y), pollard_rho(x / y);
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    int p, q, u, v, w, x, y, z, T;
    cin >> T;
    while (T--)
    {
        ll n;
        cin >> n;
        Ans = -1;//注意每次调用都要重置成-1
        pollard_rho(n);
        if (Ans == n)cout << "Prime" << '\n';
        else cout << Ans << '\n';
    }
    return 0;
}
```