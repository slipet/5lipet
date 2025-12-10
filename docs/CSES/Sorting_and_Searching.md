# Sorting and Searching

### 1. Distinct Numbers

要注意的是如果使用 unordered_set 會因為 hash colision 導致 TLE

要用 sort 或是 set。

```cpp
void solve() {
    int n;
    cin >> n;
    vector<int> a(n);
    for(int i = 0; i < n; ++i) {
        cin >> a[i];
    }
    ranges::sort(a);
    a.erase(ranges::unique(a).begin(), a.end());
    cout<<a.size()<<endl;
}
```

### 2. Apartments

看錯題目以為公寓可以容納無限人，結果是一人只能分配一個公寓，那就是兩個陣列排序後，貪心的從最小的取。

```cpp
void solve() {
    ll n, m, k;
    cin >> n >> m >> k;
    vector<ll> applicants(n), apartments(m);
    for(int i = 0; i < n; ++i) cin >> applicants[i];
    for(int i = 0; i < m; ++i) cin >> apartments[i];
    ranges::sort(applicants);
    ranges::sort(apartments);
    int j = 0;
    int ans = 0;
    for(auto &x: apartments) {
        ll start = x - k, end = x + k;
        while(j < n && applicants[j] < start) {
            j++;
        }
        if(j < n && applicants[j] <= end) {
            ans++;
            j++;
        }
    }
    cout<<ans<<endl;
}
```

### 3. Ferris Wheel

雙指針

```cpp
void solve() {
    int n, mx;
    cin >> n >> mx;
    vector<int> p(n);
    for(int i = 0; i < n; ++i) cin >> p[i];
    ranges::sort(p);
    int l = 0, r = n - 1, ans = 0;
    while(l <= r) {
        if(l != r && p[l] + p[r] <= mx) {
            l++;
            ans++;
        } else if(p[r] <= mx) {
            ans++;
        }
        r--;
    }
    cout<<ans<<endl;
}
```

### 4. Ferris Wheel

multiset 的運用，iterator 可以做加減，不能找 idx

```cpp
#define readv(sz, var) do {                 \
            for(int i = 0; i < sz; ++i) {   \
                cin >> var;                 \
            }                               \
            } while(0)

#define readv2(sz, var, stmt) do {          \
            for(int i = 0; i < sz; ++i) {   \
                cin >> var;                 \
                stmt;                       \
            }                               \
        } while(0)

void solve() {
    int n, m, x;
    cin>> n >> m;
    vector<int> queries(m), ans;
    multiset<int> prices;
    readv2(n, x, prices.insert(x));
    readv(m, queries[i]);
    for(auto &q: queries) {
        auto it = prices.upper_bound(q);
        if(it == prices.begin()) {
            cout<<-1<<endl;
        } else {
            it--;
            cout<<*it<<endl;
            prices.erase(it);
        }
    }
}
```