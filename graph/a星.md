# a*

![image-20250423185222952](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250423185222952.png)

dj求的是一个点到所有点的最短距离 而a*是在能找出预估函数的情况下优化 找从一个点到另一个点的最短距离

dj是a*的一种特殊情况，即预估函数为0

其他地方和dj一样，只是priority_queue中放的是原点到当前点的距离+预估函数

## 常见的预估函数

### 1.曼哈顿距离

![image-20250423185558013](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250423185558013.png)

例子：01网格图，每个点可以上下左右移动，1能走0不能走，当前点到目标点的最短距离即为曼哈顿距离即预估函数用曼哈顿距离



### 2.对角线距离

![image-20250423222631491](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250423222631491.png)

例子：01网格图，每个点可以上下左右（以及左上左下右上右下）移动，1能走0不能走，当前点到目标点的最短距离即为曼哈顿距离即预估函数用曼哈顿距离

### 3.欧式距离![image-20250423222738254](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250423222738254.png)

code：

```c++
vector<pa>*node=new vector<pa>[n+1];
        for(auto x:times){
            node[x[0]].push_back({x[2],x[1]});
        }
        vector<bool>vis(n+1,false);
        vector<int>dis(n+1,inf);
        priority_queue<pa,vector<pa>,greater<pa>>pq;
        dis[k]=0;
        pq.push({0,k});
        while(!pq.empty()){
            pa temp=pq.top();
            pq.pop();
            if(!vis[temp.second])
            {
                vis[temp.second]=true;
                for(auto x:node[temp.second]){
                    if(!vis[x.second]){
                        if(temp.first+x.first<dis[x.second]){
                            dis[x.second]=temp.first+x.first;
                            pq.push({dis[x.second]+f(x,y,targetx,targety),x.second});
                            //其他和dj一样,就进入堆的距离加上了预估函数
                        }
                    }
            }
            }
        }
        int an=0;
        for(int i=1;i<=n;i++){
            if(!vis[i])return -1;
            else an=max(an,dis[i]);
        }
        return an;
```

