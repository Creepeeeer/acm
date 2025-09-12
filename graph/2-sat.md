[2-SAT学习笔记 - 洛谷专栏](https://www.luogu.com.cn/article/5tnmwo5l)

**建好的边就类似一个蕴含等值式**

**建2-SAT图，必须将所有肯定关系都找出建边，否则可能会出纰漏**

**1. 我们将一个元素拆成两个点表示 bool 元素的两种情况（点i和点n+i），有向边表示若起点成立，则终点一定成立。**

**2. 当拆点建图后，如果一个元素拆出的两个点 u,v 。存在有向图上的路径 (u−>v\ ，则点 u 可以推出点 v ，点 u非法，则点 v 合法。**

**3. 有向无环图的情况下，合法点的拓扑序比非法点大。**

**4. Tarjan后同一元素拆点强连通分量编号小的点是合法点。**

**5. 如果一个元素拆成的两个点在同一个强连通分量里，即强连通分量编号相同，那么整个序列无解。**

**6. 如果一个元素拆成的两个点之间没有任何路径相连，即使是有向路径，那么这两点都可以成为合法点，这两点的分量编号不同，选小的即可。**



[P4782 【模板】2-SAT - 洛谷](https://www.luogu.com.cn/problem/P4782)

题目需要的是a v b,可以转化为(-a->b)&(-b->a)

```c++
int dfn[N+1],low[N+1],scc[N+1];
bool instk[N+1];
stack<int>stk;
vector<int>g[N+1];
int n,m,cnt,scc_cnt;
void tarjan(int u){
	dfn[u]=low[u]=++cnt;
	instk[u]=true;
	stk.push(u);
	for(auto v:g[u]){
		if(!dfn[v]){
			tarjan(v);
			low[u]=min(low[u],low[v]);
		}
		else if(instk[v]){
			low[u]=min(low[u],dfn[v]);
		}
	}
	if(low[u]==dfn[u]){
		scc_cnt++;
		while(stk.top()!=u){
			int x=stk.top();
			stk.pop();
			scc[x]=scc_cnt;
			instk[x]=false;
		}
		stk.pop();
		scc[u]=scc_cnt;
		instk[u]=false;
	}
}
//main
 fill(instk,instk+2*n+1,false);
    for(int i=1;i<=m;i++){
    	int a,b,c,d;
    	cin>>a>>b>>c>>d;
    	g[b?a:a+n].push_back(d?c+n:c);
    	g[d?c:c+n].push_back(b?a+n:a);
    }
    for(int i=1;i<=2*n;i++){
    	if(!dfn[i])tarjan(i);
    }
    for(int i=1;i<=n;i++){
    	if(scc[i]==scc[i+n]){
    		cout<<"IMPOSSIBLE";
    		return 0;
    	}
    }
    cout<<"POSSIBLE"<<'\n';
    for(int i=1;i<=n;i++){
    	cout<<(scc[i]<scc[i+n]?0:1)<<' ';
    }
```

