归并分治，在归并排序的过程中完成答案的统计。当前区间二分成左区间、右区间，问题的答案 = 左区间产生的答案 + 右区间产生的答案 + 左区间跨右区间产生的答案。关键就是计算跨区间产生的答案，利用左右区间各自有序的便利性加速统计。若每次合并是线性的，时间复杂度就是 $O(n\log n)$。

CDQ分治也是这种按左右区间划分贡献的思路，可以结合树状数组、线段树等数据结构。假如一次大小为 $n$ 的跨区间统计需要 $O(n)$ 次操作，每次操作 $O(\log n)$，那么：

$$
T(n)=2T(n/2)+O(n\log n)=O(n\log^2 n).
$$

这样一来，有些写法可以用排序替代手写归并，整体仍然是这个时间复杂度，代码也更容易实现。但要保留递归划定的左右归属，不能在还需要区分左右的时候把它们混在一起。是否节省额外空间要看具体写法，不是用了排序就一定不用辅助数组。

### 三维偏序

**本质就是每嵌套一层CDQ，在当前左边对右边的贡献中消掉一维偏序；当剩下两维的时候，就可以双指针+树状数组解决。**

有 $n$ 个元素，第 $i$ 个元素有 $a_i,b_i,c_i$ 三个属性，设 $f(i)$ 表示满足 $a_j\leq a_i$ 且 $b_j\leq b_i$ 且 $c_j\leq c_i$ 且 $j\ne i$ 的 $j$ 的数量。

先按 $(a,b,c)$ 字典序排序。对于 $cdq(l,r)$，先算 $cdq(l,m)$ 和 $cdq(m+1,r)$，然后再算跨区间的贡献。此时左右两边都已经按 $b$ 有序，那么双指针遍历，把第三维作为下标加到树状数组里面，最后按 $b$ 归并。

下面这份代码没有合并重复点。普通的左边 $\to$ 右边会漏掉完全相同的点对右边 $\to$ 左边的贡献，所以额外保留了一次反向统计：只有左右边界的 $a$ 相等时才可能产生反向贡献，再用 $b,c$ 筛选。结合最开始的字典序，能反向贡献的实际上就是三个坐标完全相同的点。每对不同的点在被分开的那层统计，不会把自己算进去。

使用前要先排序 `info[1..n]`，并把 $c$ 离散化到 $1\sim N$，`id` 保存原下标，`cnt[id]` 保存答案。每次跨区间统计前后树状数组都是空的，所以这里 `clean` 可以直接把涉及的位置清零；不能在还要保留其他贡献的时候这样清空。

```cpp
struct Info {
	int a, b, c, id;
	bool operator<(const Info&x)const {
		if (a != x.a)return a < x.a;
		else if (b != x.b)return b < x.b;
		else return c < x.c;
	}
};
const int N = 2e5;
int a[N + 1];
void add(int x) {
	for (int i = x; i <= N; i += i & -i)a[i]++;
}
void clean(int x) {
	for (int i = x; i <= N; i += i & -i)a[i] = 0;
}
int query(int x) {
	int an = 0;
	for (int i = x; i > 0; i -= i & -i)an += a[i];
	return an;
}
Info info[N + 1], info2[N + 1];
int cnt[N + 1];
void cdq(int l, int r) {
	if (l >= r)return;
	int m = (l + r) >> 1;
	cdq(l, m);
	cdq(m + 1, r);
	for (int i = m + 1, j = l; i <= r; i++) {
		while (j <= m && info[i].b >= info[j].b) {
			add(info[j].c);
			j++;
		}
		cnt[info[i].id] += query(info[i].c);
	}
	for (int i = l; i <= m; i++)clean(info[i].c);
	int ma = info[l].a, mi = info[m + 1].a;
	for (int i = l; i <= m; i++)ma = max(ma, info[i].a);
	for (int i = m + 1; i <= r; i++)mi = min(mi, info[i].a);
	if (ma == mi) {
		for (int i = l, j = m + 1; i <= m; i++) {
			while (j <= r && info[i].b >= info[j].b) {
				if (info[j].a == ma) {
					add(info[j].c);
				}
				j++;
			}
			if (info[i].a == ma) {
				cnt[info[i].id] += query(info[i].c);
			}
		}
		for (int i = m + 1; i <= r; i++)clean(info[i].c);
	}
	for (int i = l, j = m + 1, k = i; i <= m || j <= r;) {
		if (i <= m && j <= r) {
			if (info[i].b < info[j].b)info2[k++] = info[i++];
			else info2[k++] = info[j++];
		}
		else if (i <= m)info2[k++] = info[i++];
		else info2[k++] = info[j++];
	}
	for (int i = l; i <= r; i++)info[i] = info2[i];
}
```

### CDQ优化DP

本质就是CDQ，但是此时左边的DP值会拿来更新右边，所以要先算完左边，完成左边对右边的转移，再递归右边。计算顺序是：

1. $cdq(l,m)$
2. $cal(l,r)$
3. $cdq(m+1,r)$

