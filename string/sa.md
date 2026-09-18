# 后缀数组（Suffix Array）

## 基本定义

后缀数组主要关系到两个数组：`sa` 和 `rk`。

- `sa[i]`：将所有后缀按字典序排序后，第 $i$ 小后缀的起始位置。它就是后缀数组，也称**编号数组**。
- `rk[i]`：以位置 $i$ 开始的后缀在所有后缀中的排名。它是重要的辅助数组，也称**排名数组**。

两者互为反函数，满足：

$$
sa[rk[i]] = rk[sa[i]] = i
$$

### 示例

后缀数组示例：

[![后缀数组示例](https://oiwiki.org/string/images/sa1.png)](https://github.com/OI-wiki/libs/blob/master/%E9%9B%86%E8%AE%AD%E9%98%9F%E5%8E%86%E5%B9%B4%E8%AE%BA%E6%96%87/%E5%9B%BD%E5%AE%B6%E9%9B%86%E8%AE%AD%E9%98%9F2009%E8%AE%BA%E6%96%87%E9%9B%86/11.%E7%BD%97%E7%A9%97%E9%AA%9E%E3%80%8A%E5%90%8E%E7%BC%80%E6%95%B0%E7%BB%84%E2%80%94%E2%80%94%E5%A4%84%E7%90%86%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E6%9C%89%E5%8A%9B%E5%B7%A5%E5%85%B7%E3%80%8B/%E5%90%8E%E7%BC%80%E6%95%B0%E7%BB%84%E2%80%94%E2%80%94%E5%A4%84%E7%90%86%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E6%9C%89%E5%8A%9B%E5%B7%A5%E5%85%B7.pdf)

## `height` 数组

### LCP（最长公共前缀）

两个字符串 $S$ 和 $T$ 的 LCP（Longest Common Prefix）是满足下列条件的最大 $x$：

$$
x \leq \min(|S|, |T|), \qquad S_i = T_i \quad (\forall\ 1 \leq i \leq x)
$$

下文用 $lcp(i, j)$ 表示后缀 $i$ 和后缀 $j$ 的最长公共前缀长度。

### 定义

$$
height[i] = lcp(sa[i], sa[i - 1])
$$

即：排名第 $i$ 的后缀与排名第 $i - 1$ 的后缀的最长公共前缀长度。

通常将 $height[1]$ 视为 $0$。

### 线性求解的引理

$$
height[rk[i]] \geq height[rk[i - 1]] - 1
$$
