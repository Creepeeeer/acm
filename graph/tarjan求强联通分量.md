# 1.求强联通分量

此时dfs选取方式为先递归邻居节点再访问自己

![a1406d2017667c62399df1f154aa2fc](C:\Users\LENOVO\Documents\WeChat Files\wxid_cdt96oj7ckfj32\FileStorage\Temp\a1406d2017667c62399df1f154aa2fc.png)

![c2577d67eb53796c6f37bfa81082857](C:\Users\LENOVO\Documents\WeChat Files\wxid_cdt96oj7ckfj32\FileStorage\Temp\c2577d67eb53796c6f37bfa81082857.png)

![1e65f127a8945e7e0a130eaa0e460f1](C:\Users\LENOVO\Documents\WeChat Files\wxid_cdt96oj7ckfj32\FileStorage\Temp\1e65f127a8945e7e0a130eaa0e460f1.png)

```c++
list<int>graph[N + 1];
int dfn[N + 1] = { 0 };
int low[N + 1] = { 0 };
bool instack[N + 1];
vector<vector<int>>an;//储存强联通分量
stack<int>st;
int cnt = 1;
void dfs(int u) {
	dfn[u] = low[u] = cnt++;
	instack[u] = true;
	for (auto v : graph[u]) {
		if (dfn[v] == 0) {
			dfs(v);
			low[u] = min(low[u], low[v]);
		}
		else if (instack[v]) {
			low[u] = min(low[u], dfn[v]);
		}
	}
	if (dfn[u] == low[u]) {
		vector<int>temp;
		int x;
		do {
			x = st.top();
			st.pop();
			temp.push_back(x);
			instack[x] = false;
		} while (x != u);
		an.push_back(temp);
	}
}
	int n, m, u, v;
	cin >> n >> m;
	for (int i = 1; i <= m; i++) {
		cin >> u >> v;
		if (u != v)graph[u].push_back(v);
	}
	fill(instack, instack + n + 1, false);
	for (int i = 1; i <= n; i++) {
		if (dsu[i] == 0)dfs(i);
	}
```