这里 $cal(l,r)$ 只算当前左半对右半的贡献。若先递归右边，再把左边的贡献补进去，右边已经用旧的DP值向后转移过了，就可能漏掉后续影响。外层下标的转移顺序也要保留，按其他维排序时可以用临时数组或下标数组。

### 四维偏序(两次CDQ)

这里按四维偏序优化DP来写，每个点有 $(a,b,c,d)$ 四个属性，$e$ 是这个点的权值，不是第五维。选一条四维都不下降的链，求最大的权值和。

先按 $(a,b,c,d)$ 字典序排序，设 $dp[id_i]$ 表示以排序后第 $i$ 个点结尾的最大权值和，那么：

$$
dp[id_i]=\max\left(e_i,\ \max_{\substack{j<i\\a_j\leq a_i,\ b_j\leq b_i\\c_j\leq c_i,\ d_j\leq d_i}}\{dp[id_j]+e_i\}\right).
$$

这里 $j<i$ 指排序后的下标，`id` 只是用来找到这个点的DP值。初始 `dp[i]=e`，表示只选自己，最后取所有DP值的最大值，对应至少选一个点。

#### 两层CDQ怎么消维

**本质就是外层CDQ消掉 $a$，内层CDQ消掉 $b$，剩下 $c,d$ 两维用双指针+树状数组。**

外层 `cdq(l,r)` 先递归左边，然后算左边对右边的贡献。因为最开始按 $(a,b,c,d)$ 字典序排过序，左边的 $a$ 一定不大于右边，所以当前只需要检查 $b,c,d$。

把当前区间复制到 `info2`，左边标成 `op=0`，表示ADD；右边标成 `op=1`，表示QUERY。按 `cmp2` 排序，先比较 $b$，$b$ 相等时比较 `op`，保证ADD在QUERY前面，这样 $b$ 相等的贡献也不会漏掉。

然后跑内层 `cdq2(l,r)`。内层按这个顺序分左右，所以当前左边对右边又自动满足 $b_j\leq b_i$，只剩 $c,d$。两边递归完之后已经分别按 $c$ 有序，双指针把 $c_j\leq c_i$ 的左边ADD放进树状数组，再查询 $d_i$ 的前缀最大值。

```text
cmp：按(a,b,c,d)字典序排序
    cdq：外层左 -> 右，保证a合法
        复制到info2，外层左边op=0，右边op=1
        cmp2：按(b,op)排序
            cdq2：内层左 -> 右，保证b合法
                双指针保证c合法
                树状数组查询d的前缀最大值
```

“消掉一维”就是**当前这批跨区间的候选点对已经满足这一维，里面不用再判断它**，不是每调用一次 `cdq` 就消掉一维。两次CDQ指两层嵌套的分治，不是整个数组独立跑两遍。

#### op怎么筛选

`op` 表示当前这次外层跨区间统计里的身份，进入内层之后一直不变。内层还要结合当前左右位置继续筛选：

| 外层身份 | 内层当前位置 | 当前跨区间时的操作 |
| --- | --- | --- |
| op=0，ADD | 左边 | add |
| op=1，QUERY | 右边 | query |
| op=0，ADD | 右边 | 不操作 |
| op=1，QUERY | 左边 | 不操作 |

所以代码里左边遍历到的点只有 `!info2[j].op` 才add，右边只有 `info2[i].op` 才query。**一直符合ADD身份的才add，一直符合QUERY身份的才query，否则当前这次不操作。** 不需要把 `op` 定成多维，也不能内层左边就重新赋成0，右边就重新赋成1，那样会丢掉外层的限制。

这里不操作只是当前跨区间不操作，不代表从递归里删掉。比如内层右边的ADD，递归处理右半内部时，仍然可能更新同在右半的QUERY。对于一对合法的ADD和QUERY，恰好在内层某个区间第一次被分到左右两边，这时计算贡献；同在一边的时候交给子区间。

如果继续扩展到更多维，往下一维传的时候，只拿当前左边的ADD和右边的QUERY，保留原身份就行。被筛掉的点只是这一次跨区间用不到，不影响当前维度自己的左右递归。

#### 为什么外层和内层递归顺序不一样

外层是DP，左边的DP值算完之后才能用来更新右边，所以顺序是：

```text
cdq(l,m)
复制、标op、按cmp2排序、cdq2(l,r)，计算左边对右边的贡献
cdq(m+1,r)
```

内层虽然也在更新DP，但它只是算**这一批固定ADD对QUERY的贡献**。外层左边已经算完，这次所有 `op=0` 的DP值都确定了；只有 `op=1` 会被更新，而且更新完也不会在这次内层里变成ADD。所以它是静态偏序的计算顺序：

```text
cdq2(l,m)
cdq2(m+1,r)
双指针+树状数组计算当前左边ADD对右边QUERY的贡献
按c排序，供上一层使用
```

**判断是不是DP型CDQ，关键看查询结果会不会成为当前这次计算里新的更新来源，不是看代码里有没有更新dp数组。** 外层右边之后还要用新的DP值继续转移，必须先收到左边贡献；内层QUERY只接收贡献，不往外转移，所以可以先递归两边再统计。

