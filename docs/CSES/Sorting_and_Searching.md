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

### 5. Restaurant Customers

差分，要注意區間重疊的時候是開區間還是必區間，這題開始和離開是開區間。

(1) 使用 map 統計不同時間點的變化

```cpp
void solve() {
    int n;
    cin >> n;
    map<int, int> times;
    for(int i = 0; i < n; ++i) {
        int start, end;
        cin >> start >> end;
        times[start]++;
        times[end]--;
    }
    int diff = 0;
    int ans = 0;
    for(auto &[t, cnt]: times) {
        diff += cnt;
        ans = max(diff, ans);
    }
    cout<<ans<<endl;
}
```

(2) 這種方式是看到其他人有速度更快的做法

同樣是差分的思想，但是要注意的是排序後因為 -1 會先計算，所以變化量會被限制住，不會超過餐廳的人數。

```cpp
void solve() {
    int n;
    cin >> n;
    vector<pii> times;
    for(int i = 0; i < n; ++i) {
        int start, end;
        cin >> start >> end;
        times.emplace_back(start, 1);
        times.emplace_back(end, -1);
    }
    ranges::sort(times);
    int diff = 0;
    int ans = 0;
    for(auto &[t, op]: times) {
        diff += op;
        ans = max(diff, ans);
    }
    cout<<ans<<endl;
}
```

### 6. Movie Festival

```cpp
void solve() {
    int n, x, y;
    cin >> n;
    vector<pii> times;
    readv2(n, x >> y, times.emplace_back(y, x));
    ranges::sort(times);
    int reach = 0;
    int ans = 0;
    for(auto &[to, from]: times) {
        if(reach <= from) {
            reach = to;
            ans++;
        }
    }
    cout<<ans<<endl;
}
```

### 7. Sum of Two Values

經典 2 sum，但是一開始寫的時候看錯，要印的答案是 index，所以錯了一次。

(1) 雙指針，要開 2n 的空間存 index

```cpp
void solve() {
    ll n, x;
    cin >> n >> x;
    vector<pair<long long, int>> a(n);
    readv2(n, a[i].first, a[i].second = i + 1);
    ranges::sort(a);
    int l = 0, r = n - 1;
    while(l < r) {
        if(a[l].first + a[r].first < x) {
            l++;
        } else if(a[l].first + a[r].first > x) {
            r--;
        } else {
            cout<<a[l].second<<" "<<a[r].second<<endl;
            return;
        }
    }
    cout<<"IMPOSSIBLE"<<endl;
}
```

(2) hash table, O(n) 空間, 要注意會因為 1. hash collision attack 2. rehash 次數太多 或是 3. memory 太大/ cache miss 導致性能劇烈下降 $O(n^2)$。

可以先用 reserve 先確定所需的空間解決。

reserve 是從 [GPT](https://chatgpt.com/share/693c17eb-5c40-8010-9930-c66d5aaf2079) 學到的。

更詳細 [Blowing up unordered_map, and how to stop getting hacked on it](https://codeforces.com/blog/entry/62393)

讀完上面那篇文章後發現單純用 reserve 是沒辦法解決 hacking 的問題。需要自己寫一個 hash function

```cpp
struct chash {
    #include <chrono>
    static uint64_t splitmix64(uint64_t x) {
        // source: http://xorshift.di.unimi.it/splitmix64.c
        x += 0x9e3779b97f4a7c15ULL;
        x = (x ^ (x >> 30)) * 0xbf58476d1ce4e5b9ULL;
        x = (x ^ (x >> 27)) * 0x94d049bb133111ebULL;
        return x ^ (x >> 31);
    }

    // for long long / int
    size_t operator()(uint64_t x) const {
        // Use a fixed random seed (combining time and memory address) for this hash functor
        static const uint64_t FIXED_RANDOM = (long long)(make_unique<char>().get()) ^ chrono::steady_clock::now().time_since_epoch().count();
        return splitmix64(x + FIXED_RANDOM);
    }
};

void solve() {
    int n, x, y;
    cin >> n >> x;
    unordered_map<int, int, chash> dict;
    int a = 0, b = 0;
    for(int i = 0; i < n; ++i) {
        cin >> y;
        if(dict.contains(y)) {
            a = dict[y];
            b = i + 1;
        }
        dict[x - y] = i + 1;
    }
    if(a && b) {
        cout<<a<<" "<<b<<endl;
    } else {
        cout<<"IMPOSSIBLE"<<endl;
    }
}
```

### 8. Maximum Subarray Sum

經典的最大區間和有兩種做法

(1) prefix sum

前綴和一定要注意最一開始陣列為空的時候的狀態，因為 mn 的關係 wa 了一次。

```cpp
void solve() {
    
    int n, x;
    cin >> n;
    ll pre = 0, mn = 0;
    ll ans = -infll;
    
    for(int i = 0; i < n; ++i) {
        cin >> x;
        pre += x;
        ans = max(ans, pre - mn);
        mn = min(mn, pre);
    }
    cout<<ans<<endl;
}
```

(2) dp

```cpp
void solve() {
    
    int n;
    cin >> n;
    ll pre = 0, mn = 0, x;
    ll ans = -infll;
    
    for(int i = 0; i < n; ++i) {
        cin >> x;
        pre = max(x, pre + x);
        ans = max(ans, pre);
    }
    cout<<ans<<endl;
}
```

### 8. Stick Lengths

中位數貪心 $min(|a_i - b_i|), b_i = med(a_i)$

```cpp
void solve() {
    ll n, sum, x;
    cin >> n;
    int mid = n / 2;
    vl a(n), pre(n + 1);
    
    for(int i = 0; i < n; ++i) {
        cin >> a[i];
    }
    ranges::sort(a);
    partial_sum(a.begin(), a.end(), pre.begin() + 1);
    
    x = a[mid];
    sum = pre[n] - pre[mid + 1] - x * (n - (mid + 1)) + x * (mid + 1)- (pre[mid + 1]);
    cout<<sum<<endl;
}
```