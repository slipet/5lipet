# Bit-by-bit greedy

## 試填法


* 假設 i-th 為 1/0 則，能否有符合限制的前綴。

```cpp
for(int i = hi; i >= 0; --i) {
    mask |= 1 << i;
    int nxt = ans | (1 << i);
    for(auto ...) {//traverse element to meet the requirments
        res  [op=] (x & mask);
        if(check ...) {
            ans = nxt
            ...
        }
        ...
    }
}
```

1. [421. 数组中两个数的最大异或值](https://leetcode.cn/problems/maximum-xor-of-two-numbers-in-an-array/)

    題目要求找出最大 $x \oplus y$，透過維護前綴 mask 以及在 i-th 位元試填 nxt，使用 hash table 找出是否有 $nxt \oplus x_{pre_i}$，若有表示 i-th 可以填 1。

2. [2935. Maximum Strong Pair XOR II](https://leetcode.com/problems/maximum-strong-pair-xor-ii/description/)

    題目要求找出符合限制的 $x \oplus y$，透過維護前綴 mask 以及在 i-th 位元試填 nxt，使用 hash table 找到能否有符合題目要求的 $nxt \oplus x_{pre_i}$，若有表示 i-th 可以填 1。

3. [3022. 给定操作次数内使剩余元素的或值最小](https://leetcode.cn/problems/minimize-or-of-remaining-elements-using-operations/description/)

    這題乍看之下要使用下面的方法做逐位排除確定，但是從 [7,3,15,14,2,8] 這個例子會發現這樣沒辦法得到解答，可以發現若是題目把可以操作的元素限制在一個集合當中，並且要透過維護一些性質並計算出答案，就要使用類似上面的模板的思考方向。

    

核心想法:

透過前綴 mask ，透過 mask 每次都只取 x 前綴，在維護遍歷過程中題目要求，重點在於遍歷的過程中要維護限制的要求。

有些題目可以使用 0-1 Trie 完成。


* k-th lexicographical binary string

    * 對於每一位，假設這一位是 0，計算後面還能構成多少合法字串，如果 $\ge k$，就選 0，否則選 1 並扣掉數量。不一定是


    * 或者假設這一位是 1，計算左邊和右邊可以產生多少貢獻 x x x 1 o o .. ，如果 $\le k$ 那就把這位設為 1 並扣掉貢獻。

    1. [3007. 价值和小于等于 K 的最大数字](https://leetcode.cn/problems/maximum-number-that-sum-of-the-prices-is-less-than-or-equal-to-k/description/)

    2. [3145. 大数组元素的乘积](https://leetcode.cn/problems/find-products-of-elements-of-big-array/description/)

    3. [3806. 增加操作后最大按位与的结果](https://slipet.github.io/5lipet/contest/leetcode/2026_S1/#weekly-contest-484)

        貢獻法 + 試填法。構造出 target 需要多少得貢獻，選最小貢獻的 m 做 AND。

    4. [3821. 二进制中恰好K个1的第N小整数](https://slipet.github.io/5lipet/contest/leetcode/2026_S1/#weekly-contest-486)

        由高位至低位透過假設 i-th bit 填 0 的方案數決定這個位元是否填 1。

* Codeforces:

## 逐位貪心

* Codeforces:

    1. [XOR-factorization](https://slipet.github.io/5lipet/contest/codeforces/2025_S4/#c-xor-factorization)

        直覺的貪心會掉進陷阱，必須逐位元維護那些數是沿著 limit n，那些數是小於 n。

## Other

* Codeforces:

    1. [Max Sum OR (Easy Version)/(Hard Version)](https://slipet.github.io/5lipet/problems/codeforces/#2146d2-max-sum-or-hard-version)

        觀察到對於 $[2^h, r]$ 的位元表示跟　$[l, 2^h - 1]$ 是互補的，因此可以用這個方式進行配對。

need to practice:

leetcode 3106. 满足距离约束且字典序最小的字符串
leetcode 3720. 大于目标字符串的最小字典序排列
leetcode 3734. 大于目标字符串的最小字典序回文排列
leetcode 3470. 全排列 IV
leetcode 3022. 给定操作次数内使剩余元素的或值最小
leetcode 3518. 最小回文排列 II