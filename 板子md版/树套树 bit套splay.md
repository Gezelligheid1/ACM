<div align = "center">树套树 bit套splay</div>

# [BOI2007]Mokia 摩基亚

## 题目描述

摩尔瓦多的移动电话公司摩基亚（Mokia）设计出了一种新的用户定位系统。和其他的定位系统一样，它能够迅速回答任何形如“用户 C 的位置在哪？”的问题，精确到毫米。但其真正高科技之处在于，它能够回答形如“给定区域内有多少名用户？”的问题。

在定位系统中，世界被认为是一个 $w×w$ 的正方形区域，由 $1\times 1$ 的方格组成。每个方格都有一个坐标 $(x,y)$，$1\leq x,y\leq w$。坐标的编号从 $1$ 开始。对于一个 $4\times 4$ 的正方形，就有 $1\leq x\leq 4$，$1\leq y\leq 4$（如图）：

![](https://cdn.luogu.com.cn/upload/pic/17271.png)

请帮助 Mokia 公司编写一个程序来计算在某个矩形区域内有多少名用户。

## 输入格式

有三种命令，意义如下：

| 命令 |       参数       |                             意义                             |
| :--: | :--------------: | :----------------------------------------------------------: |
| $0$  |       $w$        |         初始化一个全零矩阵。本命令仅开始时出现一次。         |
| $1$  |    $x\,y\,a$     |       向方格 $(x,y)$ 中添加 $a$ 个用户。$a$ 是正整数。       |
| $2$  | $x1\,y1\,x2\,y2$ | 查询 $x1\leq x\leq x2$，$y1\leq y\leq y2$ 所规定的矩形中的用户数量。 |
| $3$  |      无参数      |              结束程序。本命令仅结束时出现一次。              |

输入共若干行，每行有若干个整数，表示一个命令。

## 输出格式

对所有命令 $2$，输出一个一行整数，即当前询问矩形内的用户数量。

## 样例 #1

### 样例输入 #1

```
0 4
1 2 3 3
2 1 1 3 3
1 2 2 2
2 2 2 3 4
3
```

### 样例输出 #1

```
3
5
```

## 提示

#### 数据规模与约定


对于 $100\%$ 的数据，保证：
- $1\leq w\leq 2000000$。
- $1\leq x1\leq x2\leq w$，$1\leq y1\leq y2\leq w$，$1\leq x,y\leq w$，$0<a\leq 10000$。
- 命令 $1$ 不超过 $160000$ 个。
- 命令 $2$ 不超过 $10000$ 个。

```c++
const int N = 2000005;
int n, m, k;
namespace Splay {
    int c[N], sum[N], son[N][2], val[N], f[N], rt[N];
    int cnt;
#define ls son[x][0]
#define rs son[x][1]

    inline void pushup(int x)
    {
        sum[x] = sum[ls] + sum[rs] + c[x];
    }

    inline int get(int x) { return x == son[f[x]][1]; }

    inline void connect(int x, int y, int d)
    {
        if (x)son[x][d] = y;
        if (y)f[y] = x;
    }

    void rotate(int x)
    {
        int fa = f[x], ffa = f[fa], p = get(x), q = get(fa);
        connect(fa, son[x][p ^ 1], p);
        connect(x, fa, p ^ 1);
        connect(ffa, x, q);
        pushup(fa), pushup(x);
    }

    void splay(int &rt, int x)
    {
        for (int fa; fa = f[x], fa; rotate(x))
            if (f[fa])rotate(get(x) == get(fa) ? fa : x);
        rt = x;
    }

    void insert(int &rt, int pos, int a)
    {
        if (!rt)
        {
            rt = ++cnt;
            c[rt] = sum[rt] = a;
            val[rt] = pos;
            return;
        }
        int x = rt, fa = 0;
        while (1)
        {
            if (val[x] == pos)
            {
                c[x] += a;
                pushup(x), pushup(fa);
                splay(rt, x);
                return;
            }
            fa = x, x = son[x][pos > val[x]];
            if (!x)
            {
                ++cnt;
                c[cnt] = sum[cnt] = a;
                val[cnt] = pos;
                f[cnt] = fa;
                son[fa][pos > val[fa]] = cnt;
                pushup(fa);
                splay(rt, cnt);
                return;
            }
        }
    }

    int pre_sum(int &rt, int pos)
    {
        int x = rt, ans = 0;
        while (x)
        {
            if (pos < val[x])
            {
                x = ls;
                continue;
            }
            ans += sum[ls];
            if (pos == val[x])
            {
                splay(rt, x);
                return ans + c[x];
            }
            ans += c[x];
            x = rs;
        }
        return ans;
    }

#undef ls
#undef rs
}
using namespace Splay;

inline int lowbit(int x) { return x & -x; }

void bit_update(int x, int y, int a)
{
    for (int i = x; i <= n; i += lowbit(i))
        insert(rt[i], y, a);
}

int bit_query(int x1, int x2, int y1, int y2)
{
    int ans = 0;
    for (int i = x2; i; i -= lowbit(i))
        ans += pre_sum(rt[i], y2) - pre_sum(rt[i], y1 - 1);
    for (int i = x1 - 1; i; i -= lowbit(i))
        ans -= pre_sum(rt[i], y2) - pre_sum(rt[i], y1 - 1);
    return ans;
}

int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    int p, q, u, v, w, x, y, z, T;
    cin >> x >> n;
    int op;
    while (cin >> op && op != 3)
    {
        int a;
        if (op == 1)cin >> x >> y >> a, bit_update(x, y, a);
        else
        {
            int x1, y1, x2, y2;
            cin >> x1 >> y1 >> x2 >> y2;
            cout << bit_query(x1, x2, y1, y2) << '\n';
        }
    }
    return 0;
}
```