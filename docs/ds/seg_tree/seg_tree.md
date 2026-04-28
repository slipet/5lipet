# Segment Tree

## Segment Tree (無區間更新)

* 只支援區間查詢，單點更新。

* 把任意區間用 $O(\log{n})$ 個區間表示，線段樹的每個節點記錄對應區間的信息。

* 詢問：把詢問區間拆分成 $O(\log{n})$ 個區間，對應著線段樹的 $O(\log{n})$ 個節點，把這 $O(\log{n})$ 個節點的信息合並，即為答案。

* <span style="color:red">單點</span>更新：有 $O(\log{n})$ 個區間包含被修改的位置，需要更新 $O(\log{n})$ 個節點的信息。

* 若是葉子節點有 n 個節點，則總共需要開闢的陣列空間為 <span style="color:red">$2^{\lceil \log{n} \rceil + 1} - 1$</span>:

    可以知道完美二元樹時，此時樹的高度 $h = \lceil \log{n} \rceil$，總共的節點個數為:
    
    $$2^0 + 2^1 + 2^2 + .... + 2^h = \frac{1(1 - 2^{h + 1})}{1 - 2} = 2^{h + 1} - 1$$

    偷懶寫法可以直接宣告 $4n$ 的空間。

```cpp
// 模板来源 https://leetcode.cn/circle/discuss/mOr1u6/
// 线段树有两个下标，一个是线段树节点的下标，另一个是线段树维护的区间的下标
// 节点的下标：从 1 开始，如果你想改成从 0 开始，需要把左右儿子下标分别改成 node*2+1 和 node*2+2
// 区间的下标：从 0 开始
template<typename T>
class SegmentTree {
    // 注：也可以去掉 template<typename T>，改在这里定义 T
    // using T = pair<int, int>;

    int n;
    vector<T> tree;

    // 合并两个 val
    T merge_val(T a, T b) const {
        return max(a, b); // **根据题目修改**
    }

    // 合并左右儿子的 val 到当前节点的 val
    void maintain(int node) {
        tree[node] = merge_val(tree[node * 2], tree[node * 2 + 1]);
    }

    // 用 a 初始化线段树
    // 时间复杂度 O(n)
    void build(const vector<T>& a, int node, int l, int r) {
        if (l == r) { // 叶子
            tree[node] = a[l]; // 初始化叶节点的值
            return;
        }
        int m = (l + r) / 2;
        build(a, node * 2, l, m); // 初始化左子树
        build(a, node * 2 + 1, m + 1, r); // 初始化右子树
        maintain(node);
    }
    
    //要注意這是單點更新
    void update(int node, int l, int r, int i, T val) {
        if (l == r) { // 叶子（到达目标）
            // 如果想直接替换的话，可以写 tree[node] = val
            tree[node] = merge_val(tree[node], val);
            return;
        }
        int m = (l + r) / 2;
        if (i <= m) { // i 在左子树
            update(node * 2, l, m, i, val);
        } else { // i 在右子树
            update(node * 2 + 1, m + 1, r, i, val);
        }
        maintain(node);
    }

    T query(int node, int l, int r, int ql, int qr) const {
        if (ql <= l && r <= qr) { // 当前子树完全在 [ql, qr] 内
            return tree[node];
        }
        int m = (l + r) / 2;
        if (qr <= m) { // [ql, qr] 在左子树 -> ql, qr, m
            return query(node * 2, l, m, ql, qr);
        }
        if (ql > m) { // [ql, qr] 在右子树 -> m + 1, ql, qr
            return query(node * 2 + 1, m + 1, r, ql, qr);
        }
        T l_res = query(node * 2, l, m, ql, qr);
        T r_res = query(node * 2 + 1, m + 1, r, ql, qr);
        return merge_val(l_res, r_res);
    }

public:
    // 线段树维护一个长为 n 的数组（下标从 0 到 n-1），元素初始值为 init_val
    SegmentTree(int n, T init_val) : SegmentTree(vector<T>(n, init_val)) {}

    // 线段树维护数组 a
    SegmentTree(const vector<T>& a) : n(a.size()), tree(2 << bit_width(a.size() - 1)) {
        build(a, 1, 0, n - 1);
    }

    // 更新 a[i] 为 merge_val(a[i], val)
    // 时间复杂度 O(log n)
    void update(int i, T val) {
        update(1, 0, n - 1, i, val);
    }

    // 返回用 merge_val 合并所有 a[i] 的计算结果，其中 i 在闭区间 [ql, qr] 中
    // 时间复杂度 O(log n)
    T query(int ql, int qr) const {
        return query(1, 0, n - 1, ql, qr);
    }

    // 获取 a[i] 的值
    // 时间复杂度 O(log n)
    T get(int i) const {
        return query(1, 0, n - 1, i, i);
    }
};
```