#### 代码

`info` 始终保持最开始的字典序，`info2` 给内层排序，`dp` 用原来的 `id` 访问。内层最后直接 `sort` 按 $c$ 排序，不用手写归并。

```cpp
struct Info{
	int a,b,c,d,e,id,op;
}info[N+1],info2[N+1];
bool cmp(const Info&x,const Info&y){
	if(x.a!=y.a)return x.a<y.a;
	if(x.b!=y.b)return x.b<y.b;
	if(x.c!=y.c)return x.c<y.c;
	return x.d<y.d;
}
bool cmp2(const Info&x,const Info&y){
	if(x.b!=y.b)return x.b<y.b;
	return x.op<y.op; // b相同时，ADD在QUERY前面
}
bool cmp3(const Info&x,const Info&y){
	return x.c<y.c;
}
ll dp[N+1];
ll tr[N+1];
void add(int x,ll v) {
    for (int i = x; i <= N; i += i & -i) {
        tr[i]=max(tr[i],v);
    }
}
void clean(int x) {
    for (int i = x; i <= N; i += i & -i) {
    	tr[i]=-lnf;
    }
}
ll query(int x) {
    ll an=-lnf;
    for (int i = x; i > 0; i -= i & -i) {
        an=max(an,tr[i]);
    }
    return an;
}

void cdq2(int l,int r){
	if(l==r)return;
	int m=(l+r)>>1;
	cdq2(l,m);
	cdq2(m+1,r);
	// 两边按c有序，只算内层左边ADD对右边QUERY的贡献
	for(int i=m+1,j=l;i<=r;i++){
		while(j<=m&&info2[i].c>=info2[j].c){
			if(!info2[j].op)add(info2[j].d,dp[info2[j].id]);
			j++;
		}
		if(info2[i].op){
			dp[info2[i].id]=max(dp[info2[i].id],info2[i].e+query(info2[i].d));
		}
	}
	for(int i=l;i<=m;i++)clean(info2[i].d);
	sort(info2+l,info2+r+1,cmp3);
}
void cdq(int l,int r){
	if(l==r)return;
	int m=(l+r)>>1;
	cdq(l,m); // 先算完外层左边的DP值
	for(int i=l;i<=r;i++){
		info2[i]=info[i];
		if(i<=m)info2[i].op=0;
		else info2[i].op=1;
	}
	sort(info2+l,info2+r+1,cmp2);
	cdq2(l,r); // 左边更新右边
	cdq(m+1,r); // 右边再用更新后的DP值继续转移
}
signed main()
{
    ios::sync_with_stdio(false);
    cin.tie(0), cout.tie(0);
    int n;
    cin>>n;
    vector<int>yd;
    for(int i=1;i<=n;i++){
    	int a,b,c,d,e;
    	cin>>a>>b>>c>>d>>e;
    	info[i]={a,b,c,d,e,i,0};
    	dp[i]=e;
    	yd.push_back(d);
    }
    sort(all(yd));yd.erase(unique(all(yd)),yd.end());
    for(int i=1;i<=n;i++){
    	info[i].d=lower_bound(all(yd),info[i].d)-yd.begin()+1;
    }
    fill(tr+1,tr+N+1,-lnf);
    sort(info+1,info+n+1,cmp);
    cdq(1,n);
    cout<<*max_element(dp+1,dp+n+1);
    return 0;
}
```

几个细节：

- `info2` 在内层会被排序，所以不能用当前数组下标访问DP，要用 `id`。每次外层重新从 `info` 复制并标记 `op`，内层才不会影响外层的分治顺序。
- `cdq2` 的左右归属是最开始按 $(b,op)$ 划定的。子区间按 $c$ 排序只改变子区间内部顺序，不会把点移到父区间的另一半；父区间统计完成后才把两边一起按 $c$ 排序。
- 树状数组维护最大值，初始和清空都用 `-lnf`，这样权值为负也能处理。每次跨区间统计前树状数组都是空的，统计完再清掉；代码把左边所有点的更新路径都清空，多清的部分也没关系，因为此时已经不需要保留本次贡献。这里 $e$ 是 `int`，且 $n\leq 5\times10^4$，`-lnf` 足够小，查不到前驱时 `e-lnf` 不会超过初值 $e$。
- 这份代码要求 $1\leq n\leq N$，四维条件都是 $\leq$，每个点最多选一次，允许四维完全相同的不同点接在一起。相同坐标的点按排序得到的任意顺序处理即可，不需要像静态计数那样反向统计；链允许跳过点，负权点可以不选。如果题目要求严格小于，或者还限制原输入下标的先后，就不能原样套用。

内层每层的双指针+树状数组是 $O(k\log N)$，最后排序是 $O(k\log k)$。因此一轮大小为 $k$ 的内层CDQ是 $O(k\log k\log N)$，外层再套一层，总复杂度是 $O(n\log^2 n\log N)$，通常写成 $O(n\log^3 n)$。两个点数组、DP和树状数组的空间是 $O(n+N)$，递归栈是 $O(\log n)$。
