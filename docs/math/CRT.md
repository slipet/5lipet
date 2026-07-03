# Chinese remainder theorem

中國剩餘定理

講解: [1](https://www.luogu.com.cn/article/b9vzcrkd) [2](https://zhuanlan.zhihu.com/p/44591114)

目標: 求解同餘方程

設 $a_1, a_2, a_3, ... , a_n$ 兩兩互質

\[
\left\{
\begin{array}{ll}
    x \equiv b_1 \pmod{a_1} \\
    x \equiv b_2 \pmod{a_2} \\
    x \equiv b_3 \pmod{a_3} \\
    ...
\end{array}
\right.
\]


設 $M_i = \frac{\prod a_i}{a_i}$，所求為

$$k_1 \times M_1 + k_2 \times M_2 + k_3 \times M_3 + ...$$

且 $k_i \times M_i \equiv b_i \pmod{a_i} \rightarrow k_i \equiv b_i \times M^{-1} \pmod{a_i}$

使用 `exgcd` 計算 $M_i$ 的逆元

```cpp
void exgcd(ll a, ll b, ll &x, ll &y) {
    if(b == 0) {
        x = 1;
        y = 0;
        return;
    }
    exgcd(b, a % b, y, x);
    y -= a / b * x;
}
ll INV(ll a, ll p) {
    ll x, y;
    exgcd(a, p, x, y);
    return (x + p) % p;
}

ll CRT(vector<ll> &r, vector<ll> &p) {
    ll mod = 1, ans = 0;
    for(auto &x: p) mod *= x;
    for(int i = 0; i < p.size(); ++i) {
        ll m = mod / p[i], t = INV(m, p[i]);
        ans = (ans + r[i] * m % mod * t % mod) % mod;
    }
    return (ans % mod + mod) % mod;
}
```

## 擴展: 模數不互質的情況

[ref](https://oi-wiki.org/math/number-theory/crt/#%E6%89%A9%E5%B1%95%E6%A8%A1%E6%95%B0%E4%B8%8D%E4%BA%92%E8%B4%A8%E7%9A%84%E6%83%85%E5%86%B5)

求同餘方程:

設 $gcd(a_1, a_2) > 1$

\[
\left\{
\begin{array}{ll}
    x \equiv b_1 \pmod{a_1} \\
    x \equiv b_2 \pmod{a_2} \\
\end{array}
\right.
\]

先將兩個式子寫成不定方程式:

\[
\begin{array}{ll}
    b_1 + a_1 p = b_2 + a_2 q \\
    \rightarrow a_1p - a_2q = b_2 - b_1\\
\end{array}
\]

此時 $p, q$ 為一整數，由斐蜀定理可以知道，當 $b_2 - b_1$ 不能被 $gcd(a_1, a_2)$ 整除時無解。

因此可以透過 `exgcd` 得到一組可行解 $(p, q)$，而原來的方程可以寫成

$$x \equiv b \pmod{M}$$

此時 $b = a_1 p + b_1$ 且 $M = lcm(a_1, a_2)$

這樣就將兩個模方程合併成一個，透過兩兩合併可以將多個方程求解
