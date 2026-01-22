# Matrix Exponentiation

矩陣快速冪

時間複雜度 $O(m^3\log{(n)})$

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

//take fibonacci as example
class Solution {
public:
    int fib(int n) {
            Matrix_t M = {{1, 1}, {1, 2}};
        Matrix_t f0 = {{1}, {1}};
        M = qpow(M, n / 2);
        f0 = mul(M, f0);
        return f0[n % 2][0];
    }
};
```

## Kitamasa

時間複雜度 $O(m^2\log{(n)})$

[講解](https://zhuanlan.zhihu.com/p/1964051212304364939)

[editorial](https://codeforces.com/blog/entry/88760)

```cpp
constexpr int MOD = 1'000'000'007; // 998244353

// 给定常系数齐次线性递推式 f(n) = coef[k-1] * f(n-1) + coef[k-2] * f(n-2) + ... + coef[0] * f(n-k)  (注意 coef 的顺序)
// 以及初始值 f(i) = a[i] (0 <= i < k)
// 返回 f(n) % MOD，其中参数 n 从 0 开始
// 时间复杂度 O(k^2 log n)，其中 k 是 coef 的长度
int kitamasa(const vector<int>& coef, const vector<int>& a, long long n) {
    if (n < a.size()) {
        return a[n] % MOD;
    }

    int k = coef.size();
    // 特判 k = 0, 1 的情况
    if (k == 0) {
        return 0;
    }
    if (k == 1) {
        return 1LL * a[0] * qpow(coef[0], n) % MOD; // 快速幂代码略
    }

    // 已知 f(n) 的各项系数为 A，f(m) 的各项系数为 B
    // 计算并返回 f(n+m) 的各项系数 C
    auto compose = [&](const vector<int>& A, vector<int> B) -> vector<int> {
        vector<int> C(k);
        for (int v : A) {
            for (int j = 0; j < k; j++) {
                C[j] = (C[j] + 1LL * v * B[j]) % MOD;
            }
            // 原地计算下一组系数，比如已知 f(4) 的各项系数，现在要计算 f(5) 的各项系数
            // 倒序遍历，避免提前覆盖旧值
            int bk1 = B.back();
            for (int i = k - 1; i > 0; i--) {
                B[i] = (B[i - 1] + 1LL * bk1 * coef[i]) % MOD;
            }
            B[0] = 1LL * bk1 * coef[0] % MOD;
        }
        return C;
    };

    // 计算 res_c，以表出 f(n) = res_c[k-1] * a[k-1] + res_c[k-2] * a[k-2] + ... + res_c[0] * a[0]
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

    return (ans + MOD) % MOD; // 保证返回值非负
}
```

## Berlekamp-Massey

這邊 Berlekamp-Massey 是搭配上面的 Kitamasa ，找出給定數列中的"最小階"遞迴表示。

時間複雜度 $O(m^2)$

[講解](https://zhuanlan.zhihu.com/p/1966417899825665440)

重點在於這個等式:

$$a_i = (\sum_{j=0}^{k-1}{c_ja_{i-1-j}}) + \frac{d}{preD}(a_{preI} - \sum_{j=0}^{k'-1}{c'_ja_{preI-1-j}})$$

$preI$: 上一次錯誤發生位置

$preD$: 上一次錯誤

$d$: 這次錯誤

設 $bias = i - preI$，$\delta=\frac{d}{preD}$

* 係數列表的長度為 $max(bias + k', k)$

* 可以看出來 index 為 $bias - 1$ 的係數增加 $\delta$

* 對於 $j = 0,1,2,...,k'-1$，index 為 $bias + j$ 的係數減少 $c'\cdot \delta$

```cpp
constexpr int MOD = 1'000'000'007; // 998244353

int qpow(long long x, int n) {
    long long res = 1;
    for (; n > 0; n /= 2) {
        if (n % 2) {
            res = res * x % MOD;
        }
        x = x * x % MOD;
    }
    return res;
}

