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

### 9. Stick Lengths

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

### 10. Missing Coin Sum

這題一開始看到會聯想到背包問題，背包問題有一種優化的方式是利用 bit 操作在值域上，但是這題的值域為 $n^9$ ，如果直接宣告 bitset 可能會造成 MLE。

接著思考 bit 操作的優化方式:

對於背包問題我們可以遍歷陣列中的元素，每遇到一個元素 x 可以把當前的值域 S 向左 shift x 距離，而 S | (S << x) 中所有 bit 為 1 的位置皆為可以構造的答案。

ex. nums = [1, 2, 2, 7]

0000 -> 0001 -> 0111 -> 11111 -> 1111 1000 0101 1111
                               
如何連結到本問題?

本題求的是 $MEX(nums)$，觀察上面的例子，可以知道所有 $\le MEX(nums)$ 位置的 bit 皆為 1，一旦操作後的值域 S 有空位(指的是不連續 1 中產生的 bit 0)的話表示有數字無法構造出來。

要構造出數字 a 必須使用 $\le a$ 的元素構造，所以直覺地使用由小至大遍歷進行判斷。

接著觀察上面的例子在位元表示的部分可以知道每次位移後若是產生空位則有數字無法構造出來，而因為我們由小至大遍歷，則更大的元素肯定也無法構造出這個數字。此外可以發現每次位移後若是產生的值域都是連續的 1 則表示小於等於 hi bit 的數字都是可以構造出來的。

由上面可以得到一個結論: 當從小至大的元素遍歷，每次都判斷當前可構造的值域 sum + 1 是否 >= 當前元素 x ，若是 < x 則表示 S 做位移後產生的值域會有空位，無法構造出來的數字為 sum + 1。

```cpp
void solve() {
    int n;
    ll sum = 0;
    cin >> n;
    vl a(n);
    readv(n, a[i]);
    ranges::sort(a);
    
    for(auto &x: a) {
        if(sum + 1 < x) {
            break;
        }
        sum += x;
    }
    cout<< sum + 1 << endl;
}
```

### 11. Collecting Numbers

一開始看錯題意，以為是單純按照升序選數字，把 n 個數選完，結果用 LIS greedy 的想法做了一次後 wa，回去重新讀題後發現是要按照順序選 1 ~ n。

```cpp
void solve() {
    int n, x;
    cin >> n;
    
    vector<int> a(n);
    for(int i = 0; i < n; ++i) {
        cin >> x;
        a[x - 1] = i;
    }
    int ans = 0;
    for(int x = 0; x < n; ++x) {
        if(x > 0 && a[x - 1] <= a[x]) continue;
        ans++;
    }
    cout<<ans<<endl;
}
```

* 這是一開始想錯的題意，不過可以當作題目的 follow-up

Q: 按照升序選數字，把 n 個數選完，最少需要選多少次

在「所有數互不相同」的序列中，

最少幾條嚴格遞增子序列可以把它拆完 = 這個序列的最長'非'嚴格遞減子序列的長度。

在 LIS 中，把比較方向改成相反（或用 -a[i]）可得。

證明:

這是一個在偏序集中的經典定理 - Dilworth theorem

