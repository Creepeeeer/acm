# kmp

**匹配大字符串s1中是否含有s2**

#### next数组(对s2求)

含义：不包含当前，前面的字符串前缀和后缀（不能包含整体，例如只有一个字符的时候不能算）最大匹配长度

但是可以交错，e.g aaaaat 在t位置的next值为4



#### 利用next数组加速匹配

如果此时s1的开头在i,s2位置在cur，匹配s1,s2这个字符不相等了，那么就把s2的开头放到i+cur-next[cur]的位置，以及此时从s2的next[cur]位置开始匹配

##### 新开头省了匹配过程

因为前缀等于后缀，那么相等的那一段就不用匹配了，直接从s2的next[cur]位置开始匹配

##### 淘汰不可能的出发点

为什么i到i+cur-next[cur]之间不用作为开始，假如这两点之间存在一个点作为开头能完整的匹配出s2，则next数组一定可以被这个点作为后缀开头更新的更大



**板子**

```c++
vector<int>get_next(string s2){
    int m=s2.size();
    vector<int>next(m);
    next[0]=-1;
    if(m==1)return next;//注意这里
    next[1]=0;
    int i=2,j=0;
    while(i<m){
        if(s2[i-1]==s2[j])next[i++]=++j;
        else if(j>0)j=next[j];
        else next[i++]=j;
    }
    return next;
}
int kmp(string s1,string s2){
    int n=s1.size(),m=s2.size();
    vector<int>next=get_next(s2);
    int i=0,j=0;
    while(i<n&&j<m){
        if(s1[i]==s2[j]){i++;j++;}
        else if(j>0)j=next[j];
        else i++;
    }
    return j==m?i-j:-1;
}
```





#### kmp例题

[P4391 [BalticOI 2009\] Radio Transmission 无线传输 - 洛谷](https://www.luogu.com.cn/problem/P4391)

**结论**：如果一个字符串s由一个循环节t出现若干次构成且这个循环节是最短的，即s=k*t+l,l为t的前缀<=> next[n]=(k-1) *t+l



[P4824 [USACO15FEB\] Censoring S - 洛谷](https://www.luogu.com.cn/problem/P4824)

首先对s2求next，用一个vector模拟删除过程，并且vector里存的pii是<s1的下标,之前s1和s2匹配了相等的s2下标>,那么下一个元素进来的时候如果vector不为空，那么就和vector.back().second+1下标位置匹配



##### **小拓展用法**

next数组求的是最长公共前后缀，那么去递归求next[next[i]]，next[next[next[i]]]......就可以得到所有的公共前后缀长度

**num[i]表示的是以0~i-1为最长前缀获得的总的公共前后缀个数** 注意这里的定义和next数组有点不一样，这里0~i-1已经是最长前缀了，而next数组是0~i-1区间的最长前缀

**初始化num[1]=1** 解释：如果一个字符串是以s[0]作为前缀的话，自己本身已经是一个公共前后缀了,（这里要注意上面num数组的定义）

```c++
void get_next(){
    nxt[0]=-1;
    nxt[1]=0;
    num[1]=1;
    int i=2,j=0;
    while(i<=n){
        if(s[i-1]==s[j]){
            j++;
            nxt[i]=j;
            num[i]=num[j]+1;
            i++;
        }
        else if(j>0)j=nxt[j];
        else{
            nxt[i]=j;
            num[i]=num[j]+1;
            i++;
        }
    }
}
```

[P3435 [POI 2006\] OKR-Periods of Words - 洛谷](https://www.luogu.com.cn/problem/P3435)

这个题就把num数组定义转变一下，转变为0~i-1为最长公共前后缀获得的最短公共前后缀

```c++
void get_next(){
	nxt[0]=-1;
	nxt[1]=0;
	num[1]=1;
	num[0]=inf;
	int i=2,j=0;
	while(i<=n){
		if(s[i-1]==s[j]){
			j++;
			nxt[i]=j;
			num[i]=num[j];
			i++;
		}
		else if(j>0)j=nxt[j];
		else{
			nxt[i]=j;
			num[i]=i;
			i++;
		}
	}
}
```

[P2375 [NOI2014\] 动物园 - 洛谷](https://www.luogu.com.cn/problem/P2375)

这个题最巧妙的是j指针不回退，从而保证了是O(1)时间

```c++
int j=0;
ll an=1;
for(int i=2;i<=n;i++){
    while(j>0&&s[i-1]!=s[j])j=nxt[j];
     if(s[i-1]==s[j])j++;
    if(2*j>i)j=nxt[j];//这里是if所以保证了是O(1)时间
    an=an*(num[j]+1)%mod;
}
cout<<an<<endl;

```
