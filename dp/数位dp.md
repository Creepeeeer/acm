```

```

# 数位dp

前导0指将1补成00001，对于某些问题前导0无影响如求所有数位之和，则可以不考虑前导0

但对于如每个数位各不相同问题，10本来符合题意，但补成010就不符合了，此时要考虑前导0

### 记忆化

如果!limit_low&&!limit_high&&is_num&&memo[][]数组非0则可直接记忆化返回

如果!limit_low&&!limit_high&&is_num 将an记忆化

记忆化的东西必须保证不受到任何限制如is_limit以及题目给的条件

记忆化数组根据i和mask来写

具体看例子

### mask

mask是需要传递的前后状态

具体看例子

### 1：不考虑前导0

不管前导0就不需要is_num参数 不用考虑跳过本位直接填0

ps：下面两个代码都是包括0的，如果不包括还需将0情况剔除

#### 只有上界

code:

```c++
//枚举到n
string s=to_string(n);
int m=sr.size();
int dfs(int i,bool limit_high){
if(i==m)return 1;
int an=0,i,j;
int lo=0;
int up=limit_high?s[i]-'0':9;
for(i=lo;i<=up;i++){
an+=dfs(i+1,limit_high&&i==up);
}
return an;
}
//主函数调用
return dfs(0,true);
```



#### 同时有上下界

code:

```c++
//枚举从l到r
string sl=to_string(l);
string sr=to_string(r);
int m=sr.size();
//将l与r补齐
while(sl.size()<m){
    sl='0'+sl;
}
int dfs(int i,bool limit_low,bool limit_high){
if(i==m)return 1;
int an=0,i,j;
int lo=limit_low?sl[i]-'0':0;
int up=limit_high?sr[i]-'0':9;
for(i=lo;i<=up;i++){
an+=dfs(i+1,limit_low&&lo==i,limit_high&&up==i);
}
return an;
}
//主函数调用
return dfs(0,true,true);
```





ps:题目要求对其他数位有其他约束时不能直接对lo，up修改，而是在下面for循环时改为max(lo,minlimit),min(hi,maxlimit);

e.g



### 2：考虑前导0

#### 只有上界

code:

```c++
//全局变量
string s=to_string(n);
int size=s.size(); 
//递归函数
int dfs(int i,int mask,bool is_limit,bool is_num){   
if(i==size)return is_num;
if(!is_limit&&is_num&&memo[i][mask]!=-1)return memo[i][mask];
   //is_num确保前面有数 不会进行跳过当前数位的dfs
int an=0;
if(!is_num){// 可以跳过当前数位
    an=dfs(i+1,mask,false,false);
}
int j;
int lo=is_num?0:1;//确定下界
int up=is_limit?s[i]-'0':9;//确定上界
for(j=lo;j<=up;j++){
    if((mask>>j&1)==0){
        an+=dfs(i+1,mask|(1<<j),is_limit&&j==up,true);
    }
}
if(!is_limit&&is_num)
memo[i][mask]=an;
return an;
}
return dfs(0,0,true,false);
```



####  同时拥有上下界

code:

```c++
//枚举从l到r
string sl=to_string(l);
string sr=to_string(r);
int m=sr.size();
//将l与r补齐
while(sl.size()<m){
    sl='0'+sl;
}
int dfs(int i,bool limit_low,bool limit_high，bool is_num){
if(i==m)
{
    return 1;//如果包括0
    //如果不包括0则写成  return is_num;
 }
int an=0,i,j;
if(!is_num&&sl[i]=='0') //前面填的都是0，limit_low一定是true
 an+=dfs(i+1,true,false,false);
int lo=limit_low?sl[i]-'0':0;
int up=limit_high?sr[i]-'0':9;
int down=is_num?0:1;//考虑前导0的话前面已经处理过前面数位都是0的情况，此时要修改下界
for(i=max(lo,down);i<=up;i++){
an+=dfs(i+1,limit_low&&lo==i,limit_high&&up==i,true);
}
return an;
}
//主函数调用
return dfs(0,true,true,false);
```





### 3：例子

e.g.

