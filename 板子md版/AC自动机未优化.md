<div align = "center">AC自动机未优化</div>

```html
有 N 个由小写字母组成的模式串以及一个文本串 T。每个模式串可能会在文本串中出现多次。你需要找出哪些模式串在文本串 T 中出现的次数最多。

保证不存在两个相同的模式串!!!

输入格式
输入含多组数据。保证输入数据不超过 50 组。
每组数据的第一行为一个正整数 N，表示共有 N 个模式串，1≤N≤150。
接下去 N 行，每行一个长度小于等于 70 的模式串。下一行是一个长度小于等于1e6的文本串T。保证不存在两个相同的模式串。
输入结束标志为 N=0。
输出格式
对于每组数据，第一行输出模式串最多出现的次数，接下去若干行每行输出一个出现次数最多的模式串，按输入顺序排列。
https://www.luogu.com.cn/problem/P3796
```

```c++
const int N = 11005;
int n, m, k;
int c[N][26], ed[N], fail[N], ans[N];
int cnt;

void insert(string &s, int pos)
{
    int p = 0;
    for (auto x:s)
    {
        if (!c[p][x - 'a'])c[p][x - 'a'] = ++cnt;
        p = c[p][x - 'a'];
    }
    ed[p] = pos;
}

void get_fail()
{
    queue<int> qu;
    for (int i = 0; i < 26; i++)
        if (c[0][i])fail[c[0][i]] = 0, qu.push(c[0][i]);
    while (!qu.empty())
    {
        int p = qu.front();
        qu.pop();
        for (int i = 0; i < 26; i++)
        {
            if (c[p][i])fail[c[p][i]] = c[fail[p]][i], qu.push(c[p][i]);
            else c[p][i] = c[fail[p]][i];
        }
    }
}

void query(string &s)
{
    int p = 0;
    for (auto x:s)
    {
        p = c[p][x - 'a'];
        for (int j = p; j; j = fail[j])
            ans[ed[j]]++;
    }
}

string s[155];

int main()
{
    ios::sync_with_stdio(false);
    int p, q, u, v, w, x, y, z, T;
    while (cin >> n && n)
    {
        cnt = 0;//~~
        memset(c, 0, sizeof(c));
        memset(fail, 0, sizeof(fail));
        memset(ed, 0, sizeof(ed));
        memset(ans, 0, sizeof(ans));
        for (int i = 1; i <= n; i++)
            cin >> s[i], insert(s[i], i);
        get_fail();
        string t;
        cin >> t;
        query(t);
        int mx = *max_element(ans + 1, ans + n + 1);
        cout << mx << '\n';
        for (int i = 1; i <= n; i++)
            if (ans[i] == mx)cout << s[i] << '\n';
    }
    return 0;
}
```