# GCD/LCM

這裡放一些GCD/LCM的內容

* 兩個都是重複貢獻的問題也就是 $x \text{ opt }x = x$

## GCD

性質:

1. $gcd(0, x) = x$

結論:

1. gcd 越多元素 g 會越小 

$$gcd(x_1, x_2) \ge gcd(x_1, x_2, x_3) \ge gcd(x_1, x_2, x_3, ...)$$

   * [leetcode #3901. 好子序列查询](https://leetcode.cn/problems/good-subsequence-queries/description/)

## LCM

性質:

1. $lcm(1, x) = x$

結論:

1. $lcm(a, b) = \frac{a \times b}{gcd(a, b)}$
2. $lcm_{i = 1}^{k}(x_1, x_2, ... , x_k) = p_1^{max(q_1, q_2 ... q_k)} \times p_2^{max(q_1, q_2 ... q_k)} ... \times p_j^{max(q_1, q_2 ... q_k)} = \prod p_j^{max_{i = 1}^{k}(q_i)}$

    lcm 是每個質因數取最大的次方項。通常來說可以維護最大和次大的次方項，也可以維護次方項的陣列。

    * [abc #445 E - Many LCMs](https://atcoder.jp/contests/abc445/tasks/abc445_e)

## Others

### 求滿足 $xy \equiv 0 (\mod M)$ 且 $1 \le x \le n$ 的個數

\[
\boxed{
M \mid xy
\iff
\frac{M}{\gcd(M,y)} \mid x
}
\]

\[
\boxed{
\#\{1\le x\le n : M\mid xy\}
=
\left\lfloor
\frac{n}{\frac{M}{\gcd(M,y)}}
\right\rfloor
}
\]

### $\prod{x_i} = \gcd(x_i) \times \operatorname{lcm}(x_i)$ 最長長度

$\prod{x_i}$ 會使得數字非常大，所以要透過特殊的性質得到。

考慮 $x_i$ 的質因數 $p$ 有 $p^{e_i}$。

1. 對於 $\gcd(x_i) = \min(e_1, e_2, ... e_k)$
2. 對於 $\operatorname{lcm}(x_i) = \max(e_1, e_2, ... e_k)$
3. 對於 $\prod{p^{e_i}} = e_1 + e_2 + ... + e_k$

因此最後會得到 $e_1 + e_2 ... + e_k = \min(e_1, e_2, ... e_k) + \max(e_1, e_2, ... e_k)$