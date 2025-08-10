# 背包dp

## 01背包

压缩前 

```c++
dp[i][j] 前i件物品背包容量j最大价值
    dp[i][j]=max(dp[i-1][j],dp[i-1][j-cost[i]]+value[i])
```

code：

```c++
const int n;
int dp[n+1];
int value[n+1]；
int cost[n+1];
//n为物品数 m为背包大小
for(i=1;i<=n;i++){
    for(j=m;j>=cost[i];j--){
        dp[j]=max(dp[j],dp[j-cost[i]]+value[i]);
    }
}
return dp[m];
```

难题

[1049. 最后一块石头的重量 II - 力扣（LeetCode）](https://leetcode.cn/problems/last-stone-weight-ii/description/?envType=problem-list-v2&envId=f4LgKPld)

思路 将总的石头分为接近sum/2的两堆（一堆大于等于一堆小于等于）用01背包（背包容量sum/2）找到最多石头质量m

re=sum-2*m

#### 01背包小拓展（物品的价值会随着背包容量变化）

[P1417 烹调方案 - 洛谷](https://www.luogu.com.cn/problem/P1417)

由于这个状态转移方程 dp[j]=max(dp[j],dp[j-cost[i]]+value[i]);

在后面dp的东西是放在背包的最后侧

那么我们就要后dp受背包容量影响使答案增大幅度最大的

## 分组背包

板子：[P1757 通天之分组背包 - 洛谷](https://www.luogu.com.cn/problem/P1757)

看成每一组内的01背包

思路：循环组->循环背包容量->循环组内每一个物品

code：

```c++
//num代表组的数量 cnt代表该组内物品数量 m代表背包容量
for(int k=1;k<=num;k++){
    for(i=m;i>=0;i--){
        for(j=1;j<=cnt[k];j++){
            if(i>=w[t[k][j]]){
                dp[i]=max(dp[i],dp[i-w[k][j]]+c[t[k][j]]);
            }
        }
    }
}
```

难题：[2218. 从栈中取出 K 个硬币的最大面值和 - 力扣（LeetCode）](https://leetcode.cn/problems/maximum-value-of-k-coins-from-piles/description/?envType=problem-list-v2&envId=f4LgKPld)

将每个栈看成每个组，将前缀和和前缀和中元素个数看成价值和重量



## 有依赖的背包

板子：[P1064 [NOIP 2006 提高组\] 金明的预算方案 - 洛谷](https://www.luogu.com.cn/problem/P1064)

可以看成分组背包 ，以一个主件作为一个组，每一组里面包含只选主件和主件加附件

如果不用分组背包 ps：要开另外一个数组 在每一个组内dp2只能利用原来的dp，操作完该组后再拷贝到dp



## 完全背包

压缩前

```c++
dp[i][j] 前i件物品背包容量j最大价值
    dp[i][j]=max(dp[i-1][j],dp[i][j-cost[i]]+value[i])
                            //此处和01不同，01是dp[i-1][j-cost[i]]
```

与01相反

code

```c++
  for (int i = 0; i < m; i++) {
        for (int j = c[i]; j <= t; j++) {//注意j循环顺序不能变 不然就是01背包了
            dp[j] = max(dp[j], dp[j - c[i]] + v[i]);
        }
    }
```



## 多重背包

每个物品有多件 ，有n件物品可以看作选n次的01背包

### 二进制优化

二进制优化 将物品分作2的次方的大物品 大物品组合可以表示所有的数

code：

```c++
   for (int i = 1; i <= n; i++) {
       cin >> v >> w >> m;//v价值 w重量 m个数
       ll c = 1;
       while (m > c) { 
           m -= c;
           ve.push_back({ c * w,c * v });
           c <<= 1;
       }
       ve.push_back({ m * w,m * v });
   }
```

### 单调队列优化

根据物品重量的余数分组

```c++
//w重量 t背包容量
for(int i=0;i<w;i++){
    for(int j=i;j<c;j+=w){
    }
}
```

未空间压缩版本

```c++
//n组物品 背包容量t v价值 w重量 m个数
ll n, t, v, w, m,temp;
ll ca(const vector<vector<ll>>&dp, int i, int k) {//不传引用会tle
    return dp[i - 1][k] - k / w * v;
}
int main() {
    ios::sync_with_stdio(false);
    cin.tie(0), cout.tie(0);
    cin >> n >> t;
    vector<vector<ll>>dp(n + 1, vector<ll>(t + 1,0));
    for (int i = 1; i <= n; i++) {
        cin >> v >> w >> m;
        for (int j = 0; j <= min(t, w - 1); j++) {
            deque<ll>qu;//注意此时队列要写在第二层循环里面
            for (int k = j; k <= t; k += w) {
                while (!qu.empty()&&ca(dp,i,k)>=ca(dp,i,qu.back())) {
                    qu.pop_back();
                }
                qu.push_back(k);
                if (qu.front() == k - (m + 1) * w)qu.pop_front();
                temp = qu.front();
                dp[i][k] = k / w * v + ca(dp, i, temp);
            }
        }
    }
    cout << dp[n][t] << endl;
```

空间压缩版本

```c++
//即从右往左计算
ll w, v, m;
ll n, t;
int ca(vector<ll>& dp, int k) {
    return dp[k] - k / w * v;
}
    cin >> n >> t;
    vector<ll>dp(t + 1, 0);
    for (int i = 0; i < n; i++) {
        cin >> v >> w >> m;
        for (int j = 0; j <= min(t, w - 1); j++) {
            deque<int>de;
            for (int k = t - j, cnt = 1; k > 0 && cnt <= m; k -= w, cnt++) {
                while (!de.empty() && ca(dp, de.back()) <= ca(dp, k)) {
                    de.pop_back();
                }
                de.push_back(k);
            }
            for (int k = t - j, enter = k - m * w; k > 0; k -= w, enter -= w) {
                if (enter >= 0) {
                    while (!de.empty() && ca(dp, de.back()) <= ca(dp, enter))de.pop_back();
                    de.push_back(enter);
                }
                dp[k] = k / w * v + ca(dp, de.front());
                if (de.front() == k)de.pop_front();
            }
        }
    }
    cout << dp[t] << endl;
```



## 混合背包

01完全和多重都有 分开处理





#### 背包难题

[F - Knapsack for All Segments](https://atcoder.jp/contests/abc159/tasks/abc159_f)难在将n2s优化为ns

