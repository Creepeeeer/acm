![image-20250426160238232](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250426160238232.png)

将小区间合并为大区间

区间长度由小到大dp

```c++
   for(int len=1;len<=n;len++){
            for(int l=0;l+len-1<n;l++){
                int r=l+len-1;
 
            }
        }
```

 ### 1.基于两侧端点讨论的可能性展开的主体思路O(n2)

dp<sub>l r</sub>去整合dp<sub>l+1 r</sub>与dp<sub>l r-1</sub>

[1312. 让字符串成为回文串的最少插入次数 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-insertion-steps-to-make-a-string-palindrome/description/?envType=problem-list-v2&envId=f4LgKPld)

### 2.基于范围上划分点的可能性展开o(n3)

因为除了枚举区间还要枚举分割点

从l到r上找一个点分割点i 将区间分为dp<sub>l i-1</sub>和dp<sub>i+1 r</sub>

#### 1.正向思维

先处理分割点再处理被分割成两块的区域

[1547. 切棍子的最小成本 - 力扣（LeetCode）](https://leetcode.cn/problems/minimum-cost-to-cut-a-stick/description/?envType=problem-list-v2&envId=f4LgKPld)

#### 2.逆向思维（难）

最后处理分割点

[312. 戳气球 - 力扣（LeetCode）](https://leetcode.cn/problems/burst-balloons/description/?envType=problem-list-v2&envId=f4LgKPld)

该题思路首先将左右补出两个虚拟1,并且区间[l,r]左右的气球还没有被戳破,找到的分割点时该区间上最后处理的点

code:

```c++

        int dp[302][302];
        int n=nums.size();
        nums.insert(nums.begin(),1);
        nums.push_back(1);
        for(int len=1;len<=n;len++){
            for(int l=1;l+len-1<=n;l++){
                int r=l+len-1;
                if(r-l==0)dp[l][r]=nums[l]*nums[l-1]*nums[l+1];
                else{
                    int an=0;
                    an=max(nums[l]*nums[l-1]*nums[r+1]+dp[l+1][r],nums[r]*nums[l-1]*nums[r+1]+dp[l][r-1]);
                    for(int i=l+1;i<r;i++){
                        an=max(an,dp[l][i-1]+dp[i+1][r]+nums[i]*nums[l-1]*nums[r+1]);
                    }
                    dp[l][r]=an;
                }
            }
        }
        return dp[1][n];
```

### 3.二者都有

[P4170 [CQOI2007\] 涂色 - 洛谷](https://www.luogu.com.cn/problem/P4170)

分析：

如果l,r颜色相同 

```c++
dp[l][r]=dp[l+1][r]=dp[l][r-1];
```

 （通过观察两侧端点）

解释：假如两侧都为A,全涂为A肯定比将两个A分开涂更优

  不同的的话则去找分界点 i

```c++
dp[l][r]=inf;
for(int i=l;i<r;i++){
dp[l][r]=min(dp[l][r],dp[l][i]+dp[i+1][r]);
}
```





#### dp除了区间l，r，还有一维参数代表状态

[546. 移除盒子 - 力扣（LeetCode）](https://leetcode.cn/problems/remove-boxes/description/?envType=problem-list-v2&envId=f4LgKPld)（难）

