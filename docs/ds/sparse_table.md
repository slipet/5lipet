# Sparse Table

[OI-wiki](https://oi-wiki.org/ds/sparse-table/)

* ST 表（Sparse Table，稀疏表）是用於解決<span style="color:red"> 可重複貢獻問題 </span>的資料結構。

    * 可重複貢獻問題 是指對於運算 opt，滿足 $x \text{ opt⁡ } x =x$，則對應的區間詢問就是一個可重複貢獻問題。例如: 對於最大值有 $max(x, x) = x$，gcd 有 $gcd(x, x) = x$。

    * opt 還必須滿足<span style="color:red">結合律</span>才能使用 ST 表求解。

* 可重複貢獻問題:
    * RMQ (Range Maximum/Minimum Query)
    * GCD
    * 區間按位 AND/OR

* <span style="color:red">區間和</span>不是重複貢獻問題，如果求區間和的時候採用的預處理區間重疊了，則會導致重疊部分被計算兩次。

* <span style="color:red">不支持修改</span>

ST 表基於"倍增"的思想，預處理 $O(n\log{n})$，回答詢問 $O(1)$。

定義 $f(i, j)$ 為區間 $[i, i + 2^{j} - 1]$ 的最大值

顯然 $f(i, 0) = a_i$。

* 預處理

    由上面的定義可以知道每次都會跳 $2^{j} - 1$，且會有轉移方程 :
    
    $$f(i, j) = max(f(i, j - 1), f(i + 2^{j - 1}, j - 1))$$

* 單點查詢

    對於詢問 [l, r]，可以將其分成兩個區間 $[l, l + 2^{s} - 1]$ 和 $[r - (2^{s} - 1), r]$，而 $s = \log_2{(r - l + 1)}$ ，兩個區間取最大值。

    由這裡可以看出重複貢獻問題的性質，重疊區間不會對答案產生影響，但若是區間和則會重複計算，利用兩個區間完全覆蓋 $[l, r]$，以確保正確性。

```cpp
//ref. https://leetcode.cn/circle/discuss/mOr1u6/
class SparseTable {
    vector<vector<int>> st_min;
    vector<vector<int>> st_max;

public:
    // 时间复杂度 O(n * log n)
    SparseTable(const vector<int>& nums) {
        int n = nums.size();
        int w = bit_width((uint32_t) n);
        st_min.resize(w, vector<int>(n));
        st_max.resize(w, vector<int>(n));

        for (int j = 0; j < n; j++) {
            st_min[0][j] = nums[j];
            st_max[0][j] = nums[j];
        }

        for (int i = 1; i < w; i++) {
            for (int j = 0; j + (1 << i) <= n; j++) {
                st_min[i][j] = min(st_min[i - 1][j], st_min[i - 1][j + (1 << (i - 1))]);
                st_max[i][j] = max(st_max[i - 1][j], st_max[i - 1][j + (1 << (i - 1))]);
            }
        }
    }

    // [l, r) 左闭右开，下标从 0 开始
    // 必须保证 l < r
    // 时间复杂度 O(1)
    int query_min(int l, int r) const {
        int k = bit_width((uint32_t) r - l) - 1;
        return min(st_min[k][l], st_min[k][r - (1 << k)]);
    }

    // [l, r) 左闭右开，下标从 0 开始
    // 必须保证 l < r
    // 时间复杂度 O(1)
    int query_max(int l, int r) const {
        int k = bit_width((uint32_t) r - l) - 1;
        return max(st_max[k][l], st_max[k][r - (1 << k)]);
    }
};
```

* 預處理 ST 表時通常需要建立一個一維大小為 $\log{n}$，另一維大小為 $n$ 的數組，此時應優先讓大小為 $\log{n}$ 的維度作為第一維，以提升 cache locality。
