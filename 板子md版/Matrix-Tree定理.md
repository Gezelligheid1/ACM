<div align = "center">Matrix-Tree定理</div>

$$
\sum_T\prod_{e\in T}w_e
$$

```c++
const int N = 305;
int n, m, k;
const int mod = 1e9 + 7;

struct matrix//注意n
{
    ll a[N][N];

    matrix operator-(const matrix &u)
    {
        matrix ans(*this);
        for (int i = 1; i <= n; i++)
            for (int j = 1; j <= n; j++)
                ans.a[i][j] = (ans.a[i][j] - u.a[i][j] + mod) % mod;
        return ans;
    }

    ll *operator[](const int i) { return a[i]; }

    matrix principal_minor(int x)//求去掉第x行第x列的n-1阶主子式
    {
        matrix ans;
        int r = 0;
        for (int i = 1; i <= n; i++)
            if (i != x)
            {
                ++r;
                int c = 0;
                for (int j = 1; j <= n; j++)
                    if (j != x)
                        ans[r][++c] = a[i][j];
            }
        return ans;
    }

    ll det(int n)
    {
        int w = 1;
        for (int i = 1; i <= n; i++)
            for (int j = i + 1; j <= n; j++)
            {
                while (a[i][i])
                {
                    ll div = a[j][i] / a[i][i];
                    for (int k = i; k <= n; k++)
                        a[j][k] = (a[j][k] - div * a[i][k] % mod + mod) % mod;
                    swap(a[i], a[j]);
                    w = -w;
                }
                swap(a[i], a[j]);
                w = -w;
            }
        ll ans = w;
        for (int i = 1; i <= n; i++)
            ans = ans * a[i][i] % mod;
        return (ans + mod) % mod;
    }
} A, D;//A为邻接矩阵，D为度数矩阵


int main()
{
    int p, q, u, v, w, x, y, z, T;
    int t;
    cin >> n >> m >> t;
    while (m--)
    {
        scanf("%d%d%d", &u, &v, &w);//如果有自环记得忽略自环！！！自环在生成树中没有用
        if (t == 0)//无向边
        {
            A[u][v] = (A[u][v] + w) % mod, A[v][u] = (A[v][u] + w) % mod;
            D[u][u] = (D[u][u] + w) % mod, D[v][v] = (D[v][v] + w) % mod;
        }
        else//有向边，此处为根为1的外向树
        {
            A[u][v] = (A[u][v] + w) % mod;
            D[v][v] = (D[v][v] + w) % mod;//外向树考虑入度，内向树考虑出度
        }
    }
    cout << (D - A).principal_minor(1).det(n - 1);//有向图去掉根所在行列，无向图去掉任意第k行列，此处以1为根
    return 0;
}
```