题目[2376. 统计特殊整数 - 力扣（LeetCode）](https://leetcode.cn/problems/count-special-integers/description/?envType=problem-list-v2&envId=f4LgKPld)

code:

```c++
//全局变量
vector<vector<int>> memo(m, vector<int>(1 << 10, -1)); // -1 表示没有计算过  记忆化数组
string s=to_string(n);
int size=s.size(); 

//递归函数
int dfs(int i,int mask,bool is_limit,bool is_num){   
if(i==size)return is_num;
if(!is_limit&&is_num&&memo[i][mask]!=-1)return memo[i][mask];
   //is_num确保前面有数 不会进行跳过当前数位的dfs
int an=0;
if(!is_num){// 可以跳过当前数位
    an=dfs(i+1,mask,false,false);
}
int j;
int up=is_limit?s[i]-'0':9;//确定上界
for(j=is_num?0:1;j<=up;j++){
    if((mask>>j&1)==0){
        an+=dfs(i+1,mask|(1<<j),is_limit&&j==up,true);
    }
}
if(!is_limit&&is_num)
memo[i][mask]=an;
return an;
}


//主函数
    return dfs(0,0,true,false);

```

i代表当前来的位

mask代表前面选的数的集合 （用位运算记录每一位是否选过）

mask 表示前面选过的数字集合，换句话说，第 i 位要选的数字不能在 mask 中。

isLimit 表示当前是否受到了 n 的约束（注意要构造的数字不能超过 n）。若为真，则第 i 位填入的数字至多为 s[i]，否则可以是 9。如果在受到约束的情况下填了 s[i]，那么后续填入的数字仍会受到 n 的约束。例如 n=123，如果 i=0 填的是 1 的话，i=1 的这一位至多填 2。如果 i=0 填的是 1，i=1 填的是 2，那么 i=2 的这一位至多填 3。

isNum 表示 i 前面的数位是否填了数字。若为假，则当前位可以跳过（不填数字），或者要填入的数字至少为 1；若为真，则要填入的数字可以从 0 开始。例如 n=123，在 i=0 时跳过的话，相当于后面要构造的是一个 99 以内的数字了，如果 i=1 不跳过，那么相当于构造一个 10 到 99 的两位数，如果 i=1 跳过，相当于构造的是一个 9 以内的数字。

为什么要定义 isNum？因为 010 和 10 都是 10，如果认为第一个 0 和第三个 0 都是我们填入的数字，这就不符合题目要求了，但 10 显然是符合题目要求的。





[P2602 [ZJOI2010\] 数字计数 - 洛谷](https://www.luogu.com.cn/problem/P2602)

code:

```c++
string sl, sr;
int m;
ll memo[13][13];
ll re[10] = { 0 };
ll dfs(int i, int sum,int num,bool low_limit, bool high_limit, bool is_num) {
    ll an = 0;
    if (i == m)
    {
        return sum;
    }
    if (!low_limit && !high_limit && is_num && memo[i][sum] != -1)
    {
        return memo[i][sum];
    }
    if (!is_num && sl[i] == '0') an += dfs(i + 1,sum,num ,true, false, false);
    int lo = low_limit ? sl[i] - '0' : 0;
    int hi = high_limit ? sr[i] - '0' : 9;
    int down = is_num ? 0 : 1;
    for (int j = max(down, lo); j <= hi; j++) {
        an += dfs(i + 1, sum + (j == num), num, low_limit && j == lo, high_limit && j == hi, true);
    }
    if (!low_limit && !high_limit && is_num) {
        memo[i][sum] = an;
    }
    return an;
}
int main() {
    cin >> sl >> sr;
    m = sr.size();
    while (sl.size() < m)sl = '0' + sl;
    int i;
    for (i = 0; i < 10; i++) {
        memset(memo, -1, sizeof(memo));
        re[i] = dfs(0, 0, i, true, true, false);
    }
    for (int i = 0; i < 10; i++)cout << re[i] << ' ';
```

### 4 经验

运用场景 给定一个范围内（l,r)范围内满足某某条件的数的个数或者数的总和

如果不统计个数的话 看情况是否用dfs 某一个参数存sum(即memo多开一维存sum，一定要把sum当做一维可变参数不然会错)

考虑用个数的信息整合出所要的sum，返回值cnt与sum的pa

[E - Popcount Sum 3](https://atcoder.jp/contests/abc406/tasks/abc406_e)

```c++
const int mod=998244353;
pa memo[65][65][2];
int m;
pa dfs(int i,int num,const string&s,bool is_limit){
	if(i==m){
		if(num==0)return {1,0};
			else return {0,0};
	}
	if(memo[i][num][is_limit].first!=-1) return memo[i][num][is_limit];
	pa an={0,0};
	int lo=0;
	int hi=is_limit?s[i]-'0':1;
	for(int j=lo;j<=hi;j++){
		pa temp=dfs(i+1,num-(j==1),s,is_limit&&j==hi);
		an.first+=temp.first;
		an.first%=mod;
		an.second+=temp.second;
		an.second%=mod;
		if(j==1){
			an.second+=(1ll<<(m-i-1))%mod*temp.first%mod;
			an.second%=mod;
		}
		
	}
	memo[i][num][is_limit]=an;
	return an;
}

```

