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