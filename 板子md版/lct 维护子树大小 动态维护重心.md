<div align = "center">lct 维护子树大小 动态维护重心</div>

考虑动态维护每棵树的重心，当连边$x→y$时
设连边之前，$x,y$所在树的重心为分别为$G_x,G_y$，那么连边后新树的重心$z$一定在$G_x→G_y$的路径上。
记路径上$A_u$和$B_u$是路径上某一点$u$的左右两个相邻节点，且满足$G_x,G_y$分别在子树$A_u,B_u$中，显然$u$的最大子树只会是$A_u,B_u$其中之一。

```c++
const int N = 100005;
int n, m, k;

struct lct
{
    int sz[N], vir_sz[N], f[N], son[N][2], rev[N], stk[N];
#define ls son[x][0]
#define rs son[x][1]

    int is_root(int x)
    {
        return son[f[x]][0] != x && son[f[x]][1] != x;
    }

    void pushup(int x)
    {
        sz[x] = sz[ls] + sz[rs] + 1 + vir_sz[x];
    }

    void pushrev(int x)
    {
        swap(ls, rs);
        rev[x] ^= 1;
    }

    void pushdown(int x)
    {
        if (rev[x])
        {
            if (ls)pushrev(ls);
            if (rs)pushrev(rs);
            rev[x] = 0;
        }
    }

    int get(int x) { return son[f[x]][1] == x; }

    void rotate(int x)
    {
        int y = f[x], z = f[y], k = get(x);
        if (!is_root(y))son[z][son[z][1] == y] = x;
        son[y][k] = son[x][!k], f[son[x][!k]] = y;
        son[x][!k] = y, f[y] = x, f[x] = z;
        pushup(y), pushup(x);
    }

    void splay(int x)
    {
        int now = x, top = 0;
        while (!is_root(now))
            stk[++top] = now, now = f[now];
        stk[++top] = now;
        while (top)pushdown(stk[top--]);
        for (int fa; fa = f[x], !is_root(x); rotate(x))
            if (!is_root(fa))rotate(get(fa) == get(x) ? fa : x);
    }

    void access(int x)
    {
        for (int y = 0; x; x = f[y = x])
        {
            splay(x);
            vir_sz[x] += sz[rs];
            rs = y;
            vir_sz[x] -= sz[rs];
            pushup(x);
        }
    }

    void makeroot(int x)
    {
        access(x), splay(x);
        pushrev(x);
    }

    int findroot(int x)
    {
        access(x), splay(x);
        while (ls)pushdown(x), x = ls;
        splay(x);
        return x;
    }

    void split(int x, int y)
    {
        makeroot(x);
        access(y), splay(y);
    }

    void link(int x, int y)
    {
        makeroot(x);
        if (findroot(y) != x)//若保证不存在边可以把splay(y)及之前的代码改成split(x,y)
        {
            splay(y);//必须加这句,虽然已经有findroot(y)里有access(y),但是把找到的根旋到了splay的根
            f[x] = y;
            vir_sz[y] += sz[x];
            pushup(y);
        }
    }

    void cut(int x, int y)
    {
        makeroot(x);
        if (findroot(y) == x && f[y] == x && !son[y][0])
        {
            f[y] = son[x][1] = 0;
            pushup(x);
        }
    }

    int update(int x)
    {
        int Lsum = 0, Rsum = 0, ans = 1e9, half = sz[x] >> 1;
        while (x)
        {
            pushdown(x);//别忘记pushdown
            int Lnow = Lsum + sz[ls];
            int Rnow = Rsum + sz[rs];
            if (Lnow <= half && Rnow <= half)
                ans = min(ans, x);
            if (Lnow >= Rnow)Rsum += vir_sz[x] + 1 + sz[rs], x = ls;
            else Lsum += vir_sz[x] + 1 + sz[ls], x = rs;
        }
        assert(ans != (int) 1e9);
        splay(ans);
        return ans;
    }

#undef ls
#undef rs
} t;

int bin[N];

int find(int x)
{
    return bin[x] == x ? x : bin[x] = find(bin[x]);
}

int main()
{
    int p, q, u, v, w, x, y, z, T;
    cin >> n >> m;
    int XOR = 0;
    for (int i = 1; i <= n; i++)
        XOR ^= i;
    iota(bin + 1, bin + n + 1, 1);
    char s[5];
    while (m--)
    {
        scanf("%s", s);
        if (s[0] == 'A')
        {
            scanf("%d%d", &x, &y);
            t.link(x, y);
            x = find(x), y = find(y);
            t.split(x, y);
            z = t.update(y);
            bin[x] = bin[y] = bin[z] = z;
            XOR ^= x ^ y ^ z;
        }
        else if (s[0] == 'Q')
        {
            scanf("%d", &x);
            printf("%d\n", find(x));
        }
        else
            printf("%d\n", XOR);
    }
    return 0;
}
```