<div align = "center">min25筛</div>

设完全积性函数$f'$在质数处取值与$f$相同

设函数$g$满足$g(n,j)=\sum\limits_{i=1}^nf'(i)(i\in prime或\min\limits_{p|i}p>P_j )$

$$
g(n,j)=\begin{cases}
g(n,j-1),(P_j^2>n)\\
g(n,j-1)-f'(P_j)(g(\frac n {P_j},j-1)-\sum\limits_{i=1}^{j-1}f'(P_i)),(P_j^2\le n)
\end{cases}
$$

本质是求质数处前缀和，有时候可以先求质数个数什么的再转化，有奇效
$$
\begin{aligned}
S(n,j)&=\sum_{i=1}^nf(i)(\min\limits_{p|i}p>P_j )\\
S(n,j)&=g(n,|P|)-\sum_{i=1}^jf'(P_i)+\sum_{k>j}\sum_{e=1}^{P_k^e\le n}f(P_k^e)(S(\frac n{P_k^e},k)+(e>1))
\end{aligned}
$$

答案$S(n,0)+f(1)$

定义积性函数$f(x)$，且$f(p^k)=p^{2k}-p^k$（$p$是一个质数），求

$$
\sum_{i=1}^n f(i)
$$
对$10^9+7$取模。$1\le n\le10^{10}$

```c++
const int N = 200005;//N开sqrt两倍
ll n, m, k;
int id1[N], id2[N];
ll sum1[N], sum2[N], w[N], g1[N], g2[N], prime[N];//注意prime开long long！！！
int vis[N];
int cnt, t, sz;//多次时记得t清零或者开成局部变量
const ll mod = 1e9 + 7;

void Prime(int n)//多次记得cnt清零,vis清零
{
    for (int i = 2; i <= n; i++)
    {
        if (!vis[i])prime[++cnt] = i;
        for (int j = 1; j <= cnt && i * prime[j] <= n; j++)
        {
            vis[i * prime[j]] = 1;
            if (i % prime[j] == 0)break;
        }
    }
    for (int i = 1; i <= cnt; i++)
    {
        sum1[i] = (sum1[i - 1] + prime[i]) % mod;
        sum2[i] = (sum2[i - 1] + prime[i] * prime[i]) % mod;
    }
}

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

inline ll f1(ll x)// 1 ~ x前缀和
{
    x %= mod;
    return x * (x + 1) / 2 % mod;
}

inline ll f2(ll x)
{
    static const ll inv6 = fpow(6, mod - 2);
    x %= mod;
    return x * (x + 1) % mod * (2 * x + 1) % mod * inv6 % mod;
}

inline int getid(ll x)//注意这边的n是全局变量
{
    if (x <= sz)return id1[x];
    else return id2[n / x];
}

ll S(ll n, int j)
{
    if (prime[j] > n)return 0;
    ll ans = (g2[getid(n)] - sum2[j] - g1[getid(n)] + sum1[j] + 2 * mod) % mod;
    for (int i = j + 1; i <= cnt && prime[i] * prime[i] <= n; i++)
        for (ll e = 1, sp = prime[i]; sp <= n; sp *= prime[i], e++)
            ans = (ans + sp % mod * ((sp - 1) % mod) % mod * (S(n / sp, i) + (e > 1))) % mod;
    return ans;
}

int main()
{
    int p, q, u, v, x, y, z, T;
    cin >> n;
    sz = sqrt(n) + 5;//sz放全局，getid要用
    Prime(sz);
    for (ll l = 1, r; l <= n; l = r + 1)
    {
        r = n / (n / l);
        w[++t] = n / l;
        g1[t] = f1(w[t]) - 1, g2[t] = f2(w[t]) - 1;//处理g(?, 0)并减去1的影响
        if (w[t] <= sz)id1[w[t]] = t;
        else id2[n / w[t]] = t;
    }
    for (int i = 1; i <= cnt; i++)
        for (int j = 1; j <= t && prime[i] * prime[i] <= w[j]; j++)//注意是prime[i],注意prime[i]开long long!!!
        {
            int id = getid(w[j] / prime[i]);
            g1[j] = (g1[j] - prime[i] * (g1[id] - sum1[i - 1]) % mod + mod) % mod;
            g2[j] = (g2[j] - prime[i] * prime[i] % mod * (g2[id] - sum2[i - 1]) % mod + mod) % mod;
        }
    cout << (S(n, 0) + 1) % mod;
    return 0;
}
```