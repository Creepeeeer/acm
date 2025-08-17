**树上某些问题需要知道以不同节点作为根的时候的答案**

#### 换根思考点:如果得到父节点作为根的答案，如何加工出来子节点作为根的答案

#### 流程：

**先以1节点作为根dfs1一遍收集一些信息，然后再从1节点开始进行树的遍历，求解每个节点作为根的时候的答案，进行dfs2**

e.g

[P3478 [POI 2008\] STA-Station - 洛谷](https://www.luogu.com.cn/problem/P3478)

子节点的sum等于父节点的sum+n-2*siz[v];

整合出最大值就行



[3585 -- Accumulation Degree](http://poj.org/problem?id=3585)

要预处理出两个cost数组来转移，特别要注意分类讨论叶节点的时候



[P3047 [USACO12FEB\] Nearby Cows G - 洛谷](https://www.luogu.com.cn/problem/P3047)

注意到这个题k的范围比较小，那么就可以设计dp[i] [j]表示离i j距离的点的和

code

```c++
void dfs(int u){
    dfn[u]=cnt++;
    dp[u][0]=a[u];
    for(auto v:g[u]){
        if(dfn[v]==0){
            dfs(v);
            for(int i=1;i<=k;i++){
                dp[u][i]+=dp[v][i-1];
            }
        }
    }
}
void dfs2(int u){
    for(auto v:g[u]){
        if(dfn[v]<dfn[u])continue;
        vector<int>te(k+1);
        for(int i=0;i<=k;i++)te[i]=dp[u][i];
        for(int i=1;i<=k;i++)te[i]-=dp[v][i-1];
        for(int i=1;i<=k;i++)dp[v][i]+=te[i-1];
        dfs2(v);
    }
}
```



[Problem - 708C - Codeforces](https://codeforces.com/problemset/problem/708/C)

要使一个点成为重心，要把它为根的最大子树的子树中删去一个<=n/2的子树使使其大小<=n/2,但是可能此时的x为根的最大子树的子树通过了x所以要维护最大值和次大值从而判断



[P6419 [COCI 2014/2015 #1\] Kamp - 洛谷](https://www.luogu.com.cn/problem/P6419)

首先先计算最后送完乘客要返回，如果一条边连接的子节点的子树上有点要送要么就要加两倍这条边的权值，然后减去最长链就是最终的答案

考虑换根：要维护最大和次大两条链，如果u,v这条边在最大链上就要pk最大-w和次大+w谁是最大

并且还有两个边界，1：如果父节点之前的点有要送的子节点没有要送的那么要+2*w，2：如果父节点的唯一要送的点是子节点那么要

-2 *w
