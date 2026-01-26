# Bit-by-bit greedy

## 試填法


1. 假設 i-th 為 1 則能否有符合限制的前綴，leetcode 421, 2935 都是標準的模板。

```cpp
for(int i = hi; i >= 0; --i) {
    int nxt = ans | (1 << i);
    for(auto ...) {//traverse element to meet the requirments
        if(check ...) {
            ans = nxt
            ...
        }
        ...
    }
}
```

2. 

* Leetcode:

    1. [421. 数组中两个数的最大异或值](https://leetcode.cn/problems/maximum-xor-of-two-numbers-in-an-array/)

    2. [2935. Maximum Strong Pair XOR II](https://leetcode.com/problems/maximum-strong-pair-xor-ii/description/)

    3. [3806. 增加操作后最大按位与的结果](https://slipet.github.io/5lipet/contest/leetcode/2026_S1/#weekly-contest-484)

        貢獻法 + 試填法。構造出 target 需要多少得貢獻，選最小貢獻的 m 做 AND。

    4. [3821. 二进制中恰好K个1的第N小整数](https://slipet.github.io/5lipet/contest/leetcode/2026_S1/#weekly-contest-486)

        由高位至低位透過假設 i-th bit 填 0 的方案數決定這個位元是否填 1。

* Codeforces:

## 逐位貪心

* Codeforces:

    1. [Max Sum OR (Easy Version)/(Hard Version)](https://slipet.github.io/5lipet/problems/codeforces/#2146d2-max-sum-or-hard-version)

    2. [XOR-factorization](https://slipet.github.io/5lipet/contest/codeforces/2025_S4/#c-xor-factorization)
