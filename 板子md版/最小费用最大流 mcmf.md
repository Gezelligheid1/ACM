```c++
const int N = 100005;
int n, m, k;

template<const int N>
struct MCMF
{
//#define int long long
    struct edge
    {
        int to, cap, val, inv;
    };
    const int inf = 1e9;
    vector<edge> G[N];
    int dis[N], h[N], pre[N], preu[N];

    void add(int u, int v, int f, int w)//f容量，w费用
    {
        G[u].push_back({v, f, w, (int) G[v].size()});
        G[v].push_back({u, 0, -w, (int) G[u].size() - 1});
    }

    void dijkstra(int s)
    {
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> qu;
        memset(dis, 0x3f, sizeof(dis));
        memset(pre, -1, sizeof(pre));
        memset(preu, -1, sizeof(preu));
        dis[s] = 0;
        qu.push({0, s});
        while (!qu.empty())
        {
            auto[d, u]=qu.top();
            qu.pop();
            if (dis[u] < d)continue;
            int x = 0;
            for (auto[v, f, w, inv]:G[u])
            {
                if (f && dis[v] > dis[u] + w + h[u] - h[v])
                {
                    dis[v] = dis[u] + h[u] - h[v] + w;
                    pre[v] = x;
                    preu[v] = u;
                    qu.push({dis[v], v});
                }
                x++;
            }
        }
    }

    pair<int, int> min_cost_max_flow(int s, int t)
    {
        memset(h, 0, sizeof(h));
        for (int flow = 0, cost = 0, res = inf;; res = inf)
        {
            dijkstra(s);
            if (dis[t] > inf)
                return {flow, cost};
            for (int i = 0; i < N; i++)
                h[i] += dis[i];
            for (int i = t; i != s; i = preu[i])
                res = min(res, G[preu[i]][pre[i]].cap);
            flow += res;
            cost += res * h[t];
            for (int i = t; i != s; i = preu[i])
            {
                G[i][G[preu[i]][pre[i]].inv].cap += res;
                G[preu[i]][pre[i]].cap -= res;
            }
        }
    }

//#undef int
};

MCMF<5005> mcmf;

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    int s, t;
    cin >> n >> m >> s >> t;
    while (m--)
    {
        int u, v, w, c;
        cin >> u >> v >> w >> c;
        mcmf.add(u, v, w, c);
    }
    auto[flow, cost] = mcmf.min_cost_max_flow(s, t);
    cout << flow << " " << cost;
    return 0;
}
```