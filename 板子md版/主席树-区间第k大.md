```c++
const int N = 200005;
int n, m, k;
int a[N], b[N], root[N];
int cnt;
struct point
{
    int l, r, val;
} tree[N * 20];

void build(int &node, int l, int r)
{
    node = ++cnt;
    if (l == r)return;
    int m = (l + r) >> 1;
    build(tree[node].l, l, m);
    build(tree[node].r, m + 1, r);
}

void update(int &node, int last, int l, int r, int x)
{
    node = ++cnt;
    tree[node] = tree[last];
    ++tree[node].val;
    if (l == r)return;
    int m = (l + r) >> 1;
    if (x <= m)update(tree[node].l, tree[last].l, l, m, x);
    else update(tree[node].r, tree[last].r, m + 1, r, x);
}

int query(int node, int last, int l, int r, int k)
{
    if (l == r)return b[l];
    int sum = tree[tree[node].l].val - tree[tree[last].l].val;
    int m = (l + r) >> 1;
    if (k <= sum)return query(tree[node].l, tree[last].l, l, m, k);
    else return query(tree[node].r, tree[last].r, m + 1, r, k - sum);
}

int main()
{
    int i, j, t, p, q, u, v, w, x, y, z, T;
    cin >> n >> m;
    for (i = 1; i <= n; i++)
        scanf("%d", &a[i]), b[i] = a[i];
    sort(b + 1, b + n + 1);
    int size = unique(b + 1, b + n + 1) - b - 1;
    for (i = 1; i <= n; i++)
        a[i] = lower_bound(b + 1, b + size + 1, a[i]) - b;
    build(root[0], 1, size);
    for (i = 1; i <= n; i++)
        update(root[i], root[i - 1], 1, size, a[i]);
    while (m--)
    {
        scanf("%d%d%d", &x, &y, &k);
        printf("%d\n", query(root[y], root[x - 1], 1, size, k));
    }
    return 0;
}
```

