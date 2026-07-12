**笛卡尔树**

给定n组key_value值（一般默认key没有相同值），key按照搜索二叉树组织，value按照小根堆或者大根堆组织（不要求是完全二叉树，只要求左右孩子比当前节点大或者小就行了）

**建树过程：时间复杂度为$O(n)$**

- 按key排序
- 当前节点value假设为x，根据x的value值，在单调栈里依次弹出节点
- 最晚弹出的节点为x的左树
- 弹出结束后假如栈顶元素为z，x为z的右树
- 节点x加入单调栈
- 结束后栈底元素为根节点

```java
template<class Cmp=less<int>>
struct CartesianTree{//默认升序
	int root,n;
	vector<int>lson,rson;
	vector<pii>node;
	Cmp cmp;
	CartesianTree(int _n=0,Cmp _cmp=Cmp()):n(_n),cmp(_cmp){}
	CartesianTree(vector<pii>&_node,Cmp _cmp=Cmp()):node(_node),cmp(_cmp){
		n=node.size();
		lson.assign(n,-1);
		rson.assign(n,-1);
		build();
	}
	void build(){
		stack<int>stk;
		sort(all(node));
		for(int i=0;i<n;i++){
			auto&[x,y]=node[i];
			int last=-1;
			while(!stk.empty()&&cmp(y,node[stk.top()].second)){
				last=stk.top();
				stk.pop();
			}
			if(last!=-1){
				lson[i]=last;
			}
			if(!stk.empty()){
				rson[stk.top()]=i;
			}
			stk.push(i);
		}
		while(!stk.empty()){
			root=stk.top();
			stk.pop();
		}
	}
};
```



**笛卡尔树的性质**

假如给定一个数组arr，以下标作为key，arr\[i\]为val

- 笛卡尔树上的每一个子树都是一个连续的区间
- 区间$[a,b]$的最值就是$lca(a,b)$维护的值

