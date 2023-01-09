```c++
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <string>
#include <algorithm>
#include <vector>
#include <queue>
#include <map>
#include <set>
#include <stack>

using namespace std;
typedef long long ll;

ll a[20000005], b[20000005];//因为limit要开4n的空间.
int limit, l, r[20000005];
const ll P = 998244353;

ll fpow(ll x, ll r)
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

ll _wn[25];

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
                u = A[k], v = A[k + i] * w % P;
                A[k] = (u + v) % P, A[k + i] = (u - v + P) % P;
                w = w * wn % P;
            }
        }
    }
    if (type == -1)
    {
        ll inv = fpow(limit, P - 2);
        for (i = 0; i < limit; i++)A[i] = A[i] * inv % P;
        for (i = 1; i < limit / 2; i++)swap(A[i], A[limit - i]);
    }
}

int main()
{
    int i, j, k, n, m, t, p, q, w, x, y;
    //若有多组fft需要memset
    for (i = 0; i <= 21; i++)_wn[i] = fpow(3, (P - 1) >> i);
    scanf("%d%d", &n, &m);
    for (i = 0; i <= n; i++)//系数从低到高0-n
        scanf("%lld", &a[i]), a[i] = (a[i] + P) % P;
    for (i = 0; i <= m; i++)//系数从低到高0-m
        scanf("%lld", &b[i]), b[i] = (b[i] + P) % P;
    l = 0;
    limit = 1;
    while (limit <= n + m)limit <<= 1, l++;
    for (i = 0; i < limit; i++)//多项式从低到高系数a[0]-a[limit-1].
        r[i] = (r[i >> 1] >> 1) | ((i & 1) << (l - 1));
    NTT(a, 1);
    NTT(b, 1);
    for (i = 0; i < limit; i++)
        a[i] = a[i] * b[i] % P;
    NTT(a, -1);
    for (i = 0; i <= n + m; i++)
        printf("%lld ", a[i]);
    return 0;
}
```

