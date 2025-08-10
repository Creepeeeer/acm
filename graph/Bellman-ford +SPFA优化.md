# 1.Bellman-ford

![image-20250426104633404](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250426104633404.png)

	### 板子

```c++

vector<int>dis(n+1,inf);
	dis[1]=0;
	bool is_relax=false;
	for(int i=1;i<=n;i++){
		is_relax=false;
		for(auto [x,y,z]:graph){
			if(dis[x]!=inf&&dis[x]+z<dis[y]){
				dis[y]=dis[x]+z;
				is_relax=true;
			}
		}
		if(!is_relax){
			break;
		}
	}
}
```





[787. K 站中转内最便宜的航班 - 力扣（LeetCode）](https://leetcode.cn/problems/cheapest-flights-within-k-stops/description/?envType=problem-list-v2&envId=f4LgKPld)

利用松弛操作算轮数

```c++
 vector<int>dis(n,inf),dis2(n,inf);
        dis[src]=0,dis2[src]=0;
        for(int i=1;i<=k+1;i++){
            for(auto x:flights){
                if(dis[x[0]]!=inf&&dis[x[0]]+x[2]<dis2[x[1]]){
                    dis2[x[1]]=dis[x[0]]+x[2];
                }
            }
            dis=dis2;
        }
```

通过将本轮计算得出的结果与原dis分开，实现每轮计算从原点到所有点距离的最短距离最多只松弛一条边，即实现题目的中转次数





![image-20250426105457062](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250426105457062.png)

# 2.+SPFA优化

![image-20250426105642882](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250426105642882.png)

不加spfa不需要建图，加了则需要建图

### 板子

```c++
list<pa>graph[2001];
	vector<bool>inqueue(n+1);
	vector<int>times(n+1,0);
	vector<int>dis(n+1,inf);
	fill(inqueue.begin(),inqueue.end(),false);
	queue<int>qu;
	qu.push(1);
	inqueue[1]=true;
	times[1]++;
	dis[1]=0;
	while(!qu.empty()){
		int x=qu.front();
		qu.pop();
		inqueue[x]=false;
		for(auto [y,z]:graph[x]){
			if(dis[x]!=inf&&dis[y]>dis[x]+z){
				dis[y]=dis[x]+z;
				if(!inqueue[y]){
					inqueue[y]=true;
					qu.push(y);
					times[y]++;
					if(times[y]>n-1){//最多松弛n-1轮 如果轮数超过n-1就存在负环
						cout<<"YES"<<endl;
						return ;
					}
				}
			}
		}
	}
	cout<<"NO"<<endl;
```

