# Fenwick Tree/Binary Indexed Tree

	
區間拆分是將 $[1, n]$ 中的下標 $i$ 做二進制分解，使用不同的關鍵區間維護前綴資訊，ex: $13 \rightarrow 1101 \rightarrow 8 + 4 + 1$，下標 $i$ 表示維護每個關鍵區間的區間和/最大/最小值

* 取 lowbit = $i \& -i$
```
i = 10010
-i = (~i) + 1 = 01101 + 1 = 01110
i & -i = 00010
```

* query
要往後加lowbit直到超過陣列大小: $i += (i \& -i)$
	
* update
要往前更新 = 減去lowbit: $i -= (i \& -i)$
i &= (i - 1)
相當於 i = 10010
i - 1 = 10001
i &= (i - 1) = 10000

```cpp
// 模板来源 https://leetcode.cn/circle/discuss/mOr1u6/
// 根据题目用 FenwickTree<int> t(n) 或者 FenwickTree<long long> t(n) 初始化

#define bit_idx(i) (i + 1)

template<typename T>
class FenwickTree {
    vector<T> tree;

public:
    // input range [0, n - 1]，size = n
    // 使用下标 1 到 n
    FenwickTree(int n) : tree(n + 1) {}

    // a[i] 增加 val
    // 1 <= i <= n
    // 时间复杂度 O(log n)
    void update(int i, T val) {
        for (; i < tree.size(); i += i & -i) {
            tree[i] += val;
        }
    }

    // 求前缀和 a[1] + ... + a[i]
    // 1 <= i <= n
    // 时间复杂度 O(log n)
    T pre(int i) const {
        T res = 0;
        for (; i > 0; i -= (i & -i)) {
            res += tree[i];
        }
        return res;
    }
    // 求區間
    // 0 <= l <= r <= n - 1
    // 时间复杂度 O(log n)
    //usage: [l, r] -> query(l, r)
    T query(int l, int r) const {
        if (r < l) {
            return 0;
        }
        return pre(r) - pre(l - 1);
    }

};
```