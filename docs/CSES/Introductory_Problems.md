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

#### xor solution

不用開 long long ，避免 overflow 。

```cpp
void solve() {
    int n, x;
    cin >> n;
    long long xor = 0, all = 0;
    for(int i = 1; i <= n - 1; ++i) {
        cin >> x;
        xor ^= x;
        all ^= i;
    }
    cout<< all ^ xor ^ n <<endl;
}
```

[ref](https://hackmd.io/p53aGB5DQBGRwSCBd9Fc-Q?view#%E6%83%B3%E6%B3%95-3-2%EF%BC%9AXOR-%E7%89%88)


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

### 9. Bit Strings

```cpp
const int MOD = 1'000'000'000 + 7;
void solve() {
    int n;
    cin >> n;
    int ans = 1;
    for(int i = 0; i < n; ++i) {
        ans = (ans << 1) % MOD;
    }
    cout<< ans << endl;
}
```
### 10. Trailing Zeros

直覺地想到是要找階乘中 2 和 5 的因子個數。

* 錯誤的想法﹕

一開始直覺的思考計算　n / 10 * 2 + (n % 10 >= 5) 計算。但是這個方法會漏掉 $5^2$, $5^3$  這種數。

* Solution:

接著思考如何得到階乘中 2 和 5 的因子個數。
對於 5 的倍數的個數可以由 $\lfloor \frac{n}{5} \rfloor$ 得到，而 $5^2$ 可以由 $\lfloor \frac{n}{25} \rfloor$ 得到。
所以對於 $n!$ 中 5 的因子個數 $cnt_5(n!)$ 可以由 $cnt_5(n!) = \lfloor \frac{n}{5} \rfloor + \lfloor \frac{n}{5^2} \rfloor + \lfloor \frac{n}{5^3} \rfloor ...$

同理，所以對於 $n!$ 中 2 的因子個數 $cnt_2(n!)$ 可以由 $cnt_2(n!) = \lfloor \frac{n}{2} \rfloor + \lfloor \frac{n}{2^2} \rfloor + \lfloor \frac{n}{2^3} \rfloor ...$

我們要找的是 2 * 5 的個數為 $min(cnt_5(n!), cnt_2(n!))$

```cpp
void solve() {
    int n;
    cin >> n;
    long long pow5 = 1, pow2 = 1;
    int cnt5 = 0, cnt2 = 0;
    while(pow5 * 5 <= n) {
        pow5 *= 5;
        cnt5 += (n / pow5);
    }
    while(pow2 * 2 <= n) {
        pow2 *= 2;
        cnt2 += (n / pow2);
    }
    cout << min(cnt2, cnt5) << endl;
}
```

由其他人的寫法發現直接計算 $cnt_5(n!)$ 就可以，一開始我也直覺地想到 $n!$ 中 5 的因子個數 $cnt_5(n!)$ 直覺上會少於 $cnt_2(n!)$，但是寫題目的時候懶得仔細去思考證明，所以同時計算 2 和 5 並且取最小值。

證明:

由上述可知:

$cnt_5(n!) = \lfloor \frac{n}{5} \rfloor + \lfloor \frac{n}{5^2} \rfloor + \lfloor \frac{n}{5^3} \rfloor + ... + \lfloor \frac{n}{5^k} \rfloor, \quad k \le \log_5{(n)}$

$cnt_2(n!) = \lfloor \frac{n}{2} \rfloor + \lfloor \frac{n}{2^2} \rfloor + \lfloor \frac{n}{2^3} \rfloor + ... + \lfloor \frac{n}{2^p} \rfloor, \quad p \le \log_2{(n)}$

可以寫成:

\[
\left\{ 
\begin{array}{ll}
    cnt_5(n!) = n\sum_{i = 1}^{k} \lfloor \frac{1}{5^i} \rfloor\\
    cnt_2(n!) = n\sum_{j = 1}^{p} \lfloor \frac{1}{2^j} \rfloor
\end{array}
\right.
\]

觀察當項數 i = j = 1, 2, ... 時，$\frac{1}{5} \le \frac{1}{2}$, $\frac{1}{5^2} \le \frac{1}{2^2}$ ...。

且 $k \le p$，表示 $cnt_2(n!)$ 的項數會比 $cnt_5(n!)$ 多，也就可以知道 $cnt_5(n!) \le cnt_2(n!)$。


```cpp
void solve() {
    int n;
    cin >> n;
    int cnt = 0;
    while(n >= 5) {
        cnt += (n / 5);
        n /= 5;
    }
    cout << cnt << endl;
}
```

### 11. Coin Piles

解聯立方程即可。

```cpp
void solve() {
    int a, b;
    cin >> a >> b;
    
    int x = 2 * a - b;
    int y = 2 * b - a;
    if(x < 0 || y < 0 || x % 3 != 0 || y % 3 != 0)
        cout<<"NO";
    else
        cout<<"YES";

    cout<<endl;
}
```

### 12. Palindrome Reorder


```cpp
void solve() {
    string s;
    cin >> s;
    unordered_map<char, int> fq;
    for(auto &c: s) fq[c]++;
    int odd = 0;
    char mid = 0;
    for(auto &[x, c]: fq) {
        if(c & 1) {
            odd++;
            mid = x;
        }
    }
    if(odd > 1) {
        cout<<"NO SOLUTION"<<endl;
        return;
    }
    string half = "";
    for(auto &[x, c]: fq) {
        if(c / 2)
            half += string(c / 2, x);
    }
    string rev = half;
    ranges::reverse(rev);
    if(odd) {
        cout<<half + mid + rev <<endl;
    } else {
        cout<<half + rev <<endl;
    }
}
```