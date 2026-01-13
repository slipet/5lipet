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