// 给定数列的前 m 项 a，返回符合 a 的最短常系数齐次线性递推式的系数 coef（模 MOD 意义下）
// 设 coef 长为 k，当 n >= k 时，有递推式 f(n) = coef[0] * f(n-1) + coef[1] * f(n-2) + ... + coef[k-1] * f(n-k)  （注意 coef 的顺序）
// 初始值 f(n) = a[n]  (0 <= n < k)
// 时间复杂度 O(m^2)，其中 m 是 a 的长度
vector<int> berlekampMassey(const vector<int>& a) {
    vector<int> coef, pre_c, tmp;
    int pre_i = -1, pre_d = 0;

    for (int i = 0; i < a.size(); i++) {
        // d = a[i] - 递推式算出来的值
        long long d = a[i];
        for (int j = 0; j < coef.size(); j++) {
            d = (d - 1LL * coef[j] * a[i - 1 - j]) % MOD;
        }
        if (d == 0) { // 递推式正确
            continue;
        }

        // 首次算错，初始化 coef 为 i+1 个 0
        if (pre_i < 0) {
            coef.resize(i + 1);
            pre_i = i;
            pre_d = d;
            continue;
        }

        int bias = i - pre_i;
        int old_len = coef.size();
        int new_len = bias + pre_c.size();
        if (new_len > old_len) { // 递推式变长了
            tmp = coef;
            coef.resize(new_len);
        }

        // 历史错误为 pre_d = a[pre_i] - sum_j pre_c[j]*a[pre_i-1-j]
        // 现在 a[i] = sum_j coef[j]*a[i-1-j] + d
        // 联立得 a[i] = sum_j coef[j]*a[i-1-j] + d/pre_d * (a[pre_i] - sum_j pre_c[j]*a[pre_i-1-j])
        // 其中 a[pre_i] 的系数 d/pre_d 位于当前（i）的 bias-1 = i-pre_i-1 处
        long long delta = d * qpow(pre_d, MOD - 2) % MOD; // qpow(pre_d, MOD - 2) 是 pre_d 的逆元
        coef[bias - 1] = (coef[bias - 1] + delta) % MOD;
        for (int j = 0; j < pre_c.size(); j++) {
            coef[bias + j] = (coef[bias + j] - delta * pre_c[j]) % MOD;
        }

        if (new_len > old_len) {
            pre_c = move(tmp);
            pre_i = i;
            pre_d = d;
        }
    }

    // 计算完后，可能 coef 的末尾有 0，这些 0 不能去掉
    // 比如数列 (1,2,4,2,4,2,4,...) 的系数为 [0,1,0]，表示 f(n) = 0*f(n-1) + 1*f(n-2) + 0*f(n-3) = f(n-2)   (n >= 3)
    // 如果把末尾的 0 去掉，变成 [0,1]，就表示 f(n) = 0*f(n-1) + f(n-2) = f(n-2)   (n >= 2)
    // 看上去一样，但按照这个式子算出来的数列是错误的 (1,2,1,2,1,2,...)

    // 注意 coef 可能包含负数，如有需要可以调整
    return coef;
}
```

如果多維 DP 可以寫成 k x k 的係數矩陣。

我們只需計算 DP 數組的連續至多 2k 項，就可以得到不超過 k 階的線性遞推式。有了遞推式，就可以用 Kitamasa 算法

ex: 
1. [CSES/Counting Towers](https://slipet.github.io/5lipet/CSES/Dynamic_Programming/#9-counting-towers)

2. [Leetcode/3700. 锯齿形数组的总数 II](https://leetcode.cn/problems/number-of-zigzag-arrays-ii/submissions/693304452/)

### Cayley-Hamilton

<span style="color:red">由 Cayley–Hamilton 可以知道對於 n x n 矩陣 A ，且 p(λ) 為 A 的特徵多項式。將矩陣 A 替換 λ 得到的矩陣多項式滿足 p(A)=0</span> 

上面有提到如果多維 DP 可以寫成 k x k 的係數矩陣。那可以使用 Berlekamp-Massey 得到不超過 k 階的"最小階"遞迴表示。

若是 k = 2 or 3 這種比較小的係數矩陣，可以利用手動推導的方式找到其遞迴表示。

ex. [CSES/Counting Towers](https://slipet.github.io/5lipet/CSES/Dynamic_Programming/#9-counting-towers)

以 2 x 2 的係數矩陣為例子:

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
a & b\\
c & d
\end{bmatrix}
，F_n = f_n + g_n
$$

由特徵多項式可得:

$$
det(\lambda I - A) = det
\begin{bmatrix}
\lambda -a & -b\\
-c & \lambda -d
\end{bmatrix}
=
(\lambda - a)(\lambda - d) - bc
=
\lambda^2 - (a + d)\lambda + (ad - bc)I
$$

由 Cayley–Hamilton 可以知道 $p(A)=0$:

$$A^2 - (a + d)A + (ad - bc)I = 0$$

乘上 $F_n = c^Tx_n$。($c^Tx_n$ 是用一組權重 $c = \begin{bmatrix} \alpha \\ \beta\end{bmatrix}$，對狀態向量 $x_n = \begin{bmatrix} f_n \\ g_n\end{bmatrix}$ 做線性加權，得到真正關心的輸出 $F_n = \begin{bmatrix} \alpha & \beta \end{bmatrix} \begin{bmatrix} f_n \\ g_n\end{bmatrix}$)

\[
\begin{array}{ll}
    A^2 - (a + d)A + (ad - bc)I = 0 \\
    \rightarrow (A^2 - (a + d)A + (ad - bc)I)x_n = 0\\ 
    \rightarrow A^2x_n - (a + d)Ax_n + (ad - bc)Ix_n = 0
\end{array}
\]

由前面可知 $x_{n+1} = Ax_n$，$x_{n+2}=A^2x_n$

\[
\begin{array}{ll}
    x_{n + 2} - (a + d)x_{n+1} + (ad - bc)x_n = 0 \\
    \rightarrow F_{n+2} - (a + d)F_{n+1} + (ad - bc)F_{n} = 0 \\
    \rightarrow F_{n} = (a + d)F_{n-1} - (ad - bc)F_{n - 2}
\end{array}
\]
