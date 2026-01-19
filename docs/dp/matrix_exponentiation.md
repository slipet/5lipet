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