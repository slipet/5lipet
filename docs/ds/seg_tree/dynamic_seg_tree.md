#動態開點

ref: https://github.com/EndlessCheng/codeforces-go/blob/master/copypasta/segment_tree.go
```cpp
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

template<typename T, int MAX_NODES = 1000000>
class DynamicSegmentTree {
private:
    struct Node {
        int lo, ro; // 改用 index 代替指標
        int l, r;
        T val;
    };

    // Memory Pool 相關
    Node tree[MAX_NODES];
    int cnt;        // 目前已分配的節點數量
    int root;       // 根節點的 index
    int empty;      // 哨兵節點的 index
    
    int rangeL, rangeR;
    T defaultVal;

    // 分配新節點
    int newNode(int l, int r) {
        if (cnt >= MAX_NODES) return empty; // 記憶體池滿了
        int idx = cnt++;
        tree[idx].l = l;
        tree[idx].r = r;
        tree[idx].val = defaultVal;
        tree[idx].lo = tree[idx].ro = empty;
        return idx;
    }

    T merge(T a, T b) const {
        return max(a, b);
    }

    void maintain(int o) {
        if (o == empty) return;
        tree[o].val = merge(tree[tree[o].lo].val, tree[tree[o].ro].val);
    }

    void update(int &o, int l, int r, int i, T v) {
        if (o == empty) {
            o = newNode(l, r);
        }
        if (l == r) {
            tree[o].val = merge(tree[o].val, v);
            return;
        }
        int m = l + (r - l) / 2;
        if (i <= m) update(tree[o].lo, l, m, i, v);
        else update(tree[o].ro, m + 1, r, i, v);
        maintain(o);
    }

    T query(int o, int ql, int qr) const {
        if (o == empty || ql > tree[o].r || qr < tree[o].l) {
            return defaultVal;
        }
        if (ql <= tree[o].l && tree[o].r <= qr) {
            return tree[o].val;
        }
        return merge(query(tree[o].lo, ql, qr), query(tree[o].ro, ql, qr));
    }

public:
    DynamicSegmentTree(int l, int r, T defVal = 0) 
        : rangeL(l), rangeR(r), defaultVal(defVal), cnt(0) {
        
        // 0 號位置留給哨兵節點 (empty)
        empty = cnt++;
        tree[empty].l = tree[empty].r = 0;
        tree[empty].val = defaultVal;
        tree[empty].lo = tree[empty].ro = empty;
        
        root = empty;
    }

    void Update(int i, T v) {
        if (i < rangeL || i > rangeR) return;
        update(root, rangeL, rangeR, i, v);
    }

    T Query(int ql, int qr) const {
        return query(root, ql, qr);
    }
};

// --- 使用範例 ---
// 注意：MAX_NODES 根據題目範圍調整。
// 若有 N 次 Update，且範圍很大，節點數約為 N * log(Range)
DynamicSegmentTree<int, 2000000> st(1, 1'000'000'000, 0);

int main() {
    ios::sync_with_stdio(false); cin.tie(nullptr);

    st.Update(500, 100);
    st.Update(1000, 500);
    st.Update(500, 200);

    cout << "Query [1, 600]: " << st.Query(1, 600) << endl;      
    cout << "Query [600, 2000]: " << st.Query(600, 2000) << endl; 

    return 0;
}
```
