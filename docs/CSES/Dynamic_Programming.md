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

### 5. Removing Digits

$F[x] = min(F[x - d_i]) + 1$，$d_i$ 是 x 的 digit。

```cpp
void solve() {
    int n;
    cin >> n;
    vector<int> f(10, inf);
    f[0] = 0;
    for(int i = 1; i <= n; ++i) {
        int x = i;
        int nxt = inf;
        while(x) {
            int d = x % 10;
            if(d) chmin(nxt, f[((i - d) % 10 + 10) % 10] + 1);
            x /= 10;
        }
        f[i % 10] = nxt;
    }
    cout<<f[n % 10]<<endl;
}
```

### 6. Grid Paths I

```cpp
const int MOD = 1'000'000'000 + 7;
vector<pii> dirs = {{0, 1}, {1, 0}};
void solve() {
    int n;
    cin >> n;
    string row;
    vector f(n + 1, 0);
    f[1] = 1;
    for(int i = 0; i < n; ++i) {
        cin >> row;
        for(int j = 0; j < n; ++j) {
            if(row[j] == '*') {
                f[j + 1] = 0;
            } else {
                f[j + 1] = (f[j + 1] + f[j]) % MOD;
            }
        }
    }
    cout<<f[n]<<endl;
}
```

### 7. Book Shop

限制個數背包，每個物品只能選一次，所以內層迴圈使用 $[x, prices_i]$。注意最後輸出 F[X]，陣列初始化是 0。

$F[x] = max(F[x], F[x - prices_j] + pages_j)$

```
void solve() {
    int n, x;
    cin >> n >> x;
    vector<int> prices(n), pages(n);
    readv(n, prices[i]);
    readv(n, pages[i]);
    vector<int> f(x + 1, 0);
    f[0] = 0;
    for(int i = 0; i < n; ++i) {
        for(int j = x; j >= prices[i]; --j) {
            chmax(f[j], f[j - prices[i]] + pages[i]);
        }
    }
    cout<<f[x]<<endl;
}
```

### 8. Array Description

F[i][j] 定義為長度為 i 時以 j 為結尾的序列的個數。 

因此轉移方程: 

$f[i][j] = \sum_{k = -1}^{1}{F[i - 1][j + k]}$

1. 當 j > 0 時只要計算 f[i][j]。

2. 當 j == 0 計算 $F[i][1:m]$。

要注意的是若是序列為 ... 0, j_i,  j_{i + 1}, 0 ...，且 abs(j_i - j_{i + 1}) > 1 表示無法構成合法序列，答案為 0。

```cpp
const int MOD = 1'000'000'000 + 7;
void solve() {
    int n, m, x;
    cin >> n >> m;
    vector<ll> f(m + 2, 0);
    cin >> x;
    if(x) {
        f[x] = 1;
    } else {
        ranges::fill(f.begin() + 1, f.end() - 1, 1);
    }
    int pre = x;
    for(int i = 1; i < n; ++i) {
        cin >> x;
        vector<ll> nxt(m + 2, 0);
        if(x) {
            if(pre > 0 && abs(x - pre) > 1) {
                cout<<0<<endl;
                return;
            }
            nxt[x] = (f[x - 1] + f[x] + f[x + 1]) % MOD;
        } else {
            for(int j = 1; j <= m; ++j) {
                nxt[j] = (f[j - 1] + f[j] + f[j + 1]) % MOD;
            }
        }
        f = move(nxt);
        pre = x;
    }
    if(x) {
        cout<<f[x]<<endl;
    }
    else {
        ll sum = reduce(f.begin(), f.end(), 0LL) % MOD;
        cout<<sum<<endl;
    }   
}
```

### 9. Counting Towers

觀察最後兩層的圖形會有下面這些 pattern:

```cpp
/*
0:      1:      2:      3:       4:       5:      6:     7:
 _ _     _ _     ___     _ _      _ _      _ _     ___     ___
|_|_|   |_|_|   |___|   |_| |    | |_|    | | |   |___|   |   |                                    
|_|_|   |___|   |_|_|   |_|_|    |_|_|    |_|_|   |___|   |___|                           

*/
```

所有的 tower 都是由這些 pattern 串接而成，例如

```cpp
/*

          4     +   3
 _ _      _ _ 
| |_|    | |_|    
|_| |  = |_|_|  +   _ _ 
|_|_|              |_| |
                   |_|_|
*/
```

