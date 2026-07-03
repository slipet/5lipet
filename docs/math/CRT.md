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