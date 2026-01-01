# Number theory

## 公因數

### 求公因數

* 即輾轉相除法

$g = gcd(x, y)$

* 常見題目類型

1. (x, y) 透過操作達到 (x - y, y), (x, y - x)

[2543. 判断一个点是否可以到达](https://leetcode.cn/problems/check-if-point-is-reachable/description/)

## 判斷質數

* $O(\sqrt{n})$

```cpp
bool isPrimes(int n){
    if(n == 1) return false;
    for(int i = 2; i*i<=n;++i){
        if(n % i == 0) return false;
    }
    return true;
}
```

## Seieve of prime

* 埃式篩

* $O(n\log{\log{(n)}})$

```cpp
// Find all primes <= n.
vector<int> prime;
bool is_prime[N];
vector<int>Eratosthenes(int n)
{
		is_prime[0] = is_prime[1] = false;
	  for(int i = 2; i <= n; ++i) is_prime[i] = true;
	  
    for(int i = 2; i <= n; ++i) {
		    if(is_prime[i])
			      for (int j = i * i; j <= n; j += i) is_prime[j] = false;
	  }       
	  for(int i = 2; i <= n; ++i)
	     if (is_prime[i]) prime.push_back(i);
}
```

* 根號優化

* $O(n\ln{\ln{\sqrt{n}}})$

```cpp
vector<int> prime;
bool is_prime[N];

void Eratosthenes(int n) {
  is_prime[0] = is_prime[1] = false;
  for (int i = 2; i <= n; ++i) is_prime[i] = true;
  // i * i <= n 说明 i <= sqrt(n)
  for (int i = 2; i * i <= n; ++i) {
    if (is_prime[i])
      for (int j = i * i; j <= n; j += i) is_prime[j] = false;
  }
  for (int i = 2; i <= n; ++i)
    if (is_prime[i]) prime.push_back(i);
}
```

## 質因數分解預處理(Prim factorization)

* 一定要用這個方式寫 $\sqrt{n}$ 的優化只能用在篩法，不然會漏掉 $\ge \sqrt{n}$

```cpp
const int MX = 100'000;
vector<int> prime_factor[MX + 10];
auto init = []() {
    for(int i = 2; i <= MX; ++i) {
        if(prime_factor[i].empty()) {
            for(int j = i; j <= MX; j += i) {
		            prime_factor[j].push_back(i);
            }
        }

    }
    return 0;
}();
```

## 預處理得到所有因數

* $O(n\log{(n)})$

```cpp
const int MX = 100'001;
vector<int> divisors[MX];

int init = [] {
    // 预处理每个数的因子
    for (int i = 1; i < MX; i++) {
        for (int j = i; j < MX; j += i) {
            divisors[j].push_back(i);
        }
    }
    return 0;
}();
```

## 平方剩餘和預處理

* 也就是說次方項為奇數的乘積

```cpp
const int MX = 1'000'00;
int core[MX + 1];

auto init = []() {
    memset(core, 0, sizeof(core));

    for(int i = 1; i <= MX; ++i) {
        if(core[i] == 0) {
            for(int j = 1; i * j * j <= MX; ++j) {
                core[i * j * j] = i;
            }
        }
    }
    return 0;
}();
```

* 類似題目:

    * [完全平方数的祖先个数总和](https://leetcode.cn/problems/sum-of-perfect-square-ancestors/description/)

    * [完全子集的最大元素和](https://leetcode.cn/problems/maximum-element-sum-of-a-complete-subset-of-indices/description/)

## 歐幾里得算法/帶餘數除法

$$gcd(a^{m - n}, b^{m - n}) = a^{gcd(m, n)} - b^{gcd(m, n)}$$

## 斐蜀定理/貝祖定理(Bézout's lemma)

Let $a,b \in Z$, not both zero.

Let $d = gcd(a, b)$.

Then there exist integers $x,y \in Z$ such that

$$ax + by = d$$

Equivalently, the set of all integer linear combinations,

$$S = \{ax + by | x, y \in Z \}$$

假設每次都從 a = 0 開始走 b 步，可以到達的位置集合

$$\{tb \text{ mod } l | t \in Z \}$$

集合大小

$$\frac{n}{gcd(l, b)}$$

是否對任意位置 $x$，存在整數 $t$ 使

$$tb \equiv x (\text{mod } l)$$

這等價於：

$$tb + ly = x$$