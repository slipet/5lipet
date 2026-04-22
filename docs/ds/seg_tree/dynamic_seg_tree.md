#動態開點

ref: https://github.com/EndlessCheng/codeforces-go/blob/master/copypasta/segment_tree.go
```cpp
#include <iostream>
#include <algorithm>
#include <functional>

class DynamicSegmentTree {
private:
    struct Node {
        Node *lo, *ro;
        int l, r, val;

        Node(int _l, int _r, Node* empty, int defaultVal) 
            : l(_l), r(_r), val(defaultVal), lo(empty), ro(empty) {}
    };

    Node *root;
    Node *empty;
    int rangeL, rangeR;
    int defaultVal;

    // --- 核心 Merge 邏輯 ---
    // 這裡可以根據需求修改，例如 return a + b; 或 return min(a, b);
    int merge(int a, int b) const {
        return max(a, b);
    }

    void maintain(Node* o) {
        if (o == empty) return;
        o->val = merge(o->lo->val, o->ro->val);
    }

    void update(Node* &o, int l, int r, int i, int v) {
        if (o == empty) {
            o = new Node(l, r, empty, defaultVal);
        }
        if (l == r) {
            // 單點更新時的合併邏輯（通常是覆蓋或取最大）
            o->val = merge(o->val, v); 
            return;
        }
        int m = l + (r - l) / 2;
        if (i <= m) update(o->lo, l, m, i, v);
        else update(o->ro, m + 1, r, i, v);
        maintain(o);
    }

    int query(Node* o, int ql, int qr) const {
        if (o == empty || ql > o->r || qr < o->l) {
            return defaultVal;
        }
        if (ql <= o->l && o->r <= qr) {
            return o->val;
        }
        return merge(query(o->lo, ql, qr), query(o->ro, ql, qr));
    }

    void clear(Node* o) {
        if (o == empty) return;
        clear(o->lo);
        clear(o->ro);
        delete o;
    }

public:
    DynamicSegmentTree(int l, int r, int defVal = 0) 
        : rangeL(l), rangeR(r), defaultVal(defVal) {
        
        // 建立哨兵節點
        empty = static_cast<Node*>(operator new(sizeof(Node)));
        empty->l = empty->r = 0;
        empty->val = defaultVal;
        empty->lo = empty->ro = empty;
        
        root = empty;
    }

    ~DynamicSegmentTree() {
        clear(root);
        operator delete(empty);
    }

    void Update(int i, int v) {
        if (i < rangeL || i > rangeR) return;
        update(root, rangeL, rangeR, i, v);
    }

    int Query(int ql, int qr) const {
        return query(root, ql, qr);
    }
};

// --- 使用範例 ---
int main() {
    // 建立一個範圍為 [1, 10^9] 的線段樹
    DynamicSegmentTree st(1, 1'000'000'000, 0);

    st.Update(500, 100);
    st.Update(1000, 500);
    st.Update(500, 200); // 更新同一個點，取 Max 會變成 200

    cout << "Query [1, 600]: " << st.Query(1, 600) << endl;      // 200
    cout << "Query [600, 2000]: " << st.Query(600, 2000) << endl; // 500
    cout << "Query [1, 2000]: " << st.Query(1, 2000) << endl;   // 500

    return 0;
}
```
