# Permutation/Combination

### 組合數/Combination

* 從 m 個不同元素中取 n 個，順序沒差

$$C(m, n) = \frac{m!}{n!(m -n)!} = \binom{m - 1}{n} + \binom{m - 1}{n - 1} = \binom{m}{n - 1}\frac{m + 1 - n}{n}$$

正確公式表示：

* **不選第 m 個元素**：則從前 $m-1$ 個中選 $n$ 個 → $C(m-1, n)$
* **選第 m 個元素**：則從前 $m-1$ 個中選 $n-1$ 個 → $C(m-1, n-1)$

#### 模逆元/Modular Multiplicative Inverse

```cpp
const int MOD = 1'000'000'000 + 7;
const int MX = 1'000'00;

ll F[MX + 1];
ll INV_F[MX + 1];

ll qpow(ll x, int n) {
    ll res = 1;
    while(n) {
        if(n & 1) res = (res * x) % MOD;
        x = (x * x) % MOD;
        n >>= 1;
    }
    return res;
}

auto init = [] {
    F[0] = 1;
    for(int i = 1; i <= MX; ++i) {
        F[i] = (F[i - 1] * i) % MOD;
    }
    INV_F[MX] = qpow(F[MX], MOD - 2);
    for(int i = MX; i; --i) {
        INV_F[i - 1] = (INV_F[i] * i) % MOD;
    }
    return 0;
}();

//從 m 個數中選 n 個數的方案數
ll comb(int m, int n) {
    return n < 0 || n > m ? 0 : F[m] * INV_F[n] % MOD * INV_F[m - n] % MOD;
}
```

#### 疊代計算

$$C(m, n) = \binom{m - 1}{n} + \binom{m - 1}{n - 1}$$

```cpp
const int MX = 66;
ll comb[MX + 1][MX + 1];
auto init = []() {
    memset(comb, 0, sizeof(comb));
    for(int i = 0; i <= MX; ++i) {
        comb[i][0] = 1;
        for(int j = 1; j <= i; ++j) {
            comb[i][j] = comb[i - 1][j] + comb[i - 1][j - 1];
        }
    }
    return 0;
}();
```

#### 原地計算

$$C(m, n) = \binom{m}{n - 1}\frac{m + 1 - n}{n}$$

由於等式左邊 C(m, n) 是整數所以右邊一定能整除

ex.  $\binom{9}{4} = \frac{9 * 8 * 7 * 6}{1 * 2 * 3 * 4} = \binom{9}{3}\frac{9 + 1 - 4}{4} = \binom{9}{2}\frac{6}{4} * \frac{9 + 1 - 3} {3} = \binom{9}{1} \frac{6}{4} * \frac{7}{3} * \frac{9 + 1 - 2} {2} = \frac{6}{4} * \frac{7}{3} * \frac{8}{2} * \frac{9}{1}$ 
 

可以先計算 $\frac{9}{1}$ ， 然後計算 $\frac{9 * 8}{1 * 2} = 36$，然後 $\frac{36 * 7}{3} = 84$ ， 最後  $\frac{84 * 6}{4} = 126$

```cpp
ll comb(int m, int n) {
    n = min(n, m - n);
    ll res = 1;
    for(int i = 1; i <= n; ++i) {
        res = res * (m + 1 - i) / i;
    }
    return res;
}

```

#### Hockey-stick identity

計算:

$$\binom{m}{n} + \binom{m + 1}{n} + ... + \binom{m + k}{n} = \sum_{i = 0}^{k} \binom{m + i}{n}$$

由上面可以知道:

\[
\begin{array}{ll}
    \binom{m}{n} = \binom{m - 1}{n} + \binom{m - 1}{n - 1}\\
    \rightarrow \binom{m - 1}{n - 1} = \binom{m}{n} - \binom{m - 1}{n} \\
    \rightarrow \binom{m}{n} = \binom{m + 1}{n + 1} - \binom{m}{n + 1}
\end{array}
\]

所以對於 $\sum_{i = 0}^{k} \binom{m + i}{n}$ 可以寫成:

\[
\begin{array}{ll}
    \binom{m}{n} &= \binom{m + 1}{n + 1} - \binom{m}{n + 1} \\
    \binom{m + 1}{n} &= \binom{m + 2}{n + 1} - \binom{m + 1}{n + 1} \\
    ...
    \binom{m + k}{n} &= \binom{m + k + 1}{n + 1} - \binom{m + k}{n + 1} \\
    \sum_{i = 0}^{k} \binom{m + i}{n} &= \binom{m + 1}{n + 1} - \binom{m}{n + 1} + \binom{m + 2}{n + 1} - \binom{m + 1}{n + 1} + ... + \binom{m + k + 1}{n + 1} - \binom{m + k}{n + 1} \\
    &= \binom{m + k + 1}{n + 1} - \binom{m}{n + 1}
\end{array}
\]

### Permutation

* 從 m 個不同元素中取 n 個，順序有差

$$A(m, n) = \frac{m!}{(m-n)!} = C(m, n) \times n!$$

例：從 5 個人中挑 2 個排座位: $A(5,2) = 20$