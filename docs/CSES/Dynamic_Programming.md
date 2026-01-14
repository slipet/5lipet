# Dynamic Programming

### 1. Dice Combinations

$F[i] =  \sum_{j = 1}^{min(i, 6)}{F[i - j]}$

```cpp
const int MOD = 1'000'000'000 + 7;
void solve() {
    int n;
    cin >> n;
    vector<int> f(n + 1);
    f[0] = 1;
    for(int i = 1; i <= n; ++i) {
        for(int j = 1; j <= min(i, 6); ++j) {
            f[i] = (f[i] + f[i - j]) % MOD;
        }
    }
    cout<<f[n]<<endl;
}
```

空間壓縮，F[i] 只跟前六個有關。

```cpp
const int MOD = 1'000'000'000 + 7;
const int sides = 6;
void solve() {
    int n, nxt;
    cin >> n;
    vector<int> f(sides);
    f[0] = 1;
    for(int i = 1; i <= n; ++i) {
        nxt = 0;
        for(int j = 1; j <= min(i, 6); ++j) {
            nxt = (nxt + f[((i - j) % sides + sides) % sides]) % MOD;
        }
        f[i % sides] = nxt;
    }
    cout<<f[n % sides]<<endl;
}
```

### 2. Minimizing Coins

$F[i] = min(F[i], F[i - c_j] + 1)$

這題要注意的地方，內外層的 for-loop 可以交換計算不影響答案，但若是內層 for-loop 為 $c_j$ 的 [1, n] 會比內層 for-loop 是 [1, x] 時還慢 => cache locality。

```cpp
void solve() {
    int n, x;
    cin >> n >> x;
    vector<int> c(n), f(x + 1, inf);
    readv(n, c[i]);
    f[0] = 0;
    for(int j = 0; j < n; ++j) {
        for(int i = 1; i <= x; ++i) {
            if(i - c[j] >= 0)
                chmin(f[i], f[i - c[j]] + 1);
        }
    }
    if(f[x] >= inf)
        cout<<-1<<endl;
    else
        cout<<f[x]<<endl;
}
```

### 3. Coin Combinations I

$F[i] = \sum_{j = 1}^{n}{F[i - c_j]}$

這題要注意的是模數的計算會非常花費時間，因此先用 long long 的 tmp 加法，最後再做模數計算可以增加整體的效能。

```cpp
const int MOD = 1'000'000'000 + 7;
void solve() {
    int n, x;
    cin >> n >> x;
    vector<int> c(n), f(x + 1);
    readv(n, c[i]);
    f[0] = 1;
    ll tmp = 0;
    for(int i = 1; i <= x; ++i) {
        tmp = 0;
        for(int j = 0; j < n; ++j) {
            if(i - c[j] >= 0) 
                tmp += f[i - c[j]];
        }
        f[i] = (tmp % MOD);
    }
    cout<<f[x]<<endl;
}
```

### 4. Coin Combinations II

這題是求組合數，因此像是 2 + 3, 3 + 2 這種重複組合要去重，透過將遍歷硬幣的 for-loop 放在外層，可以避免重複。

要注意的地方是內層 for-loop 的範圍會是 $[c_j, x]$

```cpp
const int MOD = 1'000'000'000 + 7;
void solve() {
    int n, x;
    cin >> n >> x;
    vector<int> c(n), f(x + 1);
    readv(n, c[i]);
    f[0] = 1;
    for(int j = 0; j < n; ++j) {
        for(int i = c[j]; i <= x; ++i) {
            f[i] += f[i - c[j]];
            if(f[i] >= MOD) f[i] -= MOD;
        }
    }
    cout<<f[x]<<endl;
}
```