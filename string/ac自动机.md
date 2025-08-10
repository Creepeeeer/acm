#### **ac自动机功能**

给你若干目标字符串，还有一篇文章，返回每个目标字符串在文章中出现了几次

#### **fail指针含义：**

此时这个节点一定是某个目标字符串的前缀，设置这个前缀为s，要在所有目标串的前缀中找一个是s的最长真后缀，将当前节点的fail指针指向最长真后缀的尾部节点

 ## 经典ac自动机

首先将目标字符串建立trie，然后利用bfs层序遍历建立fail指针，0节点的fail指向自己，当遍历到某个节点的时候，设置它儿子的fail指针：假设当前节点指向儿子的路为a，依次沿着fail指针向上移动直到有一个节点指向儿子的路也为a的，将之前那个儿子指向这个儿子，如果一直找不到就指向0

遍历文章：首先从0开始，到文章的x字符，如果当前节点有指向x的路，那么就移动到x，并把x的fail指针沿途所有节点的times++，如果没有指向x的路，那么就沿着fail向上找哪个节点有指向x的路，如果找到了就移动到x，并把x的fail指针沿途所有节点的times++，如果一直找不到的话那么就节点设置为0

最后每个单词出现过的词频即为这个单词最后一个字符的times



## 优化ac自动机

### 优化fail指针建立过程

将原始建立的trie改变，如果当前节点tr[i] [x]为0，那么将tr[i] [x]设置为tr[fail[i]] [x],避免了绕圈

### 优化收集词频的时候

添加词频的时候只添加当前节点的词频，然后把当前节点与fail建立反图，最后类似树形dp从底到顶收集

### 优化提前跳出

如果是给出一些敏感词然后如果遇到一个敏感词就直接跳出的话，那么优化二就没必要了，我们此时设置一个alert数组，alert[i]=true表示这个点是一个单词的结尾，此时我们设置**alert[i]|=alert[fail[i]]**（因为fail节点的字符串是son字符串的后缀，并且原本一个点要加词频它的所有fail都加），那么只需要遍历句子的时候当前节点的的alert为true就跳出





[P5357 【模板】AC 自动机 - 洛谷](https://www.luogu.com.cn/problem/P5357)

用了优化1和优化2

```c++
const int N=2e5;
vector<int>g[N+1];
int tr[N+1][26];
int fail[N+1];
int times[N+1];
int in[N+1];
int cnt=0;
int n;
int add(string s){
	int cur=0;
	for(auto c:s){
		int num=c-'a';
		if(tr[cur][num]==0)tr[cur][num]=++cnt;
		cur=tr[cur][num];
	}
	return cur;
}
void get_fail(){
	queue<int>qu;
	for(int i=0;i<26;i++){
		if(tr[0][i]!=0)qu.push(tr[0][i]);
	}
	while(!qu.empty()){
		int m=qu.size();
		for(int i=0;i<m;i++){
			int cur=qu.front();
			qu.pop();
			for(int j=0;j<26;j++){
				if(tr[cur][j]==0){
					tr[cur][j]=tr[fail[cur]][j];
				}
				else{
					int cur2=tr[cur][j];
					qu.push(cur2);
					fail[cur2]=tr[fail[cur]][j];
				}
			}
		}
	}
}
void add_times(string s){
	int cur=0;
	for(auto c:s){
		int num=c-'a';
		cur=tr[cur][num];
		times[cur]++;
	}
	for(int i=1;i<=cnt;i++){g[i].push_back(fail[i]);in[fail[i]]++;}
	queue<int>qu;
	for(int i=1;i<=cnt;i++){
		if(in[i]==0)qu.push(i);
	}
	while(!qu.empty()){
		int u=qu.front();
		qu.pop();
		for(auto v:g[u]){
			if(--in[v]==0)qu.push(v);
			times[v]+=times[u];
		}
	}
}
    vector<int>en;
    for(int i=1;i<=n;i++){
    	cin>>s;
    	en.push_back(add(s));
    }
    get_fail();
    cin>>s;
    add_times(s);
    for(auto x:en)cout<<times[x]<<'\n';
```



[P3311 [SDOI2014\] 数数 - 洛谷](https://www.luogu.com.cn/problem/P3311)

用了优化1和优化3

```c++
int tr[N+1][10];
int fail[N+1];
bool alert[N+1];
int cnt=0;
void add(string s){
	int cur=0;
	for(int i=0;i<s.size();i++){
		int num=s[i]-'0';
		if(tr[cur][num]==0)tr[cur][num]=++cnt;
		cur=tr[cur][num];
	}
	alert[cur]=true;
}
void get_fail(){
	queue<int>qu;
	for(int i=0;i<10;i++){
		if(tr[0][i]!=0){
			qu.push(tr[0][i]);
		}
	}
	while(!qu.empty()){
		int siz=qu.size();
		for(int i=0;i<siz;i++){
			int cur=qu.front();
			qu.pop();
			for(int j=0;j<10;j++){
				int cur2=tr[cur][j];
				if(cur2==0){
					tr[cur][j]=tr[fail[cur]][j];
				}
				else{
					qu.push(cur2);
					fail[cur2]=tr[fail[cur]][j];
					alert[cur2]|=alert[fail[cur2]];// 其他和之前都差不多多加了这一句
				}
			}
		}
	}
}
```

