# st表

维护区间可重复贡献问题（如gcd，min,max，&，|等）

code

```c++
  int n;
  cin >> n;
  int log = log2(n);
  vector<vector<int>>st(n + 1, vector<int>(log + 1));
  for (int i = 1; i <= n; i++)cin >> st[i][0];
  vector<int>logn(n + 1);
  logn[1] = 0; logn[2] = 1;
  for (int i = 3; i <= n; i++)logn[i] = logn[i / 2] + 1;
  for (int j = 1; j <= log; j++) {
      for (int i = 1; i + (1 << j) - 1 <= n; i++) {
          st[i][j] = min(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
      }
  }
int query(int l, int r, const vector<vector<int>>& st, const vector<int>&logn) {
    int m = logn[r - l + 1];
    return min(st[l][m], st[r - (1 << m) + 1][m]);
}
```

