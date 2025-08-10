# Floyd

![image-20250424201926924](C:\Users\LENOVO\AppData\Roaming\Typora\typora-user-images\image-20250424201926924.png)

也可以跑n次dj实现只是要求边权非负，而floyd不管正负

code：

```c++
for(int k=1;k<=n;k++){
    	for(int i=1;i<=n;i++){
    		for(int j=1;j<=n;j++){
    			graph[i][j]=min(graph[i][j],graph[i][k]+graph[k][j]);
    		}
    	}
    }
//注意循环顺序
```

