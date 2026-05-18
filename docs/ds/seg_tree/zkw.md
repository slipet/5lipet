# ZKW 線段樹

* 疊代型線段樹/bottom-up

zkw 線段樹只能用於單種運算，不能處理運算優先級的問題，比如（加法+乘法）

因為都寫 zkw 了，所以儘可能的使常數變小

```cpp
#define lc ((node) << 1)
#define rc ((node) << 1 | 1)
#define zkw_idx(idx) (idx + 1)

const int MAXN = 1'000'00 + 5;
int tree[MAXN << 2], tag[MAXN << 2];
int base;

inline int merge(const int &a, const int &b) {
    return max(a, b);
}
//base offset
//value range [1, n]
void build(int n) {
    for (base = 1; base <= n + 2; base <<= 1);
    for (int i = base + 1; i <= base + n; ++i) cin >> tree[i];
    for (int i = base - 1; i; --i) tree[i] = merge(tree[lc(i)], tree[rc(i)]);
}

void update(int x, int v) {
    for(x += base; x; x >>= 1) tree[x] += v;
}

int query(int ql, int qr) {
    int res = 0;
    //exclude range (ql - 1, qr + 1)
    ql += base - 1, qr += base + 1;
    for(; ql ^ qr ^ 1; ql >>= 1, qr >>= 1) {
        if(~ql & 1) res = merge(res, tree[ql ^ 1]);
        if(qr & 1) res = merge(res, tree[qr ^ 1]);
    }
    return res;
}

//不遵守交換律的查詢ex.矩陣乘法
int query(int ql, int qr) {
    int rsL = 0, rsR = 0;
    //exclude range (ql - 1, qr + 1)
    ql += base - 1, qr += base + 1;
    for(; ql ^ qr ^ 1; ql >>= 1, qr >>= 1) {
        if(~ql & 1) rsL = merge(rsL, tree[ql ^ 1]);
        if(qr & 1) rsR = merge(tree[qr ^ 1], rsR);
    }
    return merge(rsL, rsR);
}

//range update[1, n]
void update(int ql, int qr, int d) {
    int len = 1, lcnt = 0, rcnt = 0;
    for(ql += base - 1, qr += base + 1; ql ^ qr ^ 1; ql >>= 1, qr >>= 1, len <<= 1) {
        //這裡的l/rcnt順序很重要不能修改
        //把下面節點的修改往上傳
        tree[ql] += lcnt * d, tree[qr] += rcnt * d;
        //兄弟節點也要把下面的修改往上傳
        if(~ql & 1) tree[ql ^ 1] += len * d, lcnt += len, tag[ql ^ 1] += d;
        if(qr & 1) tree[qr ^ 1] += len * d, rcnt += len, tag[qr ^ 1] += d;
    }
    for(; ql; ql >>= 1, qr >>= 1) {
        tree[ql] += lcnt * d;
        tree[qr] += rcnt * d;
    }
}

//range query[1, n]
int query(int ql, int qr) {
    int len = 1, lcnt = 0, rcnt = 0;
    int ans = 0;
    for(ql += base - 1, qr += base + 1; ql ^ qr ^ 1; ql >>= 1, qr >>= 1, len <<= 1) {
        if(tag[ql]) ans += lcnt * tag[ql];
        if(tag[qr]) ans += rcnt * tag[qr];

        if(~ql & 1) ans += tree[ql ^ 1], lcnt += len;
        if(qr & 1) ans += tree[qr ^ 1], rcnt += len;
    }
    for(; ql; ql >>= 1, qr >>= 1) {
        ans += lcnt * tag[ql];
        ans += rcnt * tag[qr];
    }
}

int main() {
    int n;
    cin >> n;
    build(n);//
    update(zkw_idx(i), 1);//modify range [0, n - 1]
    update(zkw_idx(l), zkw_idx(r), 1);
    query(zkw_idx(ql), zkw_idx(qr));// range [0, n - 1] 
}

```

