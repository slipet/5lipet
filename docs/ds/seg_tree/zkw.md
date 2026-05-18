# ZKW 線段樹

* 疊代型線段樹/bottom-up

zkw 線段樹只能用於單種運算，不能處理運算優先級的問題，比如（加法+乘法）

因為都寫 zkw 了，所以儘可能的使常數變小

```cpp
#define lc ((node) << 1)
#define rc ((node) << 1 | 1)
#define zkw_idx(idx) (idx + 1)

const int MAXN = 1'000'00 + 5;
int tree[MAXN << 2];
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
    int ans = 0;
    //intput 
    for(ql += base - 1, qr += base + 1)
}

int main() {
    int n;
    cin >> n;
    build(n);//
    update(zkw_idx(i), 1);//modify range [0, n - 1]
}

```

