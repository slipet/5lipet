# Introductory Problems

### 1. Weird Algorithm

簡單模擬

```cpp
void solve() {
    int n;
    cin >> n;
    cout<<n;
    if(n != 1) cout<<' ';
    while(n != 1) {
        if(n & 1) {
            n *= 3;
            n++;
        } else {
            n >>= 1;
        }
        cout<<n;
        if(n != 1) cout<<' ';
    }
}
```

### 2. Missing Number

原本想用排序或是hash table 但是感覺有點醜，想了一下發現可以用 need - sum = x 找不在的元素。

題目 n 的上限是 $2 * n^5$ 有可能超過 int 索性直接開 long long 算。

```cpp
void solve() {
    int n, x;
    cin >> n;
    long long sum = 0;
    long long need = 1LL * n * (n + 1) / 2;
    for(int i = 1; i <= n - 1; ++i) {
        cin >> x;
        sum += x;
    }
    cout<< need - sum <<endl;
}
```

### 3. Repetitions

基本的技巧

1. 如何處理結尾情況

2. 如何計算不同元素

```cpp
void solve() {
    string s;
    cin >> s;
    const int n = s.length();
    int cnt = 0;
    int ans = 0;
    for(int i = 0; i < n; ++i) {
        cnt++;
        if(i == n - 1 || s[i] != s[i + 1]) {
            ans = max(ans, cnt);
            cnt = 0;
        };
    }
    cout<<ans<<endl;
}
```

### 4. Repetitions

1. 要注意 overflow 的情況

2. max 的技巧可以避免 x > pre 的情況。

```cpp
void solve() {
    int n, x, pre = 0;
    long long ans = 0;
    cin >> n;
    for(int i = 0; i < n; ++i) {
        cin >> x;
        if(i > 0)
            ans += max(0, pre - x);
        if(pre <= x) pre = x;
    }
    cout<< ans << endl;
}
```