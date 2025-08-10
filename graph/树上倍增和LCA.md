**倍增算法建表O(nlogn)每次查询logn，tarjanO(n) **

**但是tarjan是离线的，你把所有问题给完之后遍历一遍树得到所有答案**



## 使用倍增算法求LCA

```c++
const int N = 5e5;
vector<int>g[N + 1];
int dep[N + 1];
int fa[N + 1][32];
int maxdep = 0;
void dfs(int u, int f) {
	fa[u][0] = f;
	if (f != -1)dep[u] = dep[f] + 1;
	maxdep = max(maxdep, dep[u]);
	for (int i = 1; i < 32; i++) {
		if (fa[u][i - 1] == -1)fa[u][i] = -1;
		else fa[u][i] = fa[fa[u][i - 1]][i - 1];
	}
	for (auto v : g[u]) {
		if (v != f)dfs(v, u);
	}
}
int same_dep(int u, int diff) {
	for (int i = 0; i <= (int)log2(diff); i++) {
		if ((diff >> i) & 1)u = fa[u][i];
	}
	return u;
}
int lca(int a, int b) {
	if (dep[a] < dep[b])swap(a, b);
	a = same_dep(a, dep[a] - dep[b]);
	if (a == b) return a;
	for (int i = maxdep; i >= 0; i--) {
		if (fa[a][i] != fa[b][i]) {
			a = fa[a][i], b = fa[b][i];
		}
	}
	return fa[a][0];
}

//main
dep[s] = 1;
	fa[s][0] = -1;
	dfs(s, -1);
	maxdep = (int)log2(maxdep);
	while (q--) {
		int a, b;
		cin >> a >> b;
		cout << lca(a, b) << '\n';
	}
```



## 使用tarjan算法求LCA

![image-20250807183439081](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250807183439081.png)

```
int an[N+1],fa[N+1];
bool vis[N+1];
vector<int>g[N + 1];
vector<pii>qs[N+1];
int find(int x){
	if(x!=fa[x]){
		fa[x]=find(fa[x]);
	}
	return fa[x];
}
void un(int x,int y){
	x=find(x),y=find(y);
	fa[y]=x;
}
void tarjan(int u){
	vis[u]=true;
	for(auto v:g[u]){
		if(!vis[v]){
			tarjan(v);
			un(u,v);
		}
	}
	for(auto[x,y]:qs[u]){
		if(vis[x]){
			an[y]=find(x);
		}
	}
}

//main
for(int i=0;i<q;i++){
		int x,y;
		cin>>x>>y;
		qs[x].push_back({y,i});
		qs[y].push_back({x,i});
	}
	fill(vis,vis+n+1,false);
	iota(fa,fa+n+1,0);
	tarjan(s);
	for(int i=0;i<q;i++)cout<<an[i]<<'\n';
```



#### **一个结论**

**如果边权为正，假设lca(a,b)=c,那么a到b路径长度=头结点到a的长度+头结点到b的长度-头结点到lca(a,b)的长度*2**

