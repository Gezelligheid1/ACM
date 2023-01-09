<div align = "center">字符串hash</div>

```c++
struct hash_T//从低到高 0次到len-1次，下标从0开始，没有无限栈情况一定要在函数内开static!!!
{
    static constexpr ll mod = 1610612741;//要求模为素数
    static constexpr ll P = 402653189;
    ll sum[N], p[N], inv_p[N];//p为power幂次
    int len;

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

    template<class T>
    hash_T(const T &s)//从0开始，左闭右开
    {
        len = s.size();
        p[0] = inv_p[0] = 1;
        const ll inv = fpow(P, mod - 2);

        for (int i = 0; i < len; i++)
        {
            sum[i] = ((i ? sum[i - 1] : 0) + s[i] * p[i]) % mod;
            p[i + 1] = p[i] * P % mod;
            inv_p[i + 1] = inv_p[i] * inv % mod;
        }
    }

    ll get(int l, int r)//从0开始
    {
//        if (r < l)return 0;//根据情况特判，比如长度为0时l=0,r=-1
        return (sum[r] - (l ? sum[l - 1] : 0) + mod) * inv_p[l] % mod;
    }
};
```



```c++
const int N = 5000005;
int n, m, k;
int a[N];
const ll mod = 1610612741;
const ll P = 402653189;

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

map<ll, int> mp;

int main()
{	
    int i, j, p, q, u, v, w, x, y, z, T;
    ios::sync_with_stdio(false);
    cin >> n;
    string s;
    for (i = 1; i <= n; i++)
    {
        cin >> s;
        ll hash = 0;
        for (j = 0; j < s.length(); j++)
            hash = (hash * P + s[j]) % mod;
        mp[hash] = 1;
    }
    cout << mp.size();
    return 0;
}
```