觀察每個 pattern 頂部的兩格能否跟上方 pattern 的底層兩格連接會有如下的轉移方程:

\[
\left\{ 
\begin{array}{ll}
    f[0] = f[0] + f[2] + f[3] + f[4] + f[5] \\
    f[1] = f[0] + f[2] + f[3] + f[4] + f[5] \\
    f[3] = f[0] + f[2] + f[3] + f[4] + f[5] \\
    f[4] = f[0] + f[2] + f[3] + f[4] + f[5] \\
    f[5] = f[0] + f[2] + f[3] + f[4] + f[5] 
\end{array}
\right.
\left\{ 
\begin{array}{ll}
    f[2] = f[1] + f[6] + f[7] \\
    f[6] = f[1] + f[6] + f[7] \\
    f[7] = f[1] + f[6] + f[7] 
\end{array}
\right.
\]

接著化簡:

\[
\left\{ 
\begin{array}{ll}
    f[0] = f[0] + f[2] + f[3] + f[4] + f[5] \\
    f[5] = f[4] = f[3]= f[1] = f[0]
\end{array}
\right.
\left\{ 
\begin{array}{ll}
    f[2] = f[1] + f[6] + f[7] \\
    f[7] = f[6] = f[2]
\end{array}
\right.
\]

消除重複項:

\[
\left\{ 
\begin{array}{ll}
    f[0] = 4 \times f[0] + f[2] \\
    f[2] = f[0] + 2 \times f[2] 
\end{array}
\right.
\]

調整一下編號:

\[
\left\{ 
\begin{array}{ll}
    f[0] = 4 f[0] + f[1] \\
    f[1] = f[0] + 2f[1]
\end{array}
\right.
\]

時間複雜度為 O(n)，但是如果有多個 testcase，會重複計算很多次，因此可以一開始就把全部的結果算出來，最後再回答即可。

```cpp
const int MOD = 1'000'000'000 + 7;
const int MX = 1'000'000;
ll F[MX + 1];
 
auto init = []() {
    vector<ll> f(2, 1);
    F[1] = 2;
    F[2] = 8;
    for(int i = 3; i <= MX; ++i) {
        ll f0 = f[0];
        f[0] = (f[0] * 4 + f[1]) % MOD;
        f[1] = (f0 + f[1] * 2) % MOD;
 
        F[i] = (f[0] * 5 + f[1] * 3) % MOD;
    }
    return 0;
}();
 
void solve() {
    int n;
    cin >> n;
    cout<<F[n]<<endl;
}
```

由上方的轉移方程可以得到下面的矩陣形式:

$$
\begin{bmatrix}
4 & 1\\
1 & 2
\end{bmatrix}
\begin{bmatrix}
f[0] \\
f[1]
\end{bmatrix}
= \
\begin{bmatrix}
4f[0] +  f[1]\\
 f[0] + 2f[1]
\end{bmatrix}
$$

因此可以使用矩陣快速冪計算，時間複雜度為 $O(\log{(n)})$

```cpp
const int MOD = 1'000'000'000 + 7;
using Matrix_t = vector<vector<long long>>;
Matrix_t mul(Matrix_t &a, Matrix_t &b) {
    const int m = a.size(), n = b[0].size();
    Matrix_t mat = Matrix_t(m, vector<long long>(n, 0));
    for(int i = 0; i < m; ++i) {
        for(int k = 0; k < a[i].size(); ++k) {
            for(int j = 0; j < n; ++j) {
                if(!a[i][k]) continue;
                mat[i][j] = (mat[i][j] + a[i][k] * b[k][j]) % MOD;
            }
        }
    }
    return mat;
}
Matrix_t identity_mat(int n) {
    Matrix_t mat(n, vector<long long>(n, 0));
    for(int i = 0; i < n; ++i) {
        mat[i][i] = 1;
    }
    return mat;
}
Matrix_t mat_qpow(Matrix_t &x, int n) {
    Matrix_t ans = identity_mat(x.size());
    while(n) {
        if(n & 1) ans = mul(x, ans);
        x = mul(x, x);
        n >>= 1;
    }
    return ans;
}
 
void solve() {
    int n;
    cin >> n;
    if(n == 1) {
        cout<<2<<endl;
        return;
    }
    Matrix_t M = {{4, 1}, {1, 2}};
    Matrix_t f0 = {{1}, {1}};
    M = mat_qpow(M, n - 1);
    M = mul(M, f0);
    cout<<(M[0][0] + M[1][0]) % MOD <<endl;
}
```
