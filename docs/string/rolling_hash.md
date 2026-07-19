# Rolling hash

```cpp

mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());

long long rnd(long long x, long long y) {
    return uniform_int_distribution<long long>(x, y)(rng);
}

const long long MOD = 1e18 + rnd(0, 1e18);
const int BASE = 233 + rnd(0, 1e3);

struct HashSeq {
    vector<__int128> P, H;

    HashSeq(vector<int> &s) {
        int n = s.size();
        P.resize(n + 1);
        P[0] = 1;
        for (int i = 1; i <= n; i++) P[i] = P[i - 1] * BASE % MOD;
        H.resize(n + 1);
        H[0] = 0;
        for (int i = 1; i <= n; i++) H[i] = (H[i - 1] * BASE + s[i - 1]) % MOD;
    }

    long long query(int l, int r) {
        return (H[r] - H[l - 1] * P[r - l + 1] % MOD + MOD) % MOD;
    }
};

```