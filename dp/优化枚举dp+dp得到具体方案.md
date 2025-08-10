## 优化枚举

![image-20250627160736160](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250627160736160.png)

**例题**

[188. 买卖股票的最佳时机 IV - 力扣（LeetCode）](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/description/?envType=problem-list-v2&envId=Exn5Frg2)

如果不优化的话dp表大小**n*k** ,每个点的枚举代价是n的就变成**n^2*k**了，预处理出一个结构让查询变成O(1)的



## 得到具体方案

### 1.利用dp表得到具体路径

**此时不能空间压缩因为要收集路径**

https://leetcode.cn/problems/smallest-sufficient-team/?envType=problem-list-v2&envId=Exn5Frg2

如果dp**i j**==dp**i-1 j**则本轮没选

## 2.添加额外的收集结构

[P1759 通天之潜水 - 洛谷](https://www.luogu.com.cn/problem/P1759)

此时要收集最小的字典序结构无法保证先收集的字典序一定小，如2比100大，添加string的dp数组

```c++

int dp[101][201][201];
string s[101][201][201];
int a[101],b[101],c[101];
	int m,v,n;
	cin>>m>>v>>n;
	for(int i=1;i<=n;i++)cin>>a[i]>>b[i]>>c[i];
	for(int i=1;i<=n;i++){
		for(int j=0;j<=m;j++){
			for(int k=0;k<=v;k++){
				if(j>=a[i]&&k>=b[i]){
					if(dp[i-1][j][k]>dp[i-1][j-a[i]][k-b[i]]+c[i]){
						dp[i][j][k]=dp[i-1][j][k];
						s[i][j][k]=s[i-1][j][k];
					}
					else if(dp[i-1][j][k]<dp[i-1][j-a[i]][k-b[i]]+c[i]){
						dp[i][j][k]=dp[i-1][j-a[i]][k-b[i]]+c[i];
						s[i][j][k]=s[i-1][j-a[i]][k-b[i]]+" "+to_string(i);
					}
					else{
						dp[i][j][k]=dp[i-1][j][k];
						string s1=s[i-1][j][k],s2=s[i-1][j-a[i]][k-b[i]]+" "+to_string(i);
						s[i][j][k]=min(s1,s2);
					}
				}
			}
		}
	}
	cout<<dp[n][m][v]<<endl;
	cout<<s[n][m][v].substr(1)<<endl;
	return 0;
}
```

