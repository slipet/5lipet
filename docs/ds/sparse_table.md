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
    vector<vector<int>> st;

public:
    // 时间复杂度 O(n * log n)
    inline int op(const int &a, const int &b) const {
        return max(a, b);
    }
    SparseTable(const vector<int>& nums) {
        int n = nums.size();
        int w = bit_width((uint32_t) n);
        st.resize(w, vector<int>(n));

        for (int j = 0; j < n; j++) {
            st[0][j] = nums[j];
        }

        for (int i = 1; i < w; i++) {
            for (int j = 0; j + (1 << i) <= n; j++) {
                st[i][j] = op(st_min[i - 1][j], st_min[i - 1][j + (1 << (i - 1))]);
            }
        }
    }

    // [l, r) 左闭右开，下标从 0 开始
    // 必须保证 l < r
    // 时间复杂度 O(1)
    int query(int l, int r) const {
        int k = bit_width((uint32_t) r - l) - 1;
        return op(st_min[k][l], st_min[k][r - (1 << k)]);
    }
};
```

* 預處理 ST 表時通常需要建立一個一維大小為 $\log{n}$，另一維大小為 $n$ 的數組，此時應優先讓大小為 $\log{n}$ 的維度作為第一維，以提升 cache locality。

## 二維 ST 表

[講解](https://blog.nowcoder.net/n/3eccd1386a8846398d3bee62b485309b)

* [3933. 矩阵中的局部最大值 II](https://leetcode.cn/problems/largest-local-values-in-a-matrix-ii/solutions/3969719/mo-ban-er-wei-st-biao-pythonjavacgo-by-e-hw5t/)

```cpp
using vec4D = vector<vector<vector<vector<int>>>>;
class SparseTable2D {
    vec4D st;
    int m, n;
public:
    // 时间复杂度 O(n * log n)
    inline int op(const int &a, const int &b) const {
        return max(a, b);
    }
    inline int op_range(const int &a, const int &b, const int &c, const int &d) const {
        return max({a, b, c, d});
    }
    SparseTable2D(const vector<vector<int>>& matrix) {
        m = matrix.size(), n = matrix[0].size();
        int wm = bit_width((uint32_t) m), wn = bit_width((uint32_t) n);
        
        st = vector(wm, vector(wn, vector(m, vector<int>(n))));

        st[0][0] = matrix;
        
        for (int kc = 1; kc < wn; ++kc) {
            for (int r = 0; r < m; ++r) {
                for (int c = 0; c + (1 << kc) <= n; ++c) {
                    st[0][kc][r][c] = op(st[0][kc - 1][r][c], st[0][kc - 1][r][c + (1 << (kc - 1))]);
                }
            }
        }
        for (int kr = 1; kr < wm; ++kr) {
            for (int kc = 0; kc < wn; ++kc) {
                for (int r = 0; r + (1 << kr) <= m; ++r) {
                    for (int c = 0; c + (1 << kc) <= n; ++c) {
                        st[kr][kc][r][c] = op(st[kr - 1][kc][r][c], st[kr - 1][kc][r + (1 << (kr - 1))][c]);
                    }
                }
            }
        }
    }


    // 返回子矩阵最大值
    // 傳入閉區間 [r0, r1], [c0, c1]
    int query(int r0, int c0, int r1, int c1) const {
        r0 = max(0, r0);
        c0 = max(0, c0);
        r1 = min(m - 1, r1);
        c1 = min(n - 1, c1);
        r1++, c1++;//使用閉區間
        int kr = bit_width(1u * (r1 - r0)) - 1;
        int kc = bit_width(1u * (c1 - c0)) - 1;
        
        return op_range(
            st[kr][kc][r0][c0], //左上 
            st[kr][kc][r1 - (1 << kr)][c0], //右上
            st[kr][kc][r0][c1 - (1 << kc)], //左下
            st[kr][kc][r1 - (1 << kr)][c1 - (1 << kc)] //右下
        );
    }
};
```
