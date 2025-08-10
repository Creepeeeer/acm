# 状压dp

![image-20250608105002356](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250608105002356.png)

![image-20250608105020478](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250608105020478.png)



### 1.将一堆数分为若干个子集的状压

[473. 火柴拼正方形 - 力扣（LeetCode）](https://leetcode.cn/problems/matchsticks-to-square/description/?envType=problem-list-v2&envId=f4LgKPld)

f(sta,cur,line) 

sta表示位图表示的哪些火柴能选哪些不能选

cur表示当前这条边还差多少长度选够

line表示还有多少条边要选

**但是dp表只要sta这一维因为sta能够决定cur和line**

### 2.枚举status的所有子集

如100100的子集有100100,100000,000100

**方法**

```c++
for(int j=status;j>0;j=(j-1)&status){}
```

**ps:状态中不包含0，如果需要要额外添加**

https://leetcode.cn/problems/distribute-repeating-integers/description/?envType=problem-list-v2&envId=f4LgKPld