[video](https://www.bilibili.com/video/BV1VNCGY3ERK/?spm_id_from=333.337.search-card.all.click&vd_source=caaccd1459c5ece44b5e2d37804871b8)

todo

```cpp
void solve() {
    int n, x;
    cin >> n;
    
    vector<int> a;
    for(int i = 0; i < n; ++i) {
        cin >> x;
        auto it = ranges::upper_bound(a, -x);
        if(it == a.end()) a.push_back(-x);
        else *it = -x;
    }
    cout<<a.size()<<endl;
}
```

### 12. Collecting Numbers II

前一題的 follow-up，每次都會交換一對 pair 的位置，因此必須維護當前總共有多少段遞增的 subarray，每次交換都判斷前後的貢獻變化。

<span style="color:red">在 CSES 上連續輸出使用 '\n' 比 endl 快很多</span>

```cpp
void solve() {
    int n, m;
    cin >> n >> m;
    
    vector<int> a(n + 1), idx(n + 1);
    for(int i = 1; i <= n; ++i) {
        cin >> idx[i];
        a[idx[i]] = i;
    }
    int ans = 1;
    for(int i = 1; i < n; ++i) {
        ans += (a[i] > a[i + 1]);
    }
    auto check = [&](int x) -> int {
        if(x < 1 || x >= n) return 0;
        return a[x] > a[x + 1];
    };
    while(m--) {
        int i0, i1;
        cin >> i0 >> i1;
        int ia = idx[i0], ib = idx[i1];
        set<int> st = {ia - 1, ia, ib - 1, ib};
        for(int x: st) ans -= check(x);
        swap(a[ia], a[ib]);
        swap(idx[i0], idx[i1]);
        for(int x: st) ans += check(x);
        cout << ans << "\n";
    }
}
```

### 13. Collecting Numbers II

左閉右開滑窗，注意 default unordered_map 會 TLE，可以加上 customized hash 或是用 map

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
    int n;
    cin >> n;
    unordered_map<int, int, chash> cnt;
    vector<int> a(n);
    for(int i = 0; i < n; ++i) {
        cin >> a[i];
    }
    int ans = 0, invalid = 0;
    for(int r = 0, l = 0; r < n;) {
        int &x = a[r++];
        if(cnt[x] == 1) {
            invalid++;
        }
        cnt[x]++;
        while(l < r && invalid) {
            int del = a[l++];
            if(cnt[del] == 2) invalid--;
            cnt[del]--;
        }
        ans = max(ans, r - l);
    }
    cout<<ans<<endl;
}
```

### 14. Towers

Dilworth theorem 

```cpp
void solve() {
    int n, x;
    cin >> n;

    vector<int> a;
    for(int i = 0; i < n; ++i) {
        cin >> x;
        auto it = ranges::upper_bound(a, x);
        if(it == a.end()) a.push_back(x);
        else *it = x;
    }
    cout<<a.size()<<endl;
}
```

### 15. Traffic Lights

題目沒說清楚沒有路燈的長度是如何計算的，所以認為是單純計算沒有路燈的連續元素，結果發現是計算包含路燈的半開區間。

分別使用 set 跟 multiset ， set 維護目前的端點數量，multiset 維護當前合法區間長度。

不斷的往 pos 中加入元素，使用 upper_bound 找到對應的區間進行切分。

```cpp
void solve() {
    int x, n;
    cin >> x >> n;
    vi a(n);
    for(int i = 0; i < n; ++i) {
        cin >> a[i];
    }
    set<int> pos;
    multiset<int> len;
    pos.insert(0);
    pos.insert(x);
    len.insert(x);
    for(auto &p: a) {
        auto it = pos.upper_bound(p);
        int nxt = *it;
        int pre = *(--it);
        
        len.erase(len.find(nxt - pre));
        len.insert(p - pre);
        len.insert(nxt - p);
        pos.insert(p);
        cout<<*len.rbegin()<<" ";
    }
    
}
```

第二種方法是使用 linked list 的方式，我一開始想用這種方式由前往後切開區間，但是卡在離散化和發現沒辦法維護當前最大區間。假設同時有三個相同最大長度的區間，當你斷開其中一個區間後你需要維護剩下兩個最大的區間，由順序的方式會很難維護。

所以這種方式必定要由後往前進行合併，因為是由小區間合併成大區間，所以合併的過程必定會經過最大區間。

這個方法的另一個難點在處理 linked list，因為我這邊分別使用了離散化和 left, right 模擬雙向鏈結串列。

要注意的是 left[p], right[p] 內的元素要當作下一個指標才行，我一開始卡在 right[p - 1] = right[p]; 這種寫法，沒辦法正確的指到下一個指標。

```cpp
void solve() {
    int x, n;
    cin >> x >> n;
    vi a(n + 2);
    htb(int, int) dict;
    for(int i = 1; i <= n; ++i) {
        cin >> a[i];
    }
    a[0] = 0;
    a[n + 1] = x;
    vector<int> idx(n + 2);
    iota(idx.begin(), idx.end(), 0);
    ranges::sort(idx, {}, [&](const auto& i) { return a[i]; });
    for(int i = 0; i <= n + 1; ++i) {
        dict[a[idx[i]]] = i;
    }
    
    vector<int> left(n + 2), right(n + 2);
    iota(left.begin(), left.end(), -1);
    iota(right.begin(), right.end(), 1);
    left[0] = 0;
    right.back() = n + 1;
    vector<int> ans(n);
    int mx = 0;
    for(int i = 1; i <= n + 1; ++i) {
        chmax(mx, a[idx[i]] - a[idx[i - 1]]);
    }
    for(int i = n; i >= 1; --i) {
        int p = dict[a[i]];
        ans[i - 1] = mx;
        right[left[p]] = right[p];
        left[right[p]] = left[p];
        int pre = a[idx[left[p]]];
        int nxt = a[idx[right[p]]];
        
        chmax(mx, nxt - pre);
    }
    for(auto &p: ans) cout<<p<<" ";
}
```

### 16. Distinct Values Subarrays

滑動窗口

```cpp
void solve() {
    int n;
    cin >> n;
    vector<int> a(n);
    for(int i = 0; i < n; ++i) cin >> a[i];
    htb(int, int) dict;
    ll ans = 0;
    for(int l = 0, r = 0; r < n; ++r) {
        int x = a[r];
        dict[x]++;
        while(l <= r && dict[x] > 1) {
            dict[a[l++]]--;
        }
        ans += (r - l + 1);
    }
    cout<<ans<<endl;
}

