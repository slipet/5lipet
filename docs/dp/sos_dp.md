# SOS DP (Sum over Subsets)

* 對每個 mask 求所有子集的和: $f[mask] = \sum_{i \subseteq mask} a[i]$
* 樸素作法: 枚舉子集，$O(3^n)$
* SOS DP: $O(n 2^n)$

### 想法

把 mask 看成 n 維、每維長度為 2 的高維陣列，子集和就是高維前綴和，一次處理一個維度(bit):

* 枚舉第 i 個 bit，若 mask 含有第 i 個 bit，就把「拿掉第 i 個 bit」的結果加進來
* 處理完第 i 個 bit 後，$f[mask]$ 代表只在前 i 個 bit 上取子集的和
* <span style="color:red">bit 要放在外層迴圈，每個子集才只會被算到一次</span>

```cpp
// 子集和: f[mask] = sum of a[sub], sub ⊆ mask
vector<long long> f(a.begin(), a.end());
for (int i = 0; i < n; ++i)
    for (int mask = 0; mask < (1 << n); ++mask)
        if (mask >> i & 1)
            f[mask] += f[mask ^ (1 << i)];
```

### 變形

* 超集和: $g[mask] = \sum_{mask \subseteq i} a[i]$，條件改成 mask 沒有第 i 個 bit，從 $mask | (1 << i)$ 轉移
* 逆操作(由子集和還原原陣列): 把 `+=` 改成 `-=`
* 運算不一定要是加法，滿足結合律、交換律即可，例如 max、min、OR、計數

```cpp
// 超集和: g[mask] = sum of a[sup], mask ⊆ sup
vector<long long> g(a.begin(), a.end());
for (int i = 0; i < n; ++i)
    for (int mask = 0; mask < (1 << n); ++mask)
        if (!(mask >> i & 1))
            g[mask] += g[mask | (1 << i)];
```

練習:

* [CSES 1654. Bit Problem](https://cses.fi/problemset/task/1654)
* [CF 165E. Compatible Numbers](https://codeforces.com/problemset/problem/165/E)
