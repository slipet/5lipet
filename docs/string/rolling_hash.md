# Rolling hash


* **字串哈希** 如果要比較字串哈希的大小，透過二分 LCP(longest common prefix) 的長度，比較長度 + 1 位置的字符就能得到大小關係。

```cpp

mt19937 rng(chrono::steady_clock::now().time_since_epoch().count());

long long rnd(long long x, long long y) {
    return uniform_int_distribution<long long>(x, y)(rng);
}

const long long MOD = 1e18 + rnd(0, 1e18);
const int BASE = 233 + rnd(0, 1e3);

struct HashSeq {
    //__int128非常大，如果tle考慮把mod和__int128改小
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
    //usage [l, r] -> query(l + 1, r + 1)
    long long query(int l, int r) {
        return (H[r] - H[l - 1] * P[r - l + 1] % MOD + MOD) % MOD;
    }
};

```

* [3934. 最短唯一子数组](https://leetcode.cn/problems/smallest-unique-subarray/description/)


## 比較大小

```cpp
auto check = [&](int len, int i, int j) -> int {
    return rs.query(i + 1, i + len) == rs.query(j + 1, j + len);
};
int l = -1, r = n;
while(l + 1 < r) {
    int m = l + (r - l) / 2;
    (check(m, i, mnidx) ? l : r) = m;
}
if(l < n && t[i + l] <= t[mnidx + l]) {
    mnidx = i;
}
```

* [3999. 字符串变换后的最少分组数](https://leetcode.cn/problems/minimum-number-of-string-groups-through-transformations/description/)