```

利用貢獻法，計算 x 和左端點內的元素可以產生的貢獻

```cpp
void solve() {
    int n;
    cin >> n;

    htb(int, int) last_pos;
    int left_pos = 1;
    ll count = 0;

    for (int i = 1; i <= n; i++) {
        int x;
        cin >> x;
        left_pos = max(left_pos, last_pos[x] + 1);
        count += i - left_pos + 1;
        last_pos[x] = i;
    }

    cout << count << "\n";
}
```

### 17. Distinct Values Subsequences

這題沒做出來，一直想用貢獻法或DP的方式計算，但是對於重複元素的貢獻一直沒辦法很好的去重。

這題需要使用完全不同的方向，假設元素皆不相同計算子序列總數的方式為每個元素有"在子序列"跟"不在子序列"兩種可能，所以可以得到 $2^n - 1$ 種可能。

而對於有重複元素的情況，雖然位置完全不一樣，我們可以把子序列當作一個子集，所以我們只能從重複元素中選一個加入子集或是不加入子集，假設有 m 個不同元素 $cnt(x_i)$ 為重複元素 $x_i$ 的個數，最後答案為 $\prod_{i = 1}^{m} cnt(x_i) + 1$ 。

```cpp
const int MOD = 1'000'000'000 + 7;
void solve() {
    int n, x;
    cin >> n;
    htb(int, int) dict;
    readv2(n, x, dict[x]++);
    ll ans = 1;
    for(auto &[_, cnt]: dict) {
        ans = (ans * (cnt + 1)) % MOD;
    }
    cout<<ans - 1<<endl;
}
```

### 18. Josephus Problem I

約瑟夫問題，用 linked list 解，也可以用 queue, deque。

```cpp
void solve() {
    int n;
    cin >> n;
    deque<int> dq;
    for(int i = 1; i <= n; ++i) {
        dq.pb(i);
    }
    while(!dq.empty()) {
        int x = dq.front();
        dq.pop_front();
        if(dq.size() > 0) {
            int y = dq.front();
            dq.pop_front();
            cout<<y<<" ";
            dq.pb(x);
        } else {
            cout<<x;
        }
    }    
}
```

```cpp
void solve() {
    int n;
    cin >> n;
    vector<int> nxt(n + 1, -1);
    iota(nxt.begin(), nxt.end(), 1);
    nxt[n] = 1;
    int cur = 1;
    while(nxt[cur] > 0) {
        cout<<nxt[cur]<<" ";
        int j = nxt[cur];
        nxt[cur] = nxt[j];
        cur = nxt[cur];
        nxt[j] = 0;
    }
}
```

---

### 19. Josephus Problem II

有幾種寫法:

1. 線段樹/BIT 二分 $O(n\log{(n)})$

用長為 n + 1 的陣列表示哪寫元素還在數列中，可以透過觀察發現每次取第 idx + k 大的元素，若是超過當前數列長度 sz 需要模 sz

可以得到 $idx = idx + k \pmod{size}$，如果 $idx + k = sz$ 會取到第 0 大元素，所以要對前式進行變形 $idx = idx + k - 1 \pmod{size} + 1$

```cpp
class Fenwick {
    vector<int> tree;
    int sz;
public:
    Fenwick(int n): tree(n + 1, 0), sz(n) {}
    void update(int i, int val) {
        for(; i < tree.size(); i += (i & -i)) {
            tree[i] += val;
        }
    }
    int kth(int k) {
        int idx = 0;
        int step = 1;
        int hi = 1 << bit_width((unsigned)sz) - 1;
        for(; hi; hi >>= 1) {
            int nxt = idx + hi;
            if(nxt <= sz && tree[nxt] < k) {
                k -= tree[nxt];
                idx = nxt;
            }
        }
        return idx + 1;
    }
};

void solve() {
    int n, k;
    cin >> n >> k;
    Fenwick t(n);
    for(int i = 1; i <= n; ++i) t.update(i, 1);
    int cur = 1;
    int left = n;
    while(left) {
        cur = (cur + k - 1) % left + 1;
        int pos = t.kth(cur);
        cout<<pos<<" ";
        t.update(pos, -1);
        left--;
    }
}
```

2. 分塊 $O(n\sqrt{n})$

把長度為 n 的陣列切成 $\sqrt{n}$ 的大小進行運算，由 $k \pmod{n - i}$ 算出要從當前 col 往後走幾步。找到對應元素後把它從當前 row 刪除($O(\sqrt{n})$。

```cpp
void solve() {
    int n, k;
    cin >> n >> k;
    int m = sqrt(n);
    vector<vector<int>> g;
    for(int i = 1; i <= n; ) {
        vector<int> tmp;
        while(tmp.size() < m && i <= n) {
            tmp.pb(i++);
        }
        g.pb(tmp);
    }
    int r = 0, c = 0;
    for(int i = 0; i < n; ++i) {
        int j = k % (n - i);
        while(j) {
            if(c + j < g[r].size()) {
                c += j;
                j = 0;
            } else {
                j -= g[r].size() - c;
                c = 0;
                r = (r + 1) % g.size();
            }
        }
        while(c >= g[r].size()) {
            c = 0;
            r = (r + 1) % g.size();
        }
        cout<<g[r][c]<<" ";
        if(i < n - 1) {
            g[r].erase(g[r].begin() + c);
        }
    }
}
```

### 20. Nested Ranges Check

這題沒有思考清楚，一開始直覺的想到開頭可以由小至大排序，但是對於結尾的部分就沒有想清楚。中間因為沒有辦法一次找到包含和被包含的區間所以卡住，雖然後面有想到做兩次遍歷分別計算，但是被包含的區間沒有想到如何計算。

這題的核心想法是要抓著題目給的性質，若是我們進行排序後，對於 $[a_i, b_i]$, $[a_{i + 1}, b_{i + 1}]$ 兩個區間，可以知道 $a_i \le a_{i + 1}$，所以在 i + 1 左端必定小於等於 i 的情況下我們可以透過右端點的關係判斷是否有被包含住，而此時左端點相同時右端點必須是由大至小排序。也就是說透過順序遍歷排序後的區間，不斷更新右端點最大值 mx 若是 $mx \ge b_i$ 則說明當前區間 i 被包含著。

包含的關係也是利用排序後 $a_i \le a_{i + 1}$ 的性質，但是透過逆序遍歷，我們可以發現假設有右端點比當前區間的右端點還要小的話，那表示當前區間包含著其他區間，因為逆序的關係可以知道 $a_i \le a_j$ ，而又有 $b_i \ge b_j$ (假設 $0 \le i < j < n$)。

[講解](https://codeforces.com/blog/entry/98629)

```cpp
void solve() {
    int n, l, r;
    cin >> n;
    vector<pii> a;
    vector<int> inner(n, 0), outer(n, 0), idx(n, 0);
    for(int i = 0; i < n; ++i)  {
        cin >> l >> r;
        a.emplace_back(l, r);
        idx[i] = i;
    }
    ranges::sort(idx, [&](const auto &x, const auto &y) {
        if(a[x].first == a[y].first) return a[x].second > a[y].second;
        return a[x].first < a[y].first;
    });
    int mn = INT_MAX;
    for(int j = n - 1; j >= 0; --j) {
        auto &[s, e] = a[idx[j]];
        if(mn <= e) {
            outer[idx[j]] = 1;
        }
        chmin(mn, e);
    }
    int mx = INT_MIN;

    for(int j = 0; j < n; ++j) {
        auto &[s, e] = a[idx[j]];
        if(mx >= e) {
            inner[idx[j]] = 1;
        }
        chmax(mx, e);
    }
    for(auto &x: outer) cout<<x<<" ";
    cout<<endl;
    for(auto &x: inner) cout<<x<<" ";
    cout<<endl;
}
```

* 另一種做法是利用 BIT 算排序後的區間，相當於下一題的特例，判斷對應的前綴和是否大於 0 。

### 21. Nested Ranges Count

基本思路跟上一題類似，分別逆序/順序遍歷排序後的區間，計算對應的前綴和。

```cpp
class Fenwick {
    vector<int> tree;
public:
    Fenwick(int n): tree(n + 1) {}
    void clear() {
        fill(tree.begin(), tree.end(), 0);
    }
    void update(int i, int val) {
        for(; i < tree.size(); i += (i & -i)) {
            tree[i] += val;
        }
    }
    int pre(int i) {
        int res = 0;
        for(; i; i -= (i & -i)) {
            res += tree[i];
        }
        return res;
    }
};

void solve() {
    int n;
    map<int, int> disc;
    cin >> n;
    vector<pii> intervals(n);
    vector<int> inner(n), outer(n), idx(n);
    Fenwick t(2 * n);
    for(int i = 0; i < n; ++i) {
        auto &[l, r] = intervals[i];
        cin >> l >> r;
        disc[l] = 0;
        disc[r] = 0;
        idx[i] = i;
    }
    int sz = 0;
    for(auto &[_, i]: disc) i = sz++;
    for(auto &[l, r]: intervals) {
        l = disc[l];
        r = disc[r];
    }
    ranges::sort(idx, [&](const auto &a, const auto &b) {
        auto &[la, ra] = intervals[a];
        auto &[lb, rb] = intervals[b];
        if(la == lb) return ra > rb;
        return la < lb;
    });
    
    for(int i = n - 1; i >= 0; --i) {
        int j = idx[i];
        auto &[l, r] = intervals[j];
        outer[j] = (t.pre(r));
        t.update(r, 1);
    }
    t.clear();
    int sum = 0;
    for(auto &j: idx) {
        auto &[l, r] = intervals[j];
        inner[j] = (sum - t.pre(r));
        t.update(r + 1, 1);
        sum++;
    }
    for(auto &x: outer) cout<<x<<" ";
    cout<<endl;
    for(auto &x: inner) cout<<x<<" ";
    cout<<endl;
}
```

### 22. Room Allocation

經典 schedule 問題，用優先佇列解。

```cpp
void solve() {
    int n;
    cin >> n;
    vector<tiii> times(n);
    for(int i = 0; i < n; ++i) {
        auto &[l, r, idx] = times[i];
        cin >> l >> r;
        idx = i;
    }
    int sz = 1, cur = 0;
    priority_queue<pii> occupied;
    ranges::sort(times);
    vector<int> ans(n);
    for(auto &[l, r, idx]: times) {
        chmax(cur, l);
        if(!occupied.empty() && -occupied.top().first < cur) {
            auto [_, i] = occupied.top();
            occupied.pop();
            ans[idx] = i;
            occupied.emplace(-r, i);
        } else {
            ans[idx] = sz;
            occupied.emplace(-r, sz++);
        }
    }
    cout<<sz - 1<<endl;
    for(auto &x: ans) cout<<x<<" ";
}
```

### 23. Factory Machines

二分，注意上下界，以及值域範圍

```cpp
void solve() {
    ll n, t;
    cin >> n >> t;
    vector<int> a(n);
    readv(n, a[i]);
    auto [mn, mx] = ranges::minmax(a);
    ll l = t / n * mn - 1, r = (t - 1 + n)/ n * mx + 1;
    auto check = [&](ll limit) -> ll {
        ll cnt = 0;
        for(auto &x: a) {
            cnt += limit / x;
        }
        return cnt >= t;
    };
    while(l + 1 < r) {
        ll m = l + (r - l) / 2;
        (check(m) ? r : l) = m;
    }
    cout<<r<<endl;
}
```