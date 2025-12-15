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
template<typename T,typename Compare=less<T>>
class avl {
private:
	int trcnt=0, head=0;
	Compare comp;
	struct tree {
		int l, r, height, cnt, siz;
		T key;
	}*tr;
	bool equal(const T& x, const T& y) const{
		return !comp(x, y) && !comp(y, x);
	}
	void up(int i) {
		if (i == 0)return;
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
	int insert(int i, const T& key, int cnt) {
		if (i == 0) {
			if (cnt <= 0)return 0;
			trcnt++;
			tr[trcnt].key = key;
			tr[trcnt].cnt = tr[trcnt].siz = cnt;
			tr[trcnt].height = 1;
			return trcnt;
		}
		if (equal(key,tr[i].key)) {
			tr[i].cnt += cnt;
			if (tr[i].cnt <= 0) {
				return this->era(i, key);
			}
		}
		else if (comp(key,tr[i].key)) {
			tr[i].l = insert(tr[i].l, key, cnt);
		}
		else {
			tr[i].r = insert(tr[i].r, key, cnt);
		}
		up(i);
		return legal(i);
	}
	int id(int i, const T& key) {
		if (i == 0)return -1;
		if (equal(key,tr[i].key))return i;
		else if (comp(key,tr[i].key))return id(tr[i].l, key);
		else return id(tr[i].r, key);
	}
	int era2(int i, int goal) {
		if (i == goal)return tr[i].r;
		tr[i].l = era2(tr[i].l, goal);
		up(i);
		return legal(i);
	}
	int era(int i, const T& key) {
		if (equal(tr[i].key,key)) {
			if (tr[i].l == 0 && tr[i].r == 0)return 0;
			else if (tr[i].l == 0)return tr[i].r;
			else if (tr[i].r == 0)return tr[i].l;
			else {
				int cur = tr[i].r;
				while (tr[cur].l != 0)cur = tr[cur].l;
				tr[i].r = era2(tr[i].r, cur);
				tr[cur].l = tr[i].l;
				tr[cur].r = tr[i].r;
				i = cur;
			}
		}
		else if (comp(tr[i].key , key))tr[i].r = era(tr[i].r, key);
		else tr[i].l = era(tr[i].l, key);
		up(i);
		return legal(i);
	}
	optional<T> k_th(int i, int k) {
		int lsiz = tr[tr[i].l].siz;
		if (k <= lsiz)return k_th(tr[i].l, k);
		else if (k > lsiz && k <= lsiz + tr[i].cnt)return tr[i].key;
		else return k_th(tr[i].r, k - lsiz - tr[i].cnt);
	}
	optional<T> pre(int i, const T& key) {
		if (i == 0)return nullopt;
		if (!comp(tr[i].key, key))return pre(tr[i].l, key);
		else {
			optional<T>an = tr[i].key,an2=pre(tr[i].r,key);
			if (an2 && comp(*an, *an2))swap(an, an2);
			return an;
		}
	}
	optional<T> nex(int i, const T& key) {
		if (i == 0)return nullopt;
		if (!comp(key,tr[i].key))return nex(tr[i].r, key);
		else {
			optional<T>an = tr[i].key, an2 = nex(tr[i].l, key);
			if (an2 && comp(*an2, *an))swap(an, an2);
			return an;
		}
	}
	int rank(int i, const T& key) {
		if (i == 0)return 0;
		if (comp(tr[i].key,key)) {
			return tr[tr[i].l].siz + tr[i].cnt + rank(tr[i].r, key);
		}
		else if (equal(tr[i].key, key)) {
			return tr[tr[i].l].siz;
		}
		else return rank(tr[i].l, key);
	}
public:
	avl(int N = 1e6,Compare c=Compare()) :trcnt(0), head(0),comp(c) {
		tr = new tree[N + 1];
		for (int i = 0; i <= N; i++)tr[i] = { 0,0,0,0,0,0 };
	}
	~avl() {
		delete[]tr;
	}
	void add(const T& key) {
		head = insert(head, key, 1);
	}
	void add(const T& key, int cnt) {
		head = insert(head, key, cnt);
	}
	void del(const T& key) {
		head = insert(head, key, -1);
	}
	void del(const T& key, int cnt) {
		head = insert(head, key, -cnt);
	}
	bool find(const T& key) {
		return id(head, key) != -1;
	}
	void erase(const T& key) {
		if (id(head, key) != -1) {
			head = era(head, key);
		}
	}
	int size() {
		return tr[head].siz;
	}
	optional<T> k_th(int k) {
		if (k<1 || k>this->size())return nullopt;
		return k_th(head, k);
	}
	optional<T> pre(const T& key) {
		return pre(head, key);
	}
	optional<T> nex(const T& key) {
		return nex(head, key);
	}
	int rank(const T& key) {
		return rank(head, key) + 1;
	}
};
```





### 性质

avl树无论左旋还是右旋，他的中序遍历序列都不会改变

**一个用途：在一个序列中动态的给你一个x,y在位置x插入y，最后将avl树整体中序遍历一遍得到的就是整个序列了**

