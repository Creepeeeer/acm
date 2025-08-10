# 1.普通bfs

利用queue

![image-20250417171603547](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250417171603547.png)

### bfs的减枝

如果找的是最短路径 点不能重复进队列

可以利用一个unordered_set 记录已经进过队列的点避免重复

# 2.01bfs

利用deque

![image-20250417171644810](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250417171644810.png)

# 3.双向广搜

### 用途一

![image-20250419171050700](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250419171050700.png)

e.g[127. 单词接龙 - 力扣（LeetCode）](https://leetcode.cn/problems/word-ladder/?envType=problem-list-v2&envId=f4LgKPld)

code：

```c++
 int n=beginWord.size();
        if(find(wordList.begin(),wordList.end(),endWord)==wordList.end())return 0;
        unordered_set<string>dict(wordList.begin(),wordList.end());
        unordered_set<string>big,small,nex;
        small.insert(beginWord),big.insert(endWord);
        dict.erase(endWord);
        int an=2;
        while(small.size()>0){
            for(auto &x:small){
                string temp;
                for(int i=0;i<n;i++){
                    temp=x;
                    char old=x[i];
                    for(char c='a';c<='z';c++){
                        if(c!=old){
                        temp[i]=c;
                        if(big.find(temp)!=big.end())return an;//这里一定要用unordered_set的内置find是o(1)的
                        if(dict.find(temp)!=dict.end()){//不要用stlfind函数
                            nex.insert(temp);
                            dict.erase(temp);
                        }
                        }
                    }
                }
            }
            if(nex.size()<=big.size()){
                small=nex;
                nex.clear();
            }
            else{
                small=big;
                big=nex;
                nex.clear();
            }
            an++;
        }
        return 0;
```

### 用途二

![image-20250419173815337](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250419173815337.png)

对于数据量为40,50的题直接用搜索2^40会t

可以将数据分半两个2^20接近1e6 将两半搜索后整合起来

e.g.[P4799 [CEOI 2015\] 世界冰球锦标赛 (Day2) - 洛谷](https://www.luogu.com.cn/problem/P4799)

ps 这个题不能用背包dp 背包容量太大
