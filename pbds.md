```c++
#include <bits/stdc++.h>       // C++ STL 万能头
#include <bits/extc++.h>       // PBDS 万能头
using namespace std;
using namespace __gnu_pbds;

// ✅ 有序集合（支持 order_of_key / find_by_order）
template<typename T>
using ordered_set = tree<
    T,
    null_type,
    less<T>,              // 可改为 greater<T> 实现降序
    rb_tree_tag,
    tree_order_statistics_node_update
>;

// ✅ 有序映射（带键值对 + 有序操作）
template<typename K, typename V>
using ordered_map = tree<
    K,
    V,
    less<K>,
    rb_tree_tag,
    tree_order_statistics_node_update
>;

// ✅ 快速哈希表（unordered_map 替代，更快防卡）
template<typename K, typename V>
using hash_map = gp_hash_table<K, V>;

// ✅ 抗卡哈自定义哈希（用于 hash_map）
struct custom_hash {
    static uint64_t splitmix64(uint64_t x) {
        x += 0x9e3779b97f4a7c15;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9;
        x = (x ^ (x >> 27)) * 0x94d049bb133111eb;
        return x ^ (x >> 31);
    }
    size_t operator()(uint64_t x) const {
        static const uint64_t FIXED_RANDOM = chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};

```



**ordered_set**

```c++
ordered_set<int> s;
s.insert(5);
s.insert(10);
cout << *s.find_by_order(1) << "\n"; // 输出10
cout << s.order_of_key(7) << "\n";   // 输出1（5 < 7）

```

**ordered_map**

```c++
ordered_map<int, string> mp;
mp[3] = "apple";
mp[1] = "banana";
auto it = mp.find_by_order(0); // 第0小key
cout << it->first << " = " << it->second << "\n"; // 1 = banana

```

 

**hash_map 示例（默认哈希）**

```c++
hash_map<int, int> cnt;
cnt[1000000000] = 123;
cout << cnt[1000000000] << "\n";

```

**hash_map 示例（抗卡哈版本）**

```c++
gp_hash_table<long long, int, custom_hash> mp;
mp[123456789123] = 1;

```



## pbds中set和map比起stl中多的功能



## ✅ 一、`*s.find_by_order(k)` —— 找第 k小的元素（从 0 开始）

### 函数原型

```
cpp

iterator find_by_order(int k);
```

### 功能

返回有序集合中**排名为第 `k` 小的元素的迭代器**。

- 返回值是迭代器，所以用 `*s.find_by_order(k)` 取值。
- **`k` 从 0 开始**。

### 示例

```
ordered_set<int> s = {10, 20, 30, 40};

cout << *s.find_by_order(0) << "\n";  // 10
cout << *s.find_by_order(2) << "\n";  // 30
cout << *s.find_by_order(3) << "\n";  // 40
```

### 注意事项

- 如果 `k >= s.size()`，则返回 `s.end()`（非法访问要小心）。
- 元素是自动升序排序的（less）

------

## ✅ 二、`s.order_of_key(x)` —— 找小于 `x` 的元素个数（也就是 `x` 的排名）

### 函数原型

```
cpp


int order_of_key(const T& x);
```

### 功能

返回集合中**严格小于 `x` 的元素个数**。

即 `x` 的排名是第几个（从 0 开始）。

### 示例

```
ordered_set<int> s = {10, 20, 30, 40};

cout << s.order_of_key(10) << "\n";  // 0 （<10的没有）
cout << s.order_of_key(25) << "\n";  // 2 （10, 20 < 25）
cout << s.order_of_key(40) << "\n";  // 3 （10,20,30 < 40）
```

------

## ✅ 三、区间个数查询（重点）

### 求 `[L, R]` 闭区间内元素个数：

```
cpp

s.order_of_key(R + 1) - s.order_of_key(L)
```

### 为什么这样写？

- `order_of_key(R + 1)`：返回小于等于 R 的个数（因为是 `< R+1`）
- `order_of_key(L)`：返回小于 L 的个数
- 相减就是在 `[L, R]` 中的个数

------

### 示例

```
ordered_set<int> s = {5, 10, 15, 20, 25, 30};

// 查询 [10, 25] 中有几个元素
int ans = s.order_of_key(26) - s.order_of_key(10);  // = 5 - 1 = 4
cout << ans << "\n";  // 输出 4，对应：10,15,20,25
```