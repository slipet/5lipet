# Chinese remainder theorem

中國剩餘定理

講解: [1](https://www.luogu.com.cn/article/b9vzcrkd) [2](https://zhuanlan.zhihu.com/p/44591114)


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