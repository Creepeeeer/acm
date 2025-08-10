# 1.dijkstra 板子



![image-20250421174247012](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250421174247012.png)



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
                            pq.push({dis[x.second],x.second});
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



# 2.分层最短路问题

![image-20250421174416970](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250421174416970.png)

类似于bfs和dj 但区别在于每个点会有自己的状态从而影响最终的结果 比如有某些钥匙从而能走没有钥匙能走的路



## 1.bfs分层最短路

https://leetcode.cn/problems/shortest-path-to-get-all-keys/?envType=problem-list-v2&envId=f4LgKPld

将图建为含x,y和所拥有钥匙的状态（钥匙用状压）跑bfs

## 2.dj分层最短路

[LCP 35. 电动车游城市 - 力扣（LeetCode）](https://leetcode.cn/problems/DFPeFJ/description/?envType=problem-list-v2&envId=f4LgKPld)

将图建为含当前所在点和此时拥有的电量

[P4568 [JLOI2011\] 飞行路线 - 洛谷](https://www.luogu.com.cn/problem/P4568)

将图建为



