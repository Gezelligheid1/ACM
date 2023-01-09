```c++
    for (i = 1; i <= n; i++)
        for (j = 1; j <= n; j++)
        {
            i == j ? d[i][j] = 0 : d[i][j] = inf;
            path[i][j] = j;
        }
    for (k = 1; k <= n; k++)
        for (i = 1; i <= n; i++)
            for (j = 1; j <= n; j++)
            {
                if (d[i][k] + d[k][j] < d[i][j])
                {
                    d[i][j] = d[i][k] + d[k][j];
                    path[i][j] = path[i][k];//i之后连着的节点
                }
            }
```
