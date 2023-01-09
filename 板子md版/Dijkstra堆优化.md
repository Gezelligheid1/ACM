<div align = "center">Dijkstra堆优化</div>

```c++
const int N = 400005;
int n, m;
int head[N], vis[N];
ll d[N];
int cnt;
const ll inf = 100000000000000000;//1e17
struct Edge
{
    int to, w, prev;
} edge[N];

struct node
{
    int id;
    ll w;

    bool operator<(const node &u) const
    {
        return w > u.w;
    }

};

void init()
{
    for (int i = 0; i <= n; i++)
        head[i] = -1;
    cnt = 0;
}

inline void add(int u, int v, int w)
{
    edge[++cnt].to = v;
    edge[cnt].w = w;
    edge[cnt].prev = head[u];//以u为起点上一条边的编号，也就是与这个边起点相同的上一条边的编号
    head[u] = cnt;//当前以u为起点的边的编号
}

void dijkstra(int s)
{
    for (int i = 0; i <= n; i++)
        d[i] = inf;
    d[s] = 0;
    priority_queue<node> qu;
    qu.push({s, 0});
    while (!qu.empty())
    {
        node cur = qu.top();
        qu.pop();
        int now = cur.id;
        if (vis[now])continue;//多组数据则需要memset
        vis[now] = 1;
        for (int i = head[now]; i != -1; i = edge[i].prev)
        {
            int j = edge[i].to;
            if (d[now] + (ll)edge[i].w < d[j])
            {
                d[j] = d[now] + (ll)edge[i].w;
                qu.push({j, d[j]});
            }
        }
    }
}

int main()
{
    int i, j, k, t, g, p, q, u, v, w, x, y, s;
    cin >> n >> m >> s;
    init();
    for (i = 1; i <= m; i++)
    {
        scanf("%d%d%d", &u, &v, &w);
        add(u, v, w);
        add(v, u, w);//无向边开2m的数组
    }
    dijkstra(s);
    for (i = 1; i <= n; i++)
        printf(" %lld" + !(i - 1), d[i]);
    puts("");
    return 0;
}
```

