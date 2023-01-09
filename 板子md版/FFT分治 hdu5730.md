$$
给定序列g[1]-g[n]，求f[n]。\\
f[i]=\sum_{j=1}^{i}f[i-j]*g[j],f[0]=1
$$

```c++
/*
  给定序列g[1]-g[n]，求f[n]。
  f[i]=∑f[i-j]*g[j],1≤j≤i,f[0]=1
*/

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
const int N = 100005;
int n, m, k;
ll a[400005];
ll ans[400005];//因为limit要开4n的空间.
const int mod = 313;
const double pi = acos(-1);

struct Complex
{
    double x = 0, y = 0;

    Complex(double xx = 0, double yy = 0) { x = xx, y = yy; }//c++11必须写
    friend Complex operator+(const Complex &a, const Complex &b) { return Complex{a.x + b.x, a.y + b.y}; }
    
    friend Complex operator-(const Complex &a, const Complex &b) { return Complex{a.x - b.x, a.y - b.y}; }
    
    friend Complex operator*(const Complex &a, const Complex &b)
    {
        return Complex{a.x * b.x - a.y * b.y, a.y * b.x + a.x * b.y};
    }

} f[400005], g[400005];//因为limit要开4n的空间
int limit, r[400005];

void FFT(Complex *a, int type)
{
    for (int i = 0; i < limit; i++)
        if (i < r[i])swap(a[i], a[r[i]]);//求出要迭代的序列
    for (int mid = 1; mid < limit; mid <<= 1)//待合并区间limit的一半
    {
        Complex wn{cos(pi / mid), type * sin(pi / mid)};
        for (int R = mid << 1, j = 0; j < limit; j += R)//R是区间的长度，j表示前已经到哪个位置了
        {
            Complex w{1, 0};
            for (int k = 0; k < mid; k++, w = w * wn)//枚举左半部分
            {
                Complex x = a[j + k], y = w * a[j + mid + k];
                a[j + k] = x + y;
                a[j + mid + k] = x - y;
            }
        }
    }
    if (type == -1)
    {
        for (int i = 0; i < limit; i++)
            a[i].x = round(a[i].x / limit);
    }
}

void init()
{
    for (int i = 0; i < limit; i++)//多项式从低到高系数a[0]-a[limit-1].
        r[i] = (r[i >> 1] >> 1) | ((i & 1) ? limit >> 1 : 0);
}

void solve(int l, int r)//左闭右开
{
    if (l + 1 >= r)return;
    int mid = (l + r) >> 1, i;
    solve(l, mid);
    limit = r - l;//刚好是2的次幂
    init();
    for (i = 0; i < limit; i++)
        g[i] = {(double) a[i], 0};
    for (i = l; i < mid; i++)
        f[i - l] = {(double) ans[i], 0};
    for (i = mid; i < r; i++)
        f[i - l] = {0, 0};
    FFT(f, 1);
    FFT(g, 1);
    for (i = 0; i < limit; i++)
        f[i] = f[i] * g[i];
    FFT(f, -1);
    for (i = mid; i < r; i++)
        ans[i] = (ans[i] + (ll) f[i - l].x) % mod;
    solve(mid, r);
}

int main()
{
    int i, j, t, p, q, w, x, y;
    //若有多组fft需要memset
    while (scanf("%d", &n) == 1 && n)
    {
        memset(a, 0, sizeof(a));
        for (i = 1; i <= n; i++)//系数从低到高0-n
            scanf("%lld", &a[i]), a[i] %= mod;//a[0] = 0
        limit = 1;
        while (limit <= n)limit <<= 1;
        ans[0] = 1;
        for (i = 1; i < limit; i++)
            ans[i] = 0;
        solve(0, limit);
        printf("%lld\n", ans[n]);
    }
    return 0;
}
```

