# Bit-by-bit greedy

## 試填法

可以透過<span style="color:red">整數的連續性</span>將試填法分成兩種做法:

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

    這題是要求限制 k 次 AND 操作後集合中的元素進行 OR 所得最小，透過維護前綴 mask 確定那些位元會是 0 得到當前操作 $\le k$ 次的解。

4. [3806. 增加操作后最大按位与的结果](https://slipet.github.io/5lipet/contest/leetcode/2026_S1/#weekly-contest-484)

    貢獻法 + 試填法。構造出 target 需要多少貢獻，選最小貢獻的 m 做 AND。

* Codeforces:

    1. [XOR-factorization](https://slipet.github.io/5lipet/contest/codeforces/2025_S4/#c-xor-factorization)

        直覺的貪心會掉進陷阱，必須逐位元維護那些數是沿著 limit n，那些數是小於 n。

核心想法:

若是題目把可以操作的元素限制在一個<span style="color:red">有限集合</span>(大小大概是 $10^5$ )當中。透過維護前綴 mask 每次都只取 x 前綴，在遍歷過程中維護題目要求，重點在於遍歷的過程中要滿足限制。

有些題目可以使用 0-1 Trie 完成。

---

* k-th lexicographical binary string

    * 對於每一位，假設這一位是 0/1，計算後面還能構成多少合法字串，如果 $\ge k$，就選 0，否則選 1 並扣掉數量。

    * 或者假設這一位是 1，計算左邊和右邊可以產生多少貢獻 x x x 1 o o .. ，如果 $\le k$ 那就把這位設為 1 並扣掉貢獻。

    * 核心想法: <span style="color:red">利用整數的連續性</span>，不斷縮小規模從而確定答案。

1. [3007. 价值和小于等于 K 的最大数字](https://leetcode.cn/problems/maximum-number-that-sum-of-the-prices-is-less-than-or-equal-to-k/description/)

    計算 i-th 設 1 會產生出的貢獻，若是小於 k ，減去貢獻並縮小規模，貢獻的公式 $cnt = pre_i \times 2^i + \lfloor \frac{i}{x} \rfloor \times 2^{i - 1}$，直觀理解為右邊有 $2^i$ 的方案數可以使左邊 $pre_i$ 產生貢獻。對於右邊的情況則可以用組合數學的方式思考，在 [0, i - 1] 位元上共有 $\lfloor \frac{i}{x} \rfloor$ 個是可以產生貢獻的位元，對於每個位元其可以產生的貢獻為 $2^{i - 1}$，$2^{i - 1}$ 代表著除了自己之外的方案數，兩個相乘 $\lfloor \frac{i}{x} \rfloor \times 2^{i - 1}$ 表示在 x 位置上可以產生的貢獻。

2. [3145. 大数组元素的乘积](https://leetcode.cn/problems/find-products-of-elements-of-big-array/description/)

    這題跟上面那題的思路是一樣的，只有在細節上不一樣，因為題目限制只有 2 的冪次相乘，所以可以將相乘的計算改為冪次和的計算，避免數值溢出。另外對於右邊的冪次和 $\sum_{0}^{x = i - 1} x * 2^{i - 1}$，所以可以利用等差級數和公式得到 $\frac{i(i - 1)}{2} \times 2^{i - 1}$。

3. [3821. 二进制中恰好K个1的第N小整数](https://slipet.github.io/5lipet/contest/leetcode/2026_S1/#weekly-contest-486)

    由高位至低位透過假設 i-th bit 填 0 的方案數決定這個位元是否填 1

4. [3344. 最大尺寸数组](https://leetcode.cn/problems/maximum-sized-array/description/)

    這題要計算的是 $$\sum_{j, k}(j \text{ OR } k) = \sum_{j, k} \sum_m(j \text{ OR } k) \text{ AND } 2^m = \sum_m \sum_{j, k} (j \text{ AND } 2^m) \text{ OR } (k \text{ AND } 2^m)$$

    由於 $j \text{ AND } 2^m$ 只會是 $2^m$ 或是 0，所以可以轉化為 $n^2 - cnt^2(j \text{ AND } 2^m = 0)$，用全部可能 $n^2$ 減去兩側皆為 0 的情況:

    $$\sum_{j, k} \sum_m(j \text{ AND } 2^m) \text{ OR } (k \text{ AND } 2^m) = \sum_{j, k} \sum_m 2^m(n^2 - Card^2\{j | j \text{AND} 2^m = 0\})$$

    設函數 $Z(n, m) = Card\{j | 0 \le j < n, j \text{AND} 2^m = 0\}$ 為 $[0, n - 1]$ 的數中第 m 位為 0 的個數。

    $$\sum_{j, k} \sum_m(j \text{ AND } 2^m) \text{ OR } (k \text{ AND } 2^m) = \sum_m 2^m(n^2 - Z^2(n, m))$$

    可以發現 0, 1 在 [0, n - 1] 中是週期性出現的，所以 Z(n, m) 可以利用這個性質很容易的求出，我們可以利用二分的方式得到 $O(\log^2{n})$ 的方法。

    可以發現我們要計算的整數範圍是 [0, n - 1]，由整數的連續性可以知道可以透過逐位確定不斷縮小問題規模。

    ---

    ### 增量計算

    [講解](https://leetcode.cn/problems/maximum-sized-array/solutions/3703073/bi-er-fen-geng-kuai-de-olog-nzhu-wei-que-95sq/)

    要使用逐位確定的話必須求出在第 i 位產生的貢獻，假設 $n = a \cdot 2^b$， $n$ 是二進制右邊皆為 0 的形式。

    有 $G(n) = \sum_{0\le j, k < n}(j \text { OR } k) = \sum_m 2^m(n^2 - Z^2(n, m))$

    我們想要計算的是對於 $2^{b-1}$ 產生的增量，也就是 $G(n + 2^{b-1}) - G(n)$，當我們由高至低確定第 b - 1 位是否會超過限制 s ，若是不超過則 $n = n + 2^{b-1}$ 不斷逼近 s。

    由上面的 $G(n)$ 可以知道，$G(n + 2^{b-1}) = \sum_{m}2^m((n+2^{b-1})^2 - Z^2(n+2^{b-1},m))$，此時需要關注的點是 $Z(n+2^{b-1},m)$，要考慮的是 $Z(n+2^{b-1},m)$ 與 $Z(n+2,m)$ 的關係變化。

    我們要考慮的是四種情況:

    1) $0 \le m < b - 1$

    由二進制的週期性可以知道 $Z(n + 2^{b-1}, m) = \frac{n + 2^{b-1}}{2}$

    此時產生的增量為

    $$
    \begin{aligned}
    G(n + 2^{b-1}) - G(n) &= \sum_m 2^m((n+2^{b-1})^2 - Z^2(n+2^{b-1}, m)) - \sum_m 2^m(n^2 - Z^2(n, m)) \\ 
    &= \sum_m 2^m((n+2^{b-1})^2 - Z^2(n+2^{b-1}, m) - n^2 + Z^2(n,m)) \\
    &= \sum_m 2^m((n+2^{b-1})^2 - (\frac{n + 2^{b-1}}{2})^2 - n^2 + (\frac{n}{2})^2) \\
    &= \frac{3}{4} \sum_m 2^m((n+2^{b-1})^2 - n^2) \\
    &= \frac{3}{4} \cdot (2^{b-1} - 1) \cdot ((n+2^{b-1})^2 - n^2) \\
    &= \frac{3}{4} \cdot (2^{b-1} - 1) \cdot 2^{b-1}(2 \cdot n + 2^{b-1}) \\
    \end{aligned}
    $$

    可以利用等比級數和公式將式子化簡 $\sum_{m = 0}^{b - 2} 2^m = (2^{b-1} - 1)$ 

    2) $m = b - 1$
    
    在 $[n, n + 2^b-1)$ 上第 b - 1 位都是 0，這是 $Z(n + 2^{b-1}, b - 1)$ 產生的增量，也就是 $Z(n + 2^{b-1}, b - 1) = Z(n, m) + 2^{b-1}= \frac{n}{2} + 2^{b-1}$

    因此增量為:

    $$
    \begin{aligned}
    G(n + 2^{b-1}) - G(n) &= \sum_m 2^m((n+2^{b-1})^2 - Z^2(n+2^{b-1}, b-1)) - \sum_m 2^m(n^2 - Z^2(n, b-1)) \\ 
    &= \sum_m 2^m((n+2^{b-1})^2 - (\frac{n}{2} + 2^{b-1})^2) - \sum_m 2^m(n^2 - (\frac{n}{2})^2) \\
    &= \sum_m 2^m((n+2^{b-1})^2 - (\frac{n}{2} + 2^{b-1})^2 - n^2 + (\frac{n}{2})^2) \\
    &= \sum_m 2^m(2 \cdot n \cdot 2^{b-1} - n \cdot 2^{b-1}) \\
    &= \sum_m 2^m(n \cdot 2^{b-1}) \\
    &= 2^{b-1}\cdot n \cdot 2^{b-1} \\
    \end{aligned}
    $$

    3) $m \ge b, (n \text{ AND } 2^m) = 0$

    

    4) $m \ge b, (n \text{ AND } 2^m) = 2^m$


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
