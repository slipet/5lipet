#動態開點

一開始 memory pool 可以用 

空間大小公式: $O(m\log{n})$ 的大小，$m$ 為操作次數，$n$ 是值域。
* 單點修改： 每次操作最多產生 $\lceil \log_2{n} \rceil + 1$ 個新節點。如果操作 m 次，約需 $M(\log_2{n} + C)$ 個節點。
* 區間修改（Lazy Tag）： 節點數會更多一些，但通常在 $2 \times m\log{n}$ 到 $4 \times m\log{n}$ 範圍內。


## 單點修改

```cpp
#define lc(node) (tree[node].l)
#define rc(node) (tree[node].r)

const int SZ2 = m * (2 << bit_width(n));
const int SZ = 4'000'000;

template<typename T>
class DynamicSegTree {
    struct Node {
        int l, r;
        T val;
    };
    inline static Node tree[SZ];
    int empty;
    int root;
    int cnt;
    int rangeL;
    int rangeR;

    int newNode() {
        if(cnt >= SZ) return empty;
        int node = cnt++;
        tree[node].l = tree[node].r = empty;
        tree[node].val = 0;
        return node;
    }
    T merge_val(T &a, T &b) {
        return (a + b);
    }
    void ensure(int &node) {
        if (node == empty) {
            node = newNode();
        }
    }
    void maintain(int node) {
        tree[node].val = merge_val(tree[lc(node)].val, tree[rc(node)].val);
    }
    void update(int &node, int l, int r, int i, F val) {
        ensure(node);
        if (l == r) {
            tree[node].val = merge(tree[node].val, val);
            return;
        }
        int m = l + (r - l) / 2;
        if(i <= m) update(lc(node), l, m, i, val);
        if(i > m) update(rc(node), m + 1, r, i, val);
        maintain(node);
    }
    T query(int node, int l, int r, int ql, int qr) {
        if(node == empty || (ql <= l && r <= qr)) {
            return tree[node].val;
        }
        
        int m = l + (r - l) / 2;
        if(qr <= m) return query(lc(node), l, m, ql, qr);
        if(ql > m) return query(rc(node), m + 1, r, ql, qr);
        T rsL = query(lc(node), l, m, ql, qr);
        T rsR = query(rc(node), m + 1, r, ql, qr);
        return merge_val(rsL, rsR);
    }
public:
    DynamicSegTree(int l, int r)
        : cnt(0), rangeL(l), rangeR(r) {
        empty = cnt++;
        tree[empty].l = empty;
        tree[empty].r = empty;
        tree[empty].val = 0;
        root = empty;
    }
    void update(int i, F val) {
        if (i < rangeL || i > rangeR) return;
        update(root, rangeL, rangeR, i, val);
    }
    T query(int ql, int qr) {
        if (ql > qr) return 0;
        if (ql < rangeL || qr > rangeR) return 0;
        return query(root, rangeL, rangeR, ql, qr);
    }
};
```


## Lazy Tag

```cpp
#define lc(node) (tree[node].l)
#define rc(node) (tree[node].r)

const int MOD = 1'000'000'000 + 7;
const int SZ2 = 4* m * (2 << bit_width(n));
const int SZ = 4'000'000;
template<typename T, typename F>
class DynamicSegTree {
    struct Node {
        int l, r;
        T val;
        F todo;
    };
    inline static Node tree[SZ];
    const F INIT_TODO = 0;
    int empty;
    int root;
    int cnt;
    int rangeL;
    int rangeR;
    int newNode() {
        if(cnt >= SZ) return empty;
        int node = cnt++;
        tree[node].l = tree[node].r = empty;
        tree[node].val = 0;
        tree[node].todo = INIT_TODO;
        return node;
    }
    T merge_val(T &a, T &b) {
        return (a + b) % MOD;
    }
    F merge_todo(F &a, F &b) {
        return (a + b) % MOD;
    }
    void ensure(int &node) {
        if (node == empty) {
            node = newNode();
        }
    }
    void apply(int &node, int l, int r, F todo) {
        ensure(node);
        Node &cur = tree[node];
        cur.val += todo * (r - l + 1);
        cur.todo = merge_todo(cur.todo, todo);
    }
    void spread(int node, int l, int r) {
        Node &cur = tree[node];
        F todo = tree[node].todo;
        if (todo == INIT_TODO) {
            return;
        }
        int m = l + (r - l) / 2;
        apply(lc(node), l, m, todo);
        apply(rc(node), m + 1, r, todo);
        cur.todo = INIT_TODO;
    }
    void maintain(int node) {
        tree[node].val = merge_val(tree[lc(node)].val, tree[rc(node)].val);
    }
    void update(int &node, int l, int r, int ql, int qr, F val) {
        ensure(node);
        if (ql <= l && r <= qr) {
            apply(node, l, r, val);
            return;
        }
        spread(node, l, r);
        int m = l + (r - l) / 2;
        if(ql <= m) update(lc(node), l, m, ql, qr, val);
        if(qr > m) update(rc(node), m + 1, r, ql, qr, val);
        maintain(node);
    }
    T query(int node, int l, int r, int ql, int qr) {

        if(node == empty || (ql <= l && r <= qr)) {
            return tree[node].val;
        }
        
        spread(node, l, r);
        int m = l + (r - l) / 2;
        if(qr <= m) return query(lc(node), l, m, ql, qr);
        if(ql > m) return query(rc(node), m + 1, r, ql, qr);
        T rsL = query(lc(node), l, m, ql, qr);
        T rsR = query(rc(node), m + 1, r, ql, qr);
        return merge_val(rsL, rsR);
    }
public:
    DynamicSegTree(int l, int r)
        : cnt(0), rangeL(l), rangeR(r) {
        empty = cnt++;
        tree[empty].l = empty;
        tree[empty].r = empty;
        tree[empty].val = 0;
        tree[empty].todo = INIT_TODO;
        root = empty;
    }
    void update(int ql, int qr, F val) {
        if (ql > qr) return;
        if (ql < rangeL || qr > rangeR) return;
        update(root, rangeL, rangeR, ql, qr, val);
    }
    T query(int ql, int qr) {
        if (ql > qr) return 0;
        if (ql < rangeL || qr > rangeR) return 0;
        return query(root, rangeL, rangeR, ql, qr);
    }
};
```
