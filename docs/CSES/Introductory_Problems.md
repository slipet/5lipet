# Introductory Problems

### 1. Weird Algorithm

簡單模擬

* <span style="color:red"> 沒有注意到乘 3 可能導致的 overflow </span>

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

### 4. Increasing Array

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

### 5. Permutations

* 注意 1 是合法的答案

```cpp
void solve() {
    int n;
    cin >> n;
    if(n > 1 && n <= 3) {
        cout<<"NO SOLUTION";
    } else {
        int odd = n - ((n & 1) == 0);
        int even = n - (n & 1);

        for(int i = odd; i > 0; i -= 2) {
            if(i != odd) cout<<' ';
            cout<<i;
        }
        for(int i = even; i > 0; i -= 2) {
            cout<<' ';
            cout<<i;
        }
    }
    cout<<endl;
}
```

### 6. Number Spiral

```cpp
void solve() {
    long long x, y;
    cin >> y >> x;
    long long mx = max(x, y);
    long long base = mx * mx - (mx - 1);
    long long ans;
    if(mx == y) {
        ans = base + (mx & 1 ? -1 : 1) * (mx - x);
    } else {
        ans = base + (mx & 1 ? 1 : -1) * (mx - y);
    }
    cout<<ans<<endl;
}
```

### 7. Two Knights

* <span style="color:red"> 沒有注意到 square * (square - 1) 可能導致的 overflow </span>

```cpp
void solve() {
    long long n;
    cin >> n;
    for(int k = 1; k <= n; ++k) {
        long long square = k * k;
        long long w = k - 1;
        long long h = k - 2;
        cout << square * (square - 1) / 2 - w * h * 2 * 2 << endl;
    }
}
```

### 8. Two Sets

* <span style="color:red"> 沒有注意到正確時要輸出 "YES" </span>

```cpp
void solve() {
    long long n;
    cin >> n;
    long long sum = (n + 1) * n / 2;
    if(sum & 1) {
        cout<<"NO";
    } else {
        vector<int> left, right;
        long long half = sum / 2;
        for(int i = n; i; --i) {
            if(half >= i) {
                left.push_back(i);
                half -= i;
            } else{
                right.push_back(i);
            }
        }
        cout<<"YES"<<endl;
        cout<<left.size()<<endl;
        for(int i = 0; i < left.size(); ++i) {
            if(i) cout<<' ';
            cout<<left[i];
        }
        cout<<endl;
        cout<<right.size()<<endl;
        for(int i = 0; i < right.size(); ++i) {
            if(i) cout<<' ';
            cout<<right[i];
        }
    }
    cout<<endl;
}
```