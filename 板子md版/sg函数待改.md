```c++
#include<bits/stdc++.h>

using namespace std;
int k, a[100], f[10001];
int
int sg(int p)
{
    int i, t;
    bool g[101] = {0};
    for (i = 0; i < k; i++)
    {
        t = p - a[i];
        if (t < 0)break;
        if (f[t] == -1)f[t] = sg(t);
        g[f[t]] = 1;
    }
    for (i = 0;; i++)
    {
        if (!g[i])return i;
    }
}

int main()
{
    int n, i, m, t, s;
    while (scanf("%d", &k), k)
    {
        for (i = 0; i < k; i++)scanf("%d", &a[i]);
        sort(a, a + k);
        memset(f, -1, sizeof(f));
        f[0] = 0;
        scanf("%d", &n);
        while (n--)
        {
            scanf("%d", &m);
            s = 0;
            while (m--)
            {
                scanf("%d", &t);
                if (f[t] == -1) f[t] = sg(t);
                s = s ^ f[t];
            }
            if (s == 0) printf("L"); else printf("W");
        }
        printf("\n");
    }
}
```