## Lazy tag(懶標記)

```cpp
// 模板来源 https://leetcode.cn/circle/discuss/mOr1u6/
template<typename T, typename F>
class LazySegmentTree {
    // 注：也可以去掉 template<typename T, typename F>，改在这里定义 T 和 F
    // using T = pair<int, int>;
    // using F = pair<int, int>;

    // 懒标记初始值
    const F TODO_INIT = 0; // **根据题目修改**

    struct Node {
        T val;
        F todo;
    };

    int n;
    vector<Node> tree;

    // 合并两个 val
    T merge_val(const T& a, const T& b) const {
        return a + b; // **根据题目修改**
    }

    // 合并两个懒标记
    F merge_todo(const F& a, const F& b) const {
        return a + b; // **根据题目修改**
    }

    // 把懒标记作用到 node 子树（本例为区间加）
    void apply(int node, int l, int r, F todo) {
        Node& cur = tree[node];
        // 计算 tree[node] 区间的整体变化
        cur.val += todo * (r - l + 1); // **根据题目修改**
        cur.todo = merge_todo(todo, cur.todo);
    }

    // 把当前节点的懒标记下传给左右儿子
    void spread(int node, int l, int r) {
        Node& cur = tree[node];
        F todo = cur.todo;
        if (todo == TODO_INIT) { // 没有需要下传的信息
            return;
        }
        int m = (l + r) / 2;
        apply(node * 2, l, m, todo);
        apply(node * 2 + 1, m + 1, r, todo);
        cur.todo = TODO_INIT; // 下传完毕
    }

    // 合并左右儿子的 val 到当前节点的 val
    void maintain(int node) {
        tree[node].val = merge_val(tree[node * 2].val, tree[node * 2 + 1].val);
    }

    // 用 a 初始化线段树
    // 时间复杂度 O(n)
    void build(const vector<T>& a, int node, int l, int r) {
        Node& cur = tree[node];
        cur.todo = TODO_INIT;
        if (l == r) { // 叶子
            cur.val = a[l]; // 初始化叶节点的值
            return;
        }
        int m = (l + r) / 2;
        build(a, node * 2, l, m); // 初始化左子树
        build(a, node * 2 + 1, m + 1, r); // 初始化右子树
        maintain(node);
    }

    void update(int node, int l, int r, int ql, int qr, F f) {
        if (ql <= l && r <= qr) { // 当前子树完全在 [ql, qr] 内
            apply(node, l, r, f);
            return;
        }
        spread(node, l, r);
        int m = (l + r) / 2;
        if (ql <= m) { // 更新左子树
            update(node * 2, l, m, ql, qr, f);
        }
        if (qr > m) { // 更新右子树
            update(node * 2 + 1, m + 1, r, ql, qr, f);
        }
        maintain(node);
    }

    T query(int node, int l, int r, int ql, int qr) {
        if (ql <= l && r <= qr) { // 当前子树完全在 [ql, qr] 内
            return tree[node].val;
        }
        spread(node, l, r);
        int m = (l + r) / 2;
        if (qr <= m) { // [ql, qr] 在左子树
            return query(node * 2, l, m, ql, qr);
        }
        if (ql > m) { // [ql, qr] 在右子树
            return query(node * 2 + 1, m + 1, r, ql, qr);
        }
        T l_res = query(node * 2, l, m, ql, qr);
        T r_res = query(node * 2 + 1, m + 1, r, ql, qr);
        return merge_val(l_res, r_res);
    }

public:
    // 线段树维护一个长为 n 的数组（下标从 0 到 n-1），元素初始值为 init_val
    LazySegmentTree(int n, T init_val = 0) : LazySegmentTree(vector<T>(n, init_val)) {}

    // 线段树维护数组 a
    LazySegmentTree(const vector<T>& a) : n(a.size()), tree(2 << bit_width(a.size() - 1)) {
        build(a, 1, 0, n - 1);
    }

    // 用 f 更新 [ql, qr] 中的每个 a[i]
    // 0 <= ql <= qr <= n-1
    // 时间复杂度 O(log n)
    void update(int ql, int qr, F f) {
        update(1, 0, n - 1, ql, qr, f);
    }

    // 返回用 merge_val 合并所有 a[i] 的计算结果，其中 i 在闭区间 [ql, qr] 中
    // 0 <= ql <= qr <= n-1
    // 时间复杂度 O(log n)
    T query(int ql, int qr) {
        return query(1, 0, n - 1, ql, qr);
    }
};

int main() {
    LazySegmentTree<long long, long long> t(8); // 默认值为 0
    t.update(3, 5, 100);
    t.update(4, 6, 10);
    cout << t.query(0, 7) << endl;

    vector<long long> nums = {3, 1, 4, 1, 5, 9, 2, 6};
    LazySegmentTree<long long, long long> t2(nums);
    t2.update(3, 5, 1);
    t2.update(4, 6, 1);
    cout << t2.query(0, 7) << endl;
    return 0;
}
```

