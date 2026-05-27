# Matrix

矩陣運算

### 快速實作

```cpp
struct Matrix {
    //need initialize
    ll a[2][2]{};
    Matrix operator*(const Matrix &t) const {
        Matrix res;
        for(int i = 0; i < 2; ++i) {
            for(int j = 0; j < 2; ++j) {
                res.a[i][j] = infll;
                for(int k = 0; k < 2; ++k) {
                    chmin(res.a[i][j], a[i][k] + t.a[k][j]);
                }
            }
        }
        return res;
    }
};
```

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

### 固定大小

```cpp
template <typename T, int R, int C>
struct Matrix {
    
    array<array<T, C>, R> a;
    
    Matrix(T init_val = T{}) {
        for(auto &row: a) {
            ranges::fill(row, init_val);
        }
    }

    array<T, C>& operator[](int i) {
        return a[i];
    }

    const array<T, C>& operator[](int i) const {
        return a[i];
    }
    static Matrix identity() {
        static_assert(R == C);
        Matrix I(T{});
        for(int i = 0; i < R; ++i) I[i][i] = T{1};
        return I;
    }

    template <int K>
    Matrix<T, R, K> operator *(const Matrix<T, C, K> &b) const {
        Matrix res(T{});
        for(int i = 0; i < R; ++i) {
            for(int k = 0; k < C; ++k) {
                for(int j = 0; j < K; ++j) {
                    res[i][j] += a[i][k] * b[k][j];
                }
            }
        }
        return res;
    };
    Matrix operator +(const Matrix &b) const {

        Matrix res(T{});
        for(int i = 0; i < R; ++i) {
            for(int j = 0; j < C; ++j) {
                res[i][j] = this->a[i][j] + b[i][j];
            }
        }
        return res;
    };
};

//auto I = Mat::identity();
//auto B = A * I;
```