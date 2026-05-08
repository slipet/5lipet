# Matrix

矩陣運算

### 變動大小

```cpp
template <typename T>
struct Matrix {
    int m, n;
    vector<vector<T>> a;
    Matrix() : m(0), n(0) {}
    Matrix(int m, int n, T init_val = T{})
        : m(m), n(n), a(m, vector<T>(n, init_val)) {}

    vector<T>& operator[](int i) {
        return a[i];
    }

    const vector<T>& operator[](int i) const {
        return a[i];
    }
    static Matrix identity(int n) {
        Matrix<T> I(n, n, T{});
        for(int i = 0; i < n; ++i) I[i][i] = 1;
        return I;
    }

    Matrix operator *(const Matrix &b) const {
        assert(n == b.m);
        Matrix res(m, b.n, T{});
        for(int i = 0; i < m; ++i) {
            for(int k = 0; k < n; ++k) {
                for(int j = 0; j < b.n; ++j) {
                    res[i][j] += a[i][k] * b[k][j];
                }
            }
        }
        return res;
    };
    Matrix operator +(const Matrix &b) const {
        assert(m == b.m && n == b.n);

        Matrix res(m, n, T{});
        for(int i = 0; i < m; ++i) {
            for(int j = 0; j < n; ++j) {
                res[i][j] = this->a[i][j] + b[i][j];
            }
        }
        return res;
    };
};
```