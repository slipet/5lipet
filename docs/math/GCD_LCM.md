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


### Euclid's Lemma(歐幾里得引理)

若:

$$a | bc, \text{ and } gcd(a, b) = 1$$

則:

$$a | c$$

推廣

\[
\boxed{
a \mid bc
\iff
\frac{a}{\gcd(a,b)} \mid c
}
\]

證明:

設 $g = \gcd(a, b) \rightarrow a = ga', b = gb'$ 代回原式:

$$ga' \mid gb'c$$

同除 $g$ 得到 $a' \mid b'c$，由於 $\gcd(a', b') = 1$，則

$$a' \mid c$$


### 求滿足 $xy \equiv 0(\mod{M})$ 且 $1 \le x \le n$ 的個數

歐幾里得引理得知:

\[
\boxed{
a \mid bc
\iff
\frac{a}{\gcd(a,b)} \mid c
}
\]

所以有:

\[
\boxed{
\#\{1\le x\le n : M\mid xy\}
= \left\lfloor
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

對於 k = 2 時， $e_1 + e_2 = \min(e_1, e_2) + \max(e_1, e_2)$ 恆成立。

對於 k = 3 時，要分類討論:

1. 有一個 $e_i = 0$ 的情況，假設 $e_3 = 0$，則 $\min(e_1, e_2, 0) + \max(e_1, e_2, 0) = 0 + \max(e_1, e_2)$，此時等式無法成立

2. 有二個 $e_i = 0$ 的情況，假設 $e_2 = e_3 = 0$，則 $\min(e_1, 0, 0) + \max(e_1, 0, 0) = 0 + e_1$，此時等式成立

可以得到結論，在 k = 3 時只有兩個 $e_i = 0$ 可以使上面的等式成立。

因此可以推廣到 $k \ge 3$ 的情況，$e_1 + e_2 ... + e_k = \min(e_1, e_2, ... e_k) + \max(e_1, e_2, ... e_k)$ 當且僅當 至少 $k - 1$ 個 $e_i = 0$ 時成立。

也就是說如果 $k \ge 3$ 那這 k 個數必須兩兩互質，不能有兩個數有大於 1 個公因數，否則無法滿足 至少 $k - 1$ 個 $e_i = 0$。
