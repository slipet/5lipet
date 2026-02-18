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

因此可以使用矩陣快速冪計算，時間複雜度為 $O(m^3\log{(n)}) \text{ , for } m = 2$

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

* Kitamasa

時間複雜度 $O(k^2\log{(n)})$，這裡 k = 2。這個方法比矩陣快速冪更快。

把上面矩陣表達式寫成如下:

$$
x_{n+1} = 
\begin{bmatrix}
f_{n + 1} \\
g_{n + 1}
\end{bmatrix}
= A
\begin{bmatrix}
f_{n} \\
g_{n}
\end{bmatrix}
= Ax_n
，A=
\begin{bmatrix}
4 & 1\\
1 & 2
\end{bmatrix}
，F_n = f_n + g_n
$$

這題所求的是 $F_n$，但是 kitamsa 要使用遞迴式的形式，ex: fibonacci $F(n) = F(n - 1) + F(n - 2)$。

由特徵多項式可得:

$$
det(\lambda I - A) = det
\begin{bmatrix}
\lambda -4 & -1\\
-1 & \lambda -2
\end{bmatrix}
=
(\lambda - 4)(\lambda - 2) - 1
=
\lambda^2 - 6\lambda + 7I
$$

由 Cayley–Hamilton:

$$A^2 - 6A + 7I = 0$$

乘上 $F_n = c^Tx_n$。($c^Tx_n$ 是用一組權重 $c = \begin{bmatrix} \alpha \\ \beta\end{bmatrix}$，對狀態向量 $x_n = \begin{bmatrix} f_n \\ g_n\end{bmatrix}$ 做線性加權，得到真正關心的輸出 $F_n = \begin{bmatrix} 1 & 1 \end{bmatrix} \begin{bmatrix} f_n \\ g_n\end{bmatrix}$)

\[
\begin{array}{ll}
    A^2 - 6A + 7I = 0 \\
    \rightarrow (A^2 - 6A + 7I)x_n = 0\\ 
    \rightarrow A^2x_n - 6Ax_n + 7Ix_n = 0
\end{array}
\]

由前面可知 $x_{n+1} = Ax_n$，$x_{n+2}=A^2x_n$

\[
\begin{array}{ll}
    x_{n + 2} - 6x_{n+1} + 7x_n = 0 \\
    \rightarrow F_{n+2} - 6F_{n+1} + 7F_{n} = 0 \\
    \rightarrow F_{n} = 6F_{n-1} - 7F_{n - 2}
\end{array}
\]


```cpp
const int MOD = 1'000'000'007;
//f(n) = coef[k - 1] * f[n - 1] + coef[k - 2] * f[n - 2] + ... +  coef[0] * f[n - k]
//init val f(i) = a[i] (0 <= i < k)
//return f(n) % MOD
//O(k^2 log n)
int qpow(int x, long long n) {
    int res = 1;
    while(n) {
        if(n & 1) res = (res * x) % MOD;
        x = (x * x) % MOD;
        n >>= 1;
    }
    return res;
}
int kitamasa(const vector<int>& coef,const vector<int>& a, long long n) {
    if(n < a.size()) {
        return a[n] % MOD;
    }
    int k = coef.size();
    if(k == 0) return 0;
    if(k == 1) return 1LL * a[0] * qpow(coef[0], n) % MOD;
    auto compose = [&](const vector<int>& A, vector<int> B) -> vector<int> {
        vector<int> C(k);
        for (int v : A) {
            for (int j = 0; j < k; j++) {
                C[j] = (C[j] + 1LL * v * B[j]) % MOD;
            }
            int bk1 = B.back();
            for (int i = k - 1; i > 0; i--) {
                B[i] = (B[i - 1] + 1LL * bk1 * coef[i]) % MOD;
            }
            B[0] = 1LL * bk1 * coef[0] % MOD;
        }
        return C;
    };
    vector<int> res_c(k), c(k);
    res_c[0] = c[1] = 1;
    for (; n > 0; n /= 2) {
        if (n % 2) {
            res_c = compose(c, move(res_c));
        }
        c = compose(c, c);
    }
    long long ans = 0;
    for (int i = 0; i < k; i++) {
        ans = (ans + 1LL * res_c[i] * a[i]) % MOD;
    }

    return (ans + MOD) % MOD;
}

void solve() {
    int n;
    cin >> n;
    vector<int> coef = {-7, 6};//f(n) = - 7f(n - 2) + 6f(n - 1);
    vector<int> a = {2, 8};//F0 F1
    cout<<kitamasa(coef, a, n - 1) <<endl;
}
```

### 10. Edit Distance

本質上是 LCS 但是沒有仔細想清楚，一開始想用最長的字串長度減去 LCS 的長度當作操作的次數，但是沒有考慮到像 s = 'NGPYCNPO', t = 'UQPXWVLGHC'，這樣的例子 LCS 為 'PC' ，但是除了 P 之外的字符都可以直接增加或是替換得到答案，但若是使用前面的想法會需要額外操作。

這題的轉移方程如下，定義 $f(i, j)$ 為當字串 s 長度為 i 且字串 t 長度為 j 時，需要的操作數使得 s 等於 t。

if $s_i = t_j$

$$f(i + 1, j + 1) = f(i, j)$$

else

$$f(i + 1, j + 1) = min(f(i, j), f(i + 1, j), f(j, j + 1)) + 1$$

初始值為:

\[
\left\{
\begin{array}{ll}
    f(0, 0) = 0 \\
    f(0, j + 1) = j + 1 \\
    (i + 1, 0) = i + 1
\end{array}
\right.
\]

```cpp
void solve() {
    string s, t;
    cin >> s;
    cin >> t;
    const int n = s.length();
    const int m = t.length();
    vector f(2, vector<int>(m + 1, inf));
    for(int j = 0; j < m; ++j) f[0][j + 1] = j + 1;
    f[0][0] = 0;
    for(int i = 0; i < n; ++i) {
        f[(i + 1) % 2][0] = i + 1;
        for(int j = 0; j < m; ++j) {
            int &res = f[(i + 1) % 2][j + 1];
            if(s[i] == t[j]) {
                res = f[i % 2][j];
            } else {
                res = min({f[i % 2][j], f[i % 2][j + 1], f[(i + 1) % 2][j]}) + 1;
            }
        }
    }
    cout<<f[n % 2][m]<<endl;
}
```

### 11. Longest Common Subsequence

定義 $f(i, j)$ 為當字串 s 長度為 i 且字串 t 長度為 j 時的 LCS。

if $s_i = t_j$

$$f(i + 1, j + 1) = max(f(i + 1, j + 1), f(i, j))$$

else

$$f(i + 1, j + 1) = min(f(i + 1, j), f(j, j + 1))$$

```cpp
void solve() {
    int n, m;
    cin >> n >> m;
    vector<int> a(n), b(m);
    vector f(n + 1, vector<int>(m + 1, 0));
    readv(n, a[i]);
    readv(m, b[i]);
    
    for(int i = 0; i < n; ++i) {
        for(int j = 0; j < m; ++j) {
            if(a[i] == b[j]) {
                chmax(f[i + 1][j + 1], f[i][j] + 1);
            } else {
                f[i + 1][j + 1] = max(f[i + 1][j], f[i][j + 1]);
            }
        }
    }
    cout<<f[n][m]<<endl;

    int i = n, j = m;
    vector<int> ans;
    while(i && j) {
        if(f[i][j] == f[i - 1][j - 1] + 1 && a[i - 1] == b[j - 1]) {
            ans.push_back(a[i - 1]);
            i--;
            j--;
        } else {
            if(f[i][j] == f[i - 1][j]) {
                i--;
            } else {
                j--;
            }
        }
    }
    for(int i = ans.size() - 1; i >= 0; --i) {
        cout<<ans[i]<<" ";
    }
}
```

### 12. Rectangle Cutting

這題一開始直覺地想到 $O(n^3)$ 的解法，但是因為超過 $10^9$ 所以有點不敢寫，後來看到解答有利用對稱性將複雜度下降成 $O(\frac{n^3}{2})$

```cpp
void solve() {
    int n, m;
    cin >> n >> m;
    int mx = max(m, n);
    vector f(n + 1, vector<int>(m + 1, inf));
    for(int i = 1; i <= min(n, m); ++i) {
        f[i][i] = 0;
    }
    for(int len = 1; len <= n; ++len) {
        for(int width = 1; width <= m; ++width) {
            int &res = f[len][width];
            if(len == width) continue;
            for(int i = 1; i <= len / 2; ++i) {
                res = min(res, f[i][width] + f[len - i][width]);
            }
            for(int i = 1; i <= width / 2; ++i) {
                res = min(res, f[len][i] + f[len][width - i]);
            }
            res++;
        }
    }
    cout<<f[n][m]<<endl;
}
```

### 13. Minimal Grid Path

這題沒辦法使用簡單的貪心去選擇走訪方向是因為有可能會遇到相同的字母，此時無法貪心的選擇比較小的字母，沒辦法由當前位置確認字典序更小的方向。

```
在下面的例子可以發現在 (0, 0) 時沒辦法決定該往右還是往下，因為決定字典序更小的位置在 (0, 2), (1, 1), (2, 0)
A A B
A C A
D A A
```

觀察會發現從 (0, 0) 至反對角線位置的上構成的字串長度會一樣:

```
//從 (0, 0) 至 (0, 2), (1, 1), (2, 0) 所能構成的字串長度皆為 3
A B C
D E F
G H I
```

因此思考的核心會是由一層


```cpp
vector<pii> dirs = {{0, 1}, {1, 0}};
void solve() {
    int n;
    cin >> n;
    vector<string> row(n);
    readv(n, row[i]);
    vector<pii> cur = {{0, 0}};
    vector used(n, vector<int>(n, 0));
    cout<<row[0][0];
    for(int diag = 1; diag < 2 * n - 1; ++diag) {
        vector<pii> nxt;
        for(auto &[r, c]: cur) {
            for(auto &[dr, dc]: dirs) {
                int nr = r + dr, nc = c + dc;
                if(nr >= n || nc >= n) continue;
                if(used[nr][nc]) continue;
                used[nr][nc] = 1;
                auto update = [&](int y, int x) -> void {
                    if(!nxt.empty()) {
                        if(row[nxt.back().first][nxt.back().second] > row[y][x]) {
                            nxt.clear();
                        } else if(row[nxt.back().first][nxt.back().second] < row[y][x]) {
                            return;
                        }
                    }
                    nxt.emplace_back(y, x);
                };
                update(nr, nc);
            }
        }
        cout<<row[nxt[0].first][nxt[0].second];
        cur.clear();
        char pre = 0;
        for(auto &[r, c]: nxt) {
            if(pre > 0 && row[r][c] != pre) {
                break;
            }
            cur.emplace_back(r, c);
            pre = row[r][c];
        }
    }
    cout<<endl;
}
```

這題一開始的想會是由 DP 的方式出發，從原點開始，每次考慮上面和左邊的字串，會有如下的轉移方程，$f(i, j)$ 為原點至 $(i, j)$ 可以構成的最小字典序字串:

$$f(i, j) = min(f(i - 1, j), f(i, j - 1)) + s[i][j]$$

但是這樣會有 $O(n^3)$ 的複雜度，因為字串的比對所以多一個 n 的複雜度。

```cpp
void solve() {
    int n;
    cin >> n;
    vector<string> row(n);
    readv(n, row[i]);
    vector hash(n + 1, vector<int>(n + 1, n + 1));
    for(int diag = 2 * n - 2; diag >= 0; --diag) {
        vector<pii> v;
        for(int r = 0; r < n; r++) {
            int c = diag - r;
            if(0 <= c && c < n) {
                v.emplace_back(r, c);
            }
        }
        int k = v.size();
        vector<pii> order(k);
        for(int i = 0; i < k; ++i) {
            auto [r, c] = v[i];
            int state = (n + 2) * (row[r][c] - 'a') + min(hash[r + 1][c], hash[r][c + 1]);
            order[i] = {state, r};
        }
        ranges::sort(order);
        int nxt = 0;
        for(int i = 0; i < k; ++i) {
            if(i == 0 || order[i].first != order[i - 1].first) {
                nxt++;
            }
            int r = order[i].second;
            int c = diag - r;
            hash[r][c] = nxt;
        }
    }
    int r = 0, c = 0;
    cout<<row[r][c];
    while(r < n - 1 || c < n - 1) {
        if(hash[r + 1][c] < hash[r][c + 1]) {
            r++;
        } else c++;
        cout<<row[r][c];
    }
}
```