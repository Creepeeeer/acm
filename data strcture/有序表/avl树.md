**平衡性：任意子树的左树和右树高度差相差不超过1**

一个节点的左树val都比当前节点的val小，右树的val都比当前节点的val大

**节点信息**

```c++
struct tree{
		int l,r,key,height,count,siz;//左子树，右子树，点权，树高，词频，子树大小
}tr[N+1];
```



### 左旋和右旋

```c++
	int lrotate(int i){
		int&r=tr[i].r;
		tr[i].r=tr[r].l;
		tr[r].l=i;
		up(i);
		up(r);
		return r;
	}
	int rrotate(int i){
		int&l=tr[i].l;
		tr[i].l=tr[l].r;
		tr[l].r=i;
		up(i);
		up(l);
		return l;
	}
```



### 四种违规类型



#### **LL**

**height左-height右==2&&height左左>=height左右**

**方法：对头结点右旋**



#### **RR**

**height右-height左==2&&height右右>=height右左**

**方法：对头结点左旋**



#### **LR**

**height左-height右==2&&height左左<height左右**

**方法:对头结点的左孩子左旋，再右旋头结点**



#### RL

**height右-height左==2&&height右右<height右左**

**方法:对头结点的右孩子右旋，再左旋头结点**



**板子**

```c++
template<typename T>
class avl {
private:
	int trcnt, head;
	static constexpr T illegal=-1,MIN=-inf,MAX=inf;
	struct tree {
		int l, r, height, cnt, siz;
		T key;
	}*tr;
	void up(int i) {
		if(i==0)return;
		tr[i].siz = tr[tr[i].l].siz + tr[tr[i].r].siz + tr[i].cnt;
		tr[i].height = max(tr[tr[i].l].height, tr[tr[i].r].height) + 1;
	}
	int lrotate(int i) {
		int r = tr[i].r;
		tr[i].r = tr[r].l;
		tr[r].l = i;
		up(i);
		up(r);
		return r;
	}
	int rrotate(int i) {
		int l = tr[i].l;
		tr[i].l = tr[l].r;
		tr[l].r = i;
		up(i);
		up(l);
		return l;
	}
	int legal(int i) {
		int lh = tr[tr[i].l].height, rh = tr[tr[i].r].height;
		if (lh - rh > 1) {
			int& l = tr[i].l;
			if (tr[tr[l].l].height >= tr[tr[l].r].height)i = rrotate(i);
			else {
				l = lrotate(l);
				i = rrotate(i);
			}
		}
		else if (rh - lh > 1) {
			int& r = tr[i].r;
			if (tr[tr[r].r].height >= tr[tr[r].l].height)i = lrotate(i);
			else {
				r = rrotate(r);
				i = lrotate(i);
			}
		}
		return i;
	}
	int insert(int i, T& key, int cnt) {
		if (i == 0) {
			trcnt++;
			tr[trcnt].key = key;
			tr[trcnt].cnt = tr[trcnt].siz = cnt;
			tr[trcnt].height = 1;
			return trcnt;
		}
		if (tr[i].key == key) {
			tr[i].cnt += cnt;
			if(tr[i].cnt==0){
				return this->era(i,key);
			}
		}
		else if (key < tr[i].key) {
			tr[i].l = insert(tr[i].l, key, cnt);
		}
		else {
			tr[i].r = insert(tr[i].r, key, cnt);
		}
		up(i);
		return legal(i);
	}
	int id(int i, T& key) {
		if (i == 0)return -1;
		if (tr[i].key == key)return i;
		else if (key < tr[i].key)return id(tr[i].l, key);
		else return id(tr[i].r, key);
	}
	int era2(int i, int goal) {
		if (i == goal)return tr[i].r;
		tr[i].l = era2(tr[i].l, goal);
		up(i);
		return legal(i);
	}
	int era(int i, T& key) {
		if (tr[i].key == key) {
			if (tr[i].l == 0 && tr[i].r == 0)return 0;
			else if (tr[i].l == 0)return tr[i].r;
			else if (tr[i].r == 0)return tr[i].l;
			else {
				int cur = tr[i].r;
				while (tr[cur].l != 0)cur = tr[cur].l;
				tr[i].r = era2(tr[i].r, cur);
				tr[cur].l = tr[i].l;
				tr[cur].r = tr[i].r;
				i=cur;
			}
		}
		else if (tr[i].key < key)tr[i].r = era(tr[i].r, key);
		else tr[i].l = era(tr[i].l, key);
		up(i);
		return legal(i);
	}
	T k_th(int i, int k) {
		int lsiz = tr[tr[i].l].siz;
		if (k <= lsiz)return k_th(tr[i].l, k);
		else if (k > lsiz && k <= lsiz + tr[i].cnt)return tr[i].key;
		else return k_th(tr[i].r, k - lsiz - tr[i].cnt);
	}
	T pre(int i, T& key) {
		if (i == 0)return MIN;
		if (tr[i].key >= key)return pre(tr[i].l, key);
		else return max(tr[i].key, pre(tr[i].r, key));
	}
	T nex(int i, T& key) {
		if (i == 0)return MAX;
		if (tr[i].key <= key)return nex(tr[i].r, key);
		else return min(tr[i].key, nex(tr[i].l, key));
	}
	int rank(int i, T& key) {
		if (i == 0)return 0;
		if(tr[i].key<key){
			return tr[tr[i].l].siz+tr[i].cnt+rank(tr[i].r,key);
		}
		else if(tr[i].key==key){
			return tr[tr[i].l].siz;
		}
		else return rank(tr[i].l, key);
	}
public:
	avl(int N=1e6):trcnt(0),head(0){
		tr=new tree[N+1];
		for(int i=0;i<N;i++)tr[i]={0,0,0,0,0,0};
	}
	~avl(){
		delete[]tr;
	}
	void add(T& key) {
		head = insert(head, key, 1);
	}
	void add(T& key, int cnt) {
		head = insert(head, key, cnt);
	}
	void del(T&key){
		head=insert(head,key,-1);
	}
	void del(T&key,int cnt){
		head=insert(head,key,-cnt);
	}
	bool find(T& key) {
		return id(head, key) != -1;
	}
	void erase(T& key) {
		if (id(head, key) != -1) {
			head = era(head, key);
		}
	}
	int size() {
		return tr[head].siz;
	}
	T k_th(int k) {
		if (k<1 || k>this->size())return illegal;
		return k_th(head, k);
	}
	T pre(T& key) {
		return pre(head, key);
	}
	T nex(T& key) {
		return nex(head, key);
	}
	int rank(T& key) {
		return rank(head,key)+1;
	}
};
```

