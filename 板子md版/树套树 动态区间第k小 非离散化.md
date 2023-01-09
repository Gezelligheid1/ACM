<div align = "center">树套树 动态区间第k小 非离散化</div>

未离散化要开空间极大，不过树套树很难占满空间

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
//tree<int, null_type, less<int>, rb_tree_tag, tree_order_statistics_node_update> tr;
//__gnu_pbds::priority_queue<int, greater<int>, pairing_heap_tag> qu;
//typedef trie<string, null_type, trie_string_access_traits<>, pat_trie_tag, trie_prefix_search_node_update> pref_trie;
const int N = 100005;
int n, m, k;
int a[N];
int c[N * 900], lc[N * 900], rc[N * 900], rt[N];
int cnt;
int add[35], sub[35], t1, t2;

void seg_update(int l, int r, int &k, int x, int v)
{
    if (!k)k = ++cnt;
    c[k] += v;
    if (l == r)return;
    int m = (l + r) >> 1;
    if (x <= m)seg_update(l, m, lc[k], x, v);
    else seg_update(m + 1, r, rc[k], x, v);
}

inline int lowbit(int x) { return x & -x; }

void bit_update(int x, int v)//x为下标
{
    for (int i = x; i <= n; i += lowbit(i))
        seg_update(0, 1e9, rt[i], a[x], v);
}

int seg_query(int l, int r, int k)//第k小
{
    if (l == r)return l;
    int sum = 0;
    for (int i = 1; i <= t1; i++)
        sum += c[lc[add[i]]];
    for (int i = 1; i <= t2; i++)
        sum -= c[lc[sub[i]]];
    int m = (l + r) >> 1;
    if (k <= sum)
    {
        for (int i = 1; i <= t1; i++)
            add[i] = lc[add[i]];
        for (int i = 1; i <= t2; i++)
            sub[i] = lc[sub[i]];
        return seg_query(l, m, k);
    }
    else
    {
        for (int i = 1; i <= t1; i++)
            add[i] = rc[add[i]];
        for (int i = 1; i <= t2; i++)
            sub[i] = rc[sub[i]];
        return seg_query(m + 1, r, k - sum);
    }
}

int bit_query(int l, int r, int k)
{
    t1 = t2 = 0;
    memset(add, 0, sizeof(add));
    memset(sub, 0, sizeof(sub));
    for (int i = r; i; i -= lowbit(i))
        add[++t1] = rt[i];
    for (int i = l - 1; i; i -= lowbit(i))//l-1
        sub[++t2] = rt[i];
    return seg_query(0, 1e9, k);
}

int main()
{
    int p, q, u, v, w, x, y, z, T;
    cin >> n >> m;
    for (int i = 1; i <= n; i++)
        scanf("%d", &a[i]), bit_update(i, 1);
    char s[5];
    while (m--)
    {
        scanf("%s%d%d", s, &x, &y);
        if (s[0] == 'Q')
        {
            int k;
            scanf("%d", &k);
            printf("%d\n", bit_query(x, y, k));
        }
        else
        {
            bit_update(x, -1);
            a[x] = y;
            bit_update(x, 1);
        }
    }
    return 0;
}
```

