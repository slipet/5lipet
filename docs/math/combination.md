# Combination

組合數

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
```