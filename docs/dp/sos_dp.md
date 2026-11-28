# SOS DP (Sum over Subsets)/高維前綴和

* 對每個集合 S 求所有子集的和(或 max、min ...): $f[S] = \sum_{T \subseteq S} a[T]$
* 樸素作法: 枚舉子集，$O(3^n)$
* SOS DP: $O(n 2^n)$

### 從位運算到集合

把每個數看成一個集合，兩個數 AND 為 0 等價於兩個集合交集為空:

$$A \cap B = \varnothing \iff B \subseteq \complement_U A$$

所以「找與 x 沒有共同 bit 的數」就是「在 x 補集的子集中找」，這正是 SOS DP 在處理的問題。

### 為什麼不能直接拿掉一個元素轉移

直覺的做法是枚舉 S 中的元素 i，從 $S \setminus \{i\}$ 轉移，所有 $S \setminus \{i\}$ 的聯集就是 S 的所有真子集:

$$f[S] = \max(f[S],\ \max_{i \in S} f[S \setminus \{i\}])$$

* 求 max/min 時正確，因為重複算到同一個子集不影響結果
* <span style="color:red">求和時會重複計算</span>: $S = \{1, 2, 3\}$，拿掉 1 得 $\{2, 3\}$，拿掉 2 得 $\{1, 3\}$，兩者都有子集 $\{3\}$，3 會被加兩次

### 正確劃分子集

按照「是否包含最大的元素」把子集分成不重疊的兩類，例如 $S = \{1, 3, 4\}$:

* 包含 4: $\{4\}, \{1, 4\}, \{3, 4\}, \{1, 3, 4\}$
* 不含 4: $\varnothing, \{1\}, \{3\}, \{1, 3\}$

定義 $f[i][S]$: S 中大於 i 的元素必須保留，只有小於等於 i 的元素可以選或不選，所得到所有子集的結果。

$$
f[i][S] =
\begin{cases}
f[i-1][S], & i \notin S \\
f[i-1][S] \text{ op } f[i-1][S \setminus \{i\}], & i \in S
\end{cases}
$$

* 初始值: $f[-1][S] = a[S]$
* 答案: $f[w-1][S]$，w 是 bit 數
* 第一維可以滾動掉: 計算 $f[i][S]$ 時只會讀 $f[i-1][S \setminus \{i\}]$，而不含 i 的狀態在這一輪不會被改到，所以 S 由小到大或由大到小枚舉都可以

<span style="color:red">兩種寫法只差在先枚舉 S 還是先枚舉 bit，但意義完全不同: 先枚舉 S 的轉移會重疊，先枚舉 bit 才不會重複計算。</span>可以類比 [377. 组合总和 Ⅳ](https://leetcode.cn/problems/combination-sum-iv/)(排列) 和 [518. 零钱兑换 II](https://leetcode.cn/problems/coin-change-ii/)(組合) 的差別。

```cpp
// 子集和: f[mask] = sum of a[sub], sub ⊆ mask
vector<long long> f(a.begin(), a.end());
for (int i = 0; i < n; ++i)
    for (int mask = 0; mask < (1 << n); ++mask)
        if (mask >> i & 1)
            f[mask] += f[mask ^ (1 << i)];
```

小優化: 把 mask 或上第 i 個 bit，直接跳到第 i 位是 1 的 mask，省去判斷

```cpp
for (int i = 0; i < n; ++i) {
    int bit = 1 << i;
    for (int mask = 0; mask < (1 << n); ++mask) {
        mask |= bit;
        f[mask] += f[mask ^ bit];
    }
}
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

### 應用

#### s 的子集中有沒有某個 x

給定一堆數 X，對每個 s 問: <span style="color:red">s 的子集中有沒有某個 x?</span>

$$f[s] = \text{true} \iff \exists\, x \in X,\ x \subseteq s$$

做法: 每個 x 設 $f[x] = \text{true}$，再做 SOS DP，op 取 OR。

例: $X = \{011, 100\}$

| s | 000 | 001 | 010 | 011 | 100 | 101 | 110 | 111 |
|---|---|---|---|---|---|---|---|---|
| f[s] | 0 | 0 | 0 | 1 | 1 | 1 | 1 | 1 |

* $f[101] = 1$: 101 的子集中有 100
* $f[010] = 0$: 010 的子集只有 000、010，都不在 X 中

##### 用來判斷不合法

[Leetcode 4119. 有效整数中最少置位数目](https://leetcode.cn/problems/minimum-set-bits-in-a-valid-integer/)

題目關鍵: y 要和每個 x 都有共同的 bit($y \,\&\, x \ne 0$)，求 y 最少有幾個 1。

應用:

* 只要有一個 x 使 $y \,\&\, x = 0$，y 就不合法，而 $y \,\&\, x = 0 \iff x \subseteq \complement_U y$
* 所以 y 合法 $\iff$「$\complement_U y$ 的子集中沒有任何 x」$\iff f[\complement_U y] = \text{false}$
* 上例中 $f[s] = 0$ 的 s 為 000、001、010，對應合法的 y 為 111、110、101
* $\text{popcount}(y) = w - \text{popcount}(s)$，取 $f[s] = 0$ 中 1 最多的 s，答案為 $3 - 1 = 2$

練習:

* [Leetcode 3670. 没有公共位的整数最大乘积](https://leetcode.cn/problems/maximum-product-of-two-integers-with-no-common-bits/)
* [Leetcode 4119. 有效整数中最少置位数目](https://leetcode.cn/problems/minimum-set-bits-in-a-valid-integer/)
* [CSES 1654. Bit Problem](https://cses.fi/problemset/task/1654)
* [CF 165E. Compatible Numbers](https://codeforces.com/problemset/problem/165/E)

參考:

* [两种方法：普通状压 DP / 高维前缀和（SOS DP）](https://leetcode.cn/problems/maximum-product-of-two-integers-with-no-common-bits/solutions/3768219/mo-ban-gao-wei-qian-zhui-he-sos-dppython-78fz/)
* [4119 滑动窗口 + SOS DP](https://leetcode.cn/problems/minimum-set-bits-in-a-valid-integer/solutions/4059551/hua-dong-chuang-kou-sos-dppythonjavacgo-zzbi4/)
