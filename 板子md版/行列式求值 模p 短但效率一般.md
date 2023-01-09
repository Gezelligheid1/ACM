<div align = "center">行列式求值 模p 短但效率一般</div>

```c++
ll det(int a[][N])
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
```