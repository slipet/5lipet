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

#### 使用最小質因數做質因數分解

由於質因數分解預處理會有空間上的限制在 $10^6, 10^7$ 的值域下沒辦法開那麼大的空間儲存每個數的質因數。

因此可以換個方式，儲存每個數的最小質因數，透過一個質因數可以找到其餘的質因數。

時間複雜度: $O(n\log{n})$
空間複雜度: $O(n)$

```cpp
const int MAXN = 1e7;
int spf[MAXN+1];

auto init = []() {
    for(int i = 1; i <= MAXN; i++) spf[i] = i;
    for(int i = 2; i * i <= MAXN; i++) {
        if(spf[i] == i) {
            for(int j = i * i; j <= MAXN; j += i) {
                if(spf[j] == j) spf[j] = i;
            }
        }
    }
    return 0;
}();

while (x > 1) {
    int p = spf[x];
    while (x % p == 0) x /= p;
}
```

####  質因數分解方案數

[講解](https://leetcode.cn/problems/count-ways-to-make-array-with-product/solutions/2713481/tu-jie-zhi-yin-zi-fen-jie-fang-qiu-wen-t-fboo/)

設 $X$ 的質因數分解為 $p_1^{e_1}p_2^{e_2}p_3^{e_3}...p_t^{e_t}$，則構造 n 個因數的方案數:

$$\binom{e_1 + n - 1}{e_1}\binom{e_2 + n - 1}{e_2}...\binom{e_t + n - 1}{e_t}$$

```
假設要把 p^3 分配給 5 個數

| p, p | __ | __ | p | __ |

可以看成往 n = 5 個空盒分配 e = 3 個相同小球

把每個盒子都加上一個小球

| p, p, p | p | p | p, p | p |

可以看成將 e + n 個小球劃分五個區間，此時會有 e + n - 1 位置可以選擇，需要選 e 個位置

p _ p _ p _ p _ p _ p _ p _ p

也就是 C(e + n - 1, e)

```

## 預處理得到所有因數

* 一個數的因子個數可以用開立方估計(估計大致的數量級 $\le \sqrt[3]{x}$)

* $O(n\log{n})$

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

#### 高度合成數

* 高度合成數 (Highly Composite Numbers, HCN)** ——就是在所有比它小的正整數裡，它擁有最多的因子數。

[ref](https://oeis.org/A002182)

```cpp
1, 2, 4, 6, 12, 24, 36, 48, 60, 120, 180, 240, 360, 720,
840, 1260, 1680, 2520, 5040, 7560, 10080, 15120, 20160,
25200, 27720, 45360, 50400, 55440, 83160, 110880, 166320,
221760, 277200, 332640, 498960, 554400, 665280, 720720,
1081080, 1441440, 2162160
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

## $\lfloor \frac{N}{M} \rfloor \mod{P}$

由 [ref](https://codeforces.com/blog/entry/48989) 得到 

$$N = (MP)Q + r$$

將 N 模 $M \times P$ 後，兩邊同除 M 就是結果:

$$\frac{N}{M} = PQ + \frac{r}{M}$$

* atcoder:
    * [#448 E. Simple Division]()

## 歐幾里得算法/帶餘數除法

[video](https://www.bilibili.com/video/BV1eK4y1L7hq/?spm_id_from=333.1387.search.video_card.click&vd_source=caaccd1459c5ece44b5e2d37804871b8)

$$gcd(a^m - b^m, a^n - b^n) = a^{gcd(m, n)} - b^{gcd(m, n)}$$

$$gcd(a^m - 1, a^n - 1) = a^{gcd(m, n)} - 1$$

F 為 fibonacci 數列 $F_1 = F_2 = 1, \text{ 且 } F_n = F_{n - 1} + F_{n - 2}$ ，有以下結論:

$$gcd(F_m, F_n) = F_{gcd(m, n)}$$

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