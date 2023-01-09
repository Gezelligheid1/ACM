<div align = "center">可持久化01trie</div>

```html
现在有一颗以 1 为根节点的由 n 个节点组成的树，节点从 1 至 n 编号。树上每个节点上都有一个权值vi。现在有 q 次操作，操作如下：
1 x z：查询节点 x 的子树中的节点权值与 z 异或结果的最大值。(dfs序可持久化)
2 x y z：查询节点 x 到节点 y 的简单路径上的节点的权值与 z 异或结果最大值(根到节点可持久化)。
```

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
const int N = 100005;//实际应改为2e5，但clion开不下
int n, m, k;
int a[N];
vector<int> vec[N];
int rt1[N], rt2[N];
int f[N], d[N], sz[N], son[N], top[N], nid[N], oid[N];
int cnt, dfn;
struct point
{
    int son[2];
    int sum;
} c[N * 32];

void insert(int &node, int last, int x)
{
    node = ++cnt;
    c[node] = c[last], c[node].sum++;
    int p = node;
    for (int i = 30; i >= 0; i--)
    {
        int t = x >> i & 1;
        c[p].son[t] = ++cnt;
        p = c[p].son[t];
        last = c[last].son[t];
        c[p] = c[last];
        c[p].sum++;
    }
}

void dfs1(int u, int fa)
{
    f[u] = fa, d[u] = d[fa] + 1;
    sz[u] = 1;
    insert(rt2[u], rt2[fa], a[u]);
    for (auto v:vec[u])
    {
        if (v == fa)continue;
        dfs1(v, u);
        sz[u] += sz[v];
        if (sz[son[u]] < sz[v])son[u] = v;
    }
}

void dfs2(int u, int topu)
{
    top[u] = topu;
    nid[u] = ++dfn;
    oid[dfn] = u;
    insert(rt1[u], rt1[oid[dfn - 1]], a[u]);
    if (son[u])dfs2(son[u], topu);
    for (auto v:vec[u])
    {
        if (v == f[u] || v == son[u])
            continue;
        dfs2(v, v);
    }
}

int lca(int x, int y)
{
    while (top[x] != top[y])
    {
        if (d[top[x]] < d[top[y]])
            swap(x, y);
        x = f[top[x]];
    }
    return d[x] < d[y] ? x : y;
}


int query1(int node, int last, int x)
{
    int ans = 0;
    for (int i = 30; i >= 0; i--)
    {
        int t = x >> i & 1;
        if (c[c[node].son[!t]].sum - c[c[last].son[!t]].sum > 0)
            ans |= 1 << i, node = c[node].son[!t], last = c[last].son[!t];
        else node = c[node].son[t], last = c[last].son[t];
    }
    return ans;
}

int query2(int a, int b, int pre1, int pre2, int x)
{
    int ans = 0;
    for (int i = 30; i >= 0; i--)
    {
        int t = x >> i & 1;
        if (c[c[a].son[!t]].sum + c[c[b].son[!t]].sum - c[c[pre1].son[!t]].sum - c[c[pre2].son[!t]].sum > 0)
            ans |= 1 << i, a = c[a].son[!t], b = c[b].son[!t], pre1 = c[pre1].son[!t], pre2 = c[pre2].son[!t];
        else a = c[a].son[t], b = c[b].son[t], pre1 = c[pre1].son[t], pre2 = c[pre2].son[t];
    }
    return ans;
}

int main()
{
    int p, q, u, v, w, x, y, z, T;
    cin >> n >> q;
    for (int i = 1; i <= n; i++)
        scanf("%d", &a[i]);
    for (int i = 1; i < n; i++)
        scanf("%d%d", &u, &v), vec[u].emplace_back(v), vec[v].emplace_back(u);
    dfs1(1, 0);
    dfs2(1, 1);
    while (q--)
    {
        int op;
        scanf("%d", &op);
        if (op == 1)
        {
            scanf("%d%d", &x, &w);
            printf("%d\n", query1(rt1[oid[nid[x] + sz[x] - 1]], rt1[oid[nid[x] - 1]], w));
        }
        else
        {
            scanf("%d%d%d", &x, &y, &w);
            int LCA = lca(x, y);
            printf("%d\n", query2(rt2[x], rt2[y], rt2[LCA], rt2[f[LCA]], w));
        }
    }
    return 0;
}
```