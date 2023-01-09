$$
给定序列g[1]-g[n-1]，求序列f[0]-f[n-1]。\\
  f[i]=\sum_{j=1}^{i}f[i-j]*g[j],f[0]=1
$$

```c++
#include <bits/stdc++.h>
#include <ext/pb_ds/tree_policy.hpp>
#include <ext/pb_ds/trie_policy.hpp>
#include <ext/pb_ds/assoc_container.hpp>
#include <ext/pb_ds/priority_queue.hpp>

using namespace std;
using namespace __gnu_pbds;
using namespace __gnu_cxx;
typedef long long ll;
//tr<int, null_type, less<int>, rb_tree_tag, tree_order_statistics_node_update> tr;
//__gnu_pbds::priority_queue<int, greater<int>, pairing_heap_tag> qu;
//typedef trie<string, null_type, trie_string_access_traits<>, pat_trie_tag, trie_prefix_search_node_update> pref_trie;
const int N = 200005;
int n, m, k;
ll a[800005], f[800005], g[800005], ans[800005];//因为limit要开4n的空间.
int limit, r[800005];
const ll mod = 998244353;

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

ll _wn[25];

void init()
{
    for (int i = 0; i < limit; i++)//多项式从低到高系数a[0]-a[limit-1].
        r[i] = (r[i >> 1] >> 1) | ((i & 1) ? limit >> 1 : 0);
}

void NTT(ll *A, int type)
{
    int i, j = limit >> 1, k, l, c = 0;
    ll u, v, w, wn;
    for (i = 1; i < limit - 1; i++)
    {
        if (i < j)swap(A[i], A[j]);
        for (k = limit >> 1; (j ^= k) < k; k >>= 1);
    }

    for (l = 2; l <= limit; l <<= 1)
    {
        i = l >> 1, wn = _wn[++c];
        for (j = 0; j < limit; j += l)
        {
            w = 1;
            for (k = j; k < j + i; k++)
            {
                u = A[k], v = A[k + i] * w % mod;
                A[k] = (u + v) % mod, A[k + i] = (u - v + mod) % mod;
                w = (ll) w * wn % mod;
            }
        }
    }
    if (type == -1)
    {
        ll inv = fpow(limit, mod - 2);
        for (i = 0; i < limit; i++)A[i] = A[i] * inv % mod;
        for (i = 1; i < limit / 2; i++)swap(A[i], A[limit - i]);
    }

}

void solve(int l, int r)//左闭右开
{
    if (l + 1 >= r)return;
    int mid = (l + r) >> 1, i;
    solve(l, mid);
    limit = r - l;//刚好是2的次幂
    init();
    for (i = 0; i < limit; i++)
        g[i] = a[i];
    for (i = l; i < mid; i++)
        f[i - l] = ans[i];
    for (i = mid; i < r; i++)
        f[i - l] = 0;
    NTT(f, 1);
    NTT(g, 1);
    for (i = 0; i < limit; i++)
        f[i] = f[i] * g[i] % mod;
    NTT(f, -1);
    for (i = mid; i < r; i++)
        ans[i] = (ans[i] + f[i - l]) % mod;
    solve(mid, r);
}

int main()
{
    int i, j, t, p, q, w, x, y;
    //若有多组fft需要memset
    for (i = 0; i <= 21; i++)_wn[i] = fpow(3, (mod - 1) >> i);
    scanf("%d", &n);
    for (i = 1; i < n; i++)//系数从低到高0-n
        scanf("%lld", &a[i]);//a[0] = 0
    limit = 1;
    while (limit <= n)limit <<= 1;//题目要求算f[0]-f[n-1]
    ans[0] = 1;//题目要求
    solve(0, limit);
    for (i = 0; i < n; i++)
        printf("%lld ", ans[i]);
    return 0;
}
```

