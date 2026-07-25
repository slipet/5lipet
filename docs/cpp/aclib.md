# Atcoder library

在 Atcoder 中把需要取模的運算封裝起來。

## Include

```cpp
#include <atcoder/modint>

using namespace atcoder;

using mint = modint998244353;
//using mint = modint1000000007;
// or: typedef modint998244353 mint;
```

## set_mod

如果模數會變用 `set_mod`

```cpp
mint::set_mod(mod);
```

## inv

* $O(\log{mod})$

```cpp
mint(x).inv()
```

## pow

* $x^n$
* $O(\log{n})$

```cpp
mint x;
x.pow(n);
```

## raw

* 轉型成 `mint` 但不取模，用來加速

* 傳給 `raw(x)` 的數值必須為 $x \in [0, mod)$

```cpp
using mint = modint;
int main() {
    mint::set_mod(1000000007);
    mint a = 1;
    for (int i = 1; i < 100000; i++) {
        a += mint::raw(i);
    }
}
```