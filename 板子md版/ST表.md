<div align = "center">ST表</div>

```c++
const int N = 100005;
int a[N], lg[N], f[N][21];//// 第二维的大小根据数据范围决定，不小于log(N)
void init(int n)
{
    for (int i = 2; i <= n; i++)
        lg[i] = lg[i / 2] + 1;
}

void ST(int n)
{
    int i, j;
    for (i = 1; i <= n; i++)
        f[i][0] = a[i];
    for (j = 1; 1 << j <= n; j++)//下标从1开始，如果从0开始记得改
    {
        for (i = 1; i + (1 << j) - 1 <= n; i++)
            f[i][j] = max(f[i][j - 1], f[i + (1 << (j - 1))][j - 1]);
    }
}

inline int rmq(int l, int r)//注意ll
{
    int k = lg[r - l + 1];
    return max(f[l][k], f[r - (1 << k) + 1][k]);
}

inline int read()
{
    int x = 0, f = 1;
    char ch = getchar();
    while (ch < '0' || ch > '9')
    {
        if (ch == '-')
            f = -1;
        ch = getchar();
    }
    while (ch >= '0' && ch <= '9')
    {
        x = (x << 1) + (x << 3) + (ch ^ 48);
        ch = getchar();
    }
    return x * f;
}

int main()
{
    int i, j, n, m, k, t, g, p, q, u, v, w, x, y;
    n = read();
    m = read();
    for (i = 1; i <= n; i++)
        a[i] = read();
    init(n);
    ST(n);
    while (m--)
    {
        int l=read();
        int r=read();
        printf("%d\n",rmq(l,r));
    }
    return 0;
}
```