#### <span style="color:red"> 懶標記的意義是目前已經應用在 val 上的操作。</span>


* lougu [P3372 【模板】线段树 1](https://www.luogu.com.cn/problem/P3372)
* lougu [P3373 【模板】线段树 2](https://www.luogu.com.cn/problem/P3373)
    這題用到兩種區間操作 1. 區間加法 2. 區間乘法，要注意到加法和乘法是有順序關係的
    $val = (val + add) * mul$
    要將乘法的影響應用到 todo add 的 lazy tag 上
```cpp
template<typename T, typename F>
class LazySegmentTree {
    ....
    void apply(int node, int l, int r, F todo_add, F todo_mul) {
        Node &cur = tree[node];
        cur.val = cur.val * todo_mul;
        cur.val %= MOD;
        cur.val += todo_add * (r - l + 1);
        cur.val %= MOD;
        //要將乘法的影響應用到 todo add 的 lazy tag 上
        cur.todo_add = merg_add(cur.todo_add * todo_mul, todo_add);
        //
        cur.todo_mul = merg_mul(cur.todo_mul, todo_mul);
    }
    ....
};
```

## Tag Permanence(標記永久化)

如果確定懶惰標記不會在中途被加到溢出（即超過了該類型數據所能表示的最大範圍），那麼就可以將標記永久化

修改時一路更改被影響到的點，可以避免下傳懶惰標記，詢問時則一路累加路上的標記，從而省去下傳標記的操作。


```cpp
template<typename T, typename F>
class PermanenceSegTree{
    struct Node {
        T val;
        F tag;
    };
    const F INIT_TAG = 0;
    vector<Node> tree;
    int n;

    T merge_val(T &a, T &b) {
        return a + b;
    }
    void maintain(int node) {
        tree[node].val = merge_val(tree[node * 2].val, tree[node * 2 + 1].val);
    }
    void build(const vector<T> &a, int node, int l, int r) {
        tree[node].tag = INIT_TAG;
        if(l == r) {
            tree[node].val = a[l];
            return;
        }
        int m = l + (r - l) / 2;
        build(a, node * 2, l, m);
        build(a, node * 2 + 1, m + 1, r);
        maintain(node);
    }

    void update(int node, int l, int r, int ql, int qr, F val) {
        if(ql <= l && r <= qr) {
            tree[node].tag += val;
            return;
        }
        tree[node].val += val * (qr - ql + 1);
        int m = l + (r - l) / 2;
        if(qr <= m) update(node * 2, l, m, ql, qr, val);
        else if(ql > m) update(node * 2 + 1, m + 1, r, ql, qr, val);
        else {
            update(node * 2, l, m, ql, m, val);
            update(node * 2 + 1, m + 1, r, m + 1, qr, val);
        }
    }

    T query(int node, int l, int r, int ql, int qr) {
        F gain = tree[node].tag * (qr - ql + 1);
        if(ql <= l && r <= qr) {
            return tree[node].val + gain;
        }
        int m = l + (r - l) / 2;
        if(qr <= m) return gain + query(node * 2, l, m, ql, qr);
        if(ql > m) return gain + query(node * 2 + 1, m + 1, r, ql, qr);
        T rsL = query(node * 2, l, m, ql, m);
        T rsR = query(node * 2 + 1, m + 1, r, m + 1, qr);
        return gain + rsL + rsR;
    }

public:


    PermanenceSegTree(int n, T init_val = 0) : PermanenceSegTree(vector<T>(n, init_val)) {}
    
    PermanenceSegTree(const vector<T>& a) : n(a.size()), tree(2 << bit_width(a.size() - 1)) {
        build(a, 1, 0, n - 1);
    }
    void update(int ql, int qr, F val) {
        if(ql > qr) return;
        update(1, 0, n - 1, ql, qr, val);
    }
    T query(int ql, int qr) {
        if(ql > qr) return 0;
        return query(1, 0, n - 1, ql, qr);
    }
};
```