# Enumerate Skills

這裡放一些枚舉技巧

### BIT

#### 子集枚舉

```cpp
for(int sub = s; sub; sub = (sub - 1 & s)) {
		....
} 
//including 0
sub = s;
do {
	sub = (sub - 1 & s);
} while(sub != s);
```

#### low bit

```cpp
low bit = x & -x 
```

* 移除 low bit

```cpp
s = s & (s - 1)
s -= (s & -s)
```

#### including
```cpp
a & b = a //0101 & 1101 = 0101
a | b = b //0101 | 1101 = 1101
```

#### Gosper's Hack

枚舉大小為k的子集

```cpp
void GospersHack(int k, int n)
{
    int cur = (1 << k) - 1;
    int limit = (1 << n);
    while (cur < limit)
    {
        // do something
        int lb = cur & -cur;
        int r = cur + lb;
        cur = ((r ^ cur) >> __builtin_ctz(lb) + 2) | r;
        // 或：cur = (((r ^ cur) >> 2) / lb) | r;
    }
}
//  x     = 0101110
// -x     = 1010010
// lb     = 0000010
//  x     = 0101110
// x + lb = 0110000 -> left
// left^x = 0011110 >> (1 + 2 = 3)
//        = 0000011
//      r = 0110000  

//  x     = [01]110
// -x     = 10010
// lb     = 00010
// x + lb = [10]000
// left ^ x = 11110 >> (ctz(lb) >> 2)
```

### 枚舉對角線

**對角線**

對於每條對角線上的網格 $(r,c)$ 其 $r - c$ 為一定值。

設 k = r - c + n，則可以知道右上角的對角線為 $k = 0 - (n - 1) + n = 1$，而左下角的對角線為 $k = m - 1 - 0 + n = m + n - 1$

透過枚舉 $k = 1, 2, ... + m + n - 1$ 可以枚舉每條對角線。

因此可以知道透過 $r \in [0, m - 1]$ 的極值得到 $c$ 的上下限:

\[
\begin{array}{ll}
    k = 1, 2, ... + m + n - 1\\
    \min_c = max(n - k, 0) \\
    \max_c = min(m + n + 1 - k, n - 1) \\
    r = k + c - n
\end{array}
\]

```cpp
// k = r - c + n
for(int k = 1; k < m + n; ++k) {
    int mnc = max(n - k, 0);
    int mxc = min(m + n + 1 - k, n - 1);
    for(int c = mnc; c <= mxc; ++c) {
        int r = k + c - n;
    }
}
```

**反對角線**

同上設 $k = r + c + 1, k = 1, 2, ...., m + n - 1$

透過 $r \in [0, m - 1]$ 的極值得到 $c$ 的上下限:

\[
\begin{array}{ll}
    k = 1, 2, ... + m + n - 1\\
    \min_c = max(k - m, 0) \\
    \max_c = min(k - 1, n - 1) \\
    r = k - c - 1
\end{array}
\]

```cpp
// k = r + c + 1
for(int k = 1; k < m + n; ++k) {
    int mnc = max(k - m, 0);
    int mxc = min(k - 1, n - 1);
    for(int c = mnc; c <= mxc; ++c) {
        int r = k - c - 1;
    }
}
```

### 求移動絕對值貢獻

給定一個長為 n 的序列 $A = a_1, a_2, ..., a_n$，求 $1 \le j \le n$ 的最大貢獻，貢獻的計算方式為:

$$ G = max_{j = 1}^{n}(gain_j) = max_{j = 1}^{n}max_{i = 1}^{n}(a_i + |j - i|)$$

暴力計算需要 $O(n^2)$，因此先將絕對值消掉想辦法找規律，並專注在某個位置 $j$ 上:

\[
\begin{array}{ll}
    gain_j = max_{i = 1}^{n}{(a_i - (j - i))}, \text{ for } i \le j\\
    gain_j = max_{i = 1}^{n}{(a_i - (i - j))}, \text{ for } i > j
\end{array}
\]

把與 $i$ 的無關項 $j$ 提出來:

\[
\begin{array}{ll}
    gain_j = - j + max_{i = 1}^{n}{(a_i + i)}, \text{ for } i \le j\\
    gain_j = j+ max_{i = 1}^{n}{(a_i - i)}, \text{ for } i > j
\end{array}
\]

可以發現此時 $max$ 內的項只會與 $i$ 有關，接著透過前後綴分解可以得到最大值進行計算，將複雜度降到 $O(n)$

* [leetcode 1937. 扣分后的最大得分](https://leetcode.cn/problems/maximum-number-of-points-with-cost/description/)

### 把某些元素分到兩個集合

1. 枚舉其中一個集合的元素 $k = 0, 1, 2, .. , n$ ，則另一個集合則是 $n - k = n, n - 1, ... , 1, 0$。
    
    接著再利用 k 去計算符合題目限制的要求。


### 原地去重（相同或值只保留最左边的）

```cpp
// 原理见力扣 26. 删除有序数组中的重复项
int m = 1;
for (int j = 1; j < or_left.size(); j++) {
    if (or_left[j].first != or_left[j - 1].first) {
        or_left[m++] = or_left[j];
    }
}
```

### Abel 求和

\[
\begin{array}{ll}
    \sum_{n}^{i = m}a_ib_i &=a_nS_n - \sum_{i = m}^{n-1}(a_{i + 1} - a_i)S_i\\
    S_i &= \sum_{k = m}^{i} b_k
\end{array}
\]

抽象化的表示:

```
w 1      2       3      4
i 1 || 2 3 || 4 5 6 || 7 8
  - || - - || - - - || - -
    || - - || - - - || - -
    ||     || - - - || - -
    ||     ||       || - -
```

透過計算**倍率變化**的前綴和，

### 逆向思維/正難則反

#### 背包求 $\ge k$

背包求 $\ge k$ 不一定很好得到，透過 $all - (< k)$ 可以間接得到。

* [3685. 含上限元素的子序列和](https://leetcode.cn/problems/number-of-great-partitions/solutions/2032009/ni-xiang-si-wei-01-bei-bao-fang-an-shu-p-v47x/)

* [3333. 找到初始输入字符串 II](https://leetcode.cn/problems/find-the-original-typed-string-ii/solutions/2966856/zheng-nan-ze-fan-qian-zhui-he-you-hua-dp-5mi9/)