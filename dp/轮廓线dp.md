**轮廓线dp是去优化状压dp**

面对n行m列二维网格需要逐行逐列做决策并且状态信息表示上一行状态的时候，此时为了得到当前行的状态需要暴力dfs

时间复杂度是$O(2^m*n*2^m)$

**轮廓线dp的原理**

使用轮廓线dp $f(i,j,s)$表示当前来到i行j列，轮廓线的状况为s 

此时$i-1$行$j->m-1$列用$s[j....m-1]$表示

$i$行$0->j-1$列用$s[0...j-1]$表示

像一条轮廓线一样包裹

此时的时间复杂度是$O(2^m*n*m)$

[P1879 [USACO06NOV\] Corn Fields G - 洛谷](https://www.luogu.com.cn/problem/P1879)

```c++
int sta[N];
const int mod=1e8;
int dp[N][M][1<<M];
int n,m;
int dfs(int i,int j,int s){
    if(i==n)return 1;
    if(dp[i][j][s]!=-1)return dp[i][j][s];
    ll an=dfs(j==m-1?i+1:i,j==m-1?0:j+1,s&(~(1<<j)));
    if(j==0&&sta[i]>>j&1&&!(s>>j&1))an=(an+dfs(j==m-1?i+1:i,j==m-1?0:j+1,s|(1<<j)))%mod;
    if(j>0&&sta[i]>>j&1&&!(s>>j&1)&&!(s>>(j-1)&1))an=(an+dfs(j==m-1?i+1:i,j==m-1?0:j+1,s|(1<<j)))%mod;
    dp[i][j][s]=an;
    return an;
}
```

