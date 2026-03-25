# 洛谷模板題


### [HH 的项链](https://www.luogu.com.cn/problem/P1972)

* 離線 + Fenwick

```cpp
struct QUERY {
    int l, r, id;
};

void solve() {
    int n;
    cin >> n;
    vector<int> a(n);
    readv(n, a[i]);
    int m;
    cin >> m;
    vector<QUERY> query(m);
    FenwickTree<int> t(mapping(n));
    unordered_map<int, int> last;
    for(int i = 0; i < m; ++i) {
        auto &q = query[i];
        cin >> q.l >> q.r;
        q.l--; q.r--;
        q.id = i;
    }
    ranges::sort(query, {}, &QUERY::r);
    vector<int> ans(m);
    for(int i = 0, j = 0; i < m; ++i) {
        auto &[l, r, id] = query[i];
        for(;j <= r && j < n; ++j) {
            int &x = a[j];
            if(last.contains(x)) {
                t.update(mapping(last[x]), -1);
            }
            t.update(mapping(j), 1);
            last[x] = j;
        }
        ans[id] = t.query(mapping(l), mapping(r));
    }

    for(auto &x: ans) cout<<x<<endl;
}
```

* 在線: 可持久化線段樹

---