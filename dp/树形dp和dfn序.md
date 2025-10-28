# 树形dp



<img src="C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250527222520728.png" alt="image-20250527222520728" style="zoom: 50%;" />

好题：https://leetcode.cn/problems/binary-tree-cameras/?envType=problem-list-v2&envId=f4LgKPld





# dfn序

<img src="C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250527222711499.png" alt="image-20250527222711499" style="zoom: 67%;" />



code：

```c++
list<int>graph[N+1];
int cnt=0;
int dfn[N+1];//dfn序号 dfn[i]原序号为i的dfn序
int siz[N+1];//树的大小 siz[i]原序号为i的树的大小
//siz[i]也可以以dfn序为下标
int dfs(int u){
    dfn[u]=++cnt;
    int time=1;
    for(auto v:graph[u]){
        if(dfn[v]==0){
            int x=dfs(v);
            time+=x;
        }
    }
    pre[u]=num;
    siz[u]=time;
    return {time,num};
}
```



# 树上背包

[P2014 [CTSC1997\] 选课 - 洛谷](https://www.luogu.com.cn/problem/P2014)

根据题目意思，每个点只有一个前继节点，则可以想到是树结构

code

```c++

int dfs(int u,int pre,int m){
	if(m==0)return 0;
	if(pre==0||m==1)return val[u];
	if(memo[u][pre][m]!=-1)return memo[u][pre][m];
	int an=0;
	int v=graph[u][pre-1];
	for(int i=0;i<m;i++){
		an=max(an,dfs(u,pre-1,m-i)+dfs(v,out[v],i));
	}
	memo[u][pre][m]=an;
	return an;
}
	memo.resize(n+1);
	for(int i=0;i<=n;i++)memo[i].resize(out[i]+1);
	for(int i=0;i<=n;i++){
		for(int j=0;j<=out[i];j++){
			memo[i][j].resize(m+2);
		}
	}
	for(int i=0;i<=n;i++){
		for(int j=0;j<=out[i];j++){
			for(int k=0;k<=m+1;k++){
				memo[i][j][k]=-1;
			}
		}
	}
	cout<<dfs(0,out[0],m+1);
```

