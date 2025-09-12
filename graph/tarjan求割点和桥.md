# 割点

### 1.是根节点并且有>=2个儿子

### 2.不是根节点且low[v]>=dfn[u]

```c++
bool is_p[N + 1];
int dfn[N + 1], low[N + 1];
void tarjan(int u, int f) {
    int child = 0;
    dfn[u] = low[u] = cnt++;
    for (auto v : g[u]) {
        if (!dfn[v]) {
            tarjan(v, u);
            child++;
            low[u] = min(low[u], low[v]);
            if (f != 0 && low[v] >= dfn[u]) {
                is_p[u] = true;//是割点
            }
        }
        else if (v != f) low[u] = min(low[u], dfn[v]);
    }
    if (f == 0 && child >= 2)is_p[u] = true;
}

```



# 桥

#### low[v]>dfn[u]

**注意有重边，存祖先的边的id不走重边**

```c++
bool is_bridge[M+1];
int dfn[N+1],low[N+1];
int n,m,cnt=1;
void tarjan(int u,int f){
    dfn[u]=low[u]=cnt++;
    for(auto [v,id]:g[u]){
        if(!dfn[v]){
            tarjan(v,id);
            low[u]=min(low[u],low[v]);
            if(low[v]>dfn[u])is_bridge[id]=true;
        }
        else if(id!=f){
            low[u]=min(low[u],dfn[v]);
        }
    }
}
 for(int i=1;i<=m;i++){
        int u,v;
        cin>>u>>v;
        g[u].push_back({v,i});
        g[v].push_back({u,i});
    }
    for(int i=1;i<=n;i++){
        if(!dfn[i])tarjan(i,0);
    }
    int an=0;
    for(int i=1;i<=m;i++)an+=is_bridge[i];
    cout<<an;
```

