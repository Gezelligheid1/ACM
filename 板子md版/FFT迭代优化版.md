```c++
const double pi = acos(-1);
struct complex {
    double x = 0, y = 0;
    //complex(double xx = 0, double yy = 0) { x = xx, y = yy; }//c++11必须写
    friend complex operator + (const complex& a, const complex& b) { return complex{ a.x + b.x,a.y + b.y }; }
    friend complex operator - (const complex& a, const complex& b) { return complex{ a.x - b.x,a.y - b.y }; }
    friend complex operator * (const complex& a, const complex& b) { return complex{ a.x * b.x - a.y * b.y,a.y * b.x + a.x * b.y }; }
}a[20000005], b[20000005];//因为limit要开4n的空间
int limit, l, r[20000005];
void fft(complex *a, int type)
{
    for (int i = 0; i < limit; i++)
        if (i < r[i])swap(a[i], a[r[i]]);//求出要迭代的序列
    for (int mid = 1; mid < limit; mid <<= 1)//待合并区间limit的一半
    {
        complex wn{ cos(pi / mid),type * sin(pi / mid) };
        for (int R = mid << 1, j = 0; j < limit; j += R)//R是区间的长度，j表示前已经到哪个位置了
        {
            complex w{ 1,0 };
            for (int k = 0; k < mid; k++, w = w * wn)//枚举左半部分
            {
                complex x = a[j + k], y = w * a[j + mid + k];
                a[j + k] = x + y;
                a[j + mid + k] = x - y;
            }
        }
    }
}
int main()
{
    int i, j, k, n, m, t, g, p, q, w, x, y;
    //若有多组fft需要memset
    scanf("%d%d", &n, &m);
    for (i = 0; i <= n; i++)//系数从低到高0-n
        scanf("%lf", &a[i].x);
    for (i = 0; i <= m; i++)//系数从低到高0-m
        scanf("%lf", &b[i].x);
    l = 0;limit = 1; 
    while (limit <= n + m)limit <<= 1, l++;
    for (i = 0; i < limit; i++)//多项式从低到高系数a[0]-a[limit-1]
        r[i] = (r[i >> 1] >> 1) | ((i & 1) << (l - 1));
    fft(a, 1);
    fft(b, 1);
    for (i = 0; i < limit; i++)
        a[i] = a[i] * b[i];
    fft(a, -1);
    for (i = 0; i <= n + m; i++)
        printf("%d ", (int)round(a[i].x / limit));
    puts("");
    return 0;
}
```

