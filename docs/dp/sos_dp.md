# SOS DP (Sum over Subsets)/高維前綴和

把二進位數看成集合(第 i 位是 1 表示元素 i 在集合中)，對每個集合 S 求所有子集的和(或 max、min、OR ...):

$$f[S] = \sum_{T \subseteq S} a[T]$$

設 w 為 bit 數:

* 樸素作法: 對每個 S 枚舉子集，$O(3^w)$
* SOS DP: $O(w 2^w)$

### 為什麼不能直接拿掉一個元素轉移

直覺的做法是枚舉 S 中的元素 i，從 $S \setminus \{i\}$ 轉移，所有 $S \setminus \{i\}$ 的聯集就是 S 的所有真子集:

$$f[S] = \max(f[S],\ \max_{i \in S} f[S \setminus \{i\}])$$

* 求 max/min 時正確，因為重複算到同一個子集不影響結果
* <span style="color:red">求和時會重複計算</span>: $S = \{1, 2, 3\}$，拿掉 1 得 $\{2, 3\}$，拿掉 2 得 $\{1, 3\}$，兩者都有子集 $\{3\}$，3 會被加兩次

### 正確劃分子集

按照「是否包含最大的元素」把子集分成不重疊的兩類，例如 $S = \{1, 3, 4\}$:

* 包含 4: $\{4\}, \{1, 4\}, \{3, 4\}, \{1, 3, 4\}$
* 不含 4: $\varnothing, \{1\}, \{3\}, \{1, 3\}$

一次只處理一個元素 i，就能保證每個子集只被分到其中一類。

定義 $f[i][S]$: S 中大於 i 的元素必須保留，只有小於等於 i 的元素可以選或不選，這些子集的 op 結果。

$$
f[i][S] =
\begin{cases}
f[i-1][S], & i \notin S \\
f[i-1][S] \text{ op } f[i-1][S \setminus \{i\}], & i \in S
\end{cases}
$$

* 初始值: $f[-1][S] = a[S]$
* 答案: $f[w-1][S]$
* 第一維可以滾動掉，直接原地更新: 第 i 輪只會改「含 i」的狀態，讀的 $f[S \setminus \{i\}]$「不含 i」，這一輪不會被改到，所以 S 由小到大或由大到小枚舉都可以

### 模板

* 把 a 中的每個數看成集合，w 為 a[i] 的二進位最大長度
* 時間複雜度 $O(n + U \log U)$，其中 $U = \max(a)$
* 優化: `s |= 1 << i` 直接跳到第 i 位是 1 的 s，省去判斷

<span style="color:red">迴圈順序不能對調: 外層枚舉 bit、內層枚舉 S，每個子集只會被算到一次；若外層枚舉 S、內層枚舉 bit，就變回「拿掉一個元素」的轉移，同一個子集會從多條路徑重複計算(求 max/min 不影響，求和會錯)。</span>

從子集轉移過來: $f[S]$ 表示 a 中是 S 的**子集**的元素個數

```cpp
vector<int> sos_dp(vector<int>& a) {
    int mx = ranges::max(a);
    int w = bit_width((uint32_t) mx); // 二進位長度上限

    vector<int> f(1 << w);
    for (int x : a) {
        f[x]++; // 初始值
    }

    for (int i = 0; i < w; i++) {
        for (int s = 0; s < (1 << w); s++) {
            s |= 1 << i; // 優化: 快速跳到 i 位是 1 的 s
            f[s] += f[s ^ (1 << i)];
        }
    }
    return f;
}
```

從超集轉移過來: $f[S]$ 表示 a 中是 S 的**超集**的元素個數

```cpp
vector<int> sos_dp(vector<int>& a) {
    int mx = ranges::max(a);
    int w = bit_width((uint32_t) mx); // 二進位長度上限

    vector<int> f(1 << w);
    for (int x : a) {
        f[x]++; // 初始值
    }

    for (int i = 0; i < w; i++) {
        for (int s = 0; s < (1 << w); s++) {
            s |= 1 << i; // 優化: 快速跳到 i 位是 1 的 s
            f[s ^ (1 << i)] += f[s];
        }
    }
    return f;
}
```

<span style="color:red">兩個模板只差在最後一行的轉移方向: 子集是 `f[s] += f[s ^ bit]`(不含 i 的流向含 i 的)，超集是 `f[s ^ bit] += f[s]`(含 i 的流向不含 i 的)。</span>

### 變形

* 初始值不一定是個數，也可以是 `f[x] += x`(求和)、`f[x] = x`(求最大值)、`f[x] = true`(存在性)
* 運算不一定要是加法，滿足結合律、交換律即可，例如 max、min、OR
* 逆操作(由子集和還原原陣列): 把 `+=` 改成 `-=`，只有加法這種可以減回去的運算才行

### 應用

#### AND 為 0 → 補集的子集

把每個數看成一個集合，兩個數 AND 為 0 等價於兩個集合交集為空:

$$A \cap B = \varnothing \iff B \subseteq \complement_U A$$

所以「找與 x 沒有共同 bit 的數」就是「在 x 補集的子集中找」，補集用 $(2^w - 1) \oplus x$ 計算。

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

想法:

* 只要有一個 x 使 $y \,\&\, x = 0$，y 就不合法，而 $y \,\&\, x = 0 \iff x \subseteq \complement_U y$
* 所以 y 合法 $\iff$「$\complement_U y$ 的子集中沒有任何 x」$\iff f[\complement_U y] = \text{false}$
* 以上面的 $X = \{011, 100\}$ 為例，$f[s] = 0$ 的 s 為 000、001、010，對應合法的 y 為 111、110、101
* $\text{popcount}(y) = w - \text{popcount}(s)$，取 $f[s] = 0$ 中 1 最多的 s，答案為 $3 - 1 = 2$

練習:

* [Leetcode 3670. 没有公共位的整数最大乘积](https://leetcode.cn/problems/maximum-product-of-two-integers-with-no-common-bits/)
* [Leetcode 4119. 有效整数中最少置位数目](https://leetcode.cn/problems/minimum-set-bits-in-a-valid-integer/)
* [CSES 1654. Bit Problem](https://cses.fi/problemset/task/1654)
* [CF 165E. Compatible Numbers](https://codeforces.com/problemset/problem/165/E)

參考:

* [两种方法：普通状压 DP / 高维前缀和（SOS DP）](https://leetcode.cn/problems/maximum-product-of-two-integers-with-no-common-bits/solutions/3768219/mo-ban-gao-wei-qian-zhui-he-sos-dppython-78fz/)
* [4119 滑动窗口 + SOS DP](https://leetcode.cn/problems/minimum-set-bits-in-a-valid-integer/solutions/4059551/hua-dong-chuang-kou-sos-dppythonjavacgo-zzbi4/)
