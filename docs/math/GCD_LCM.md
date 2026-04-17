# GCD/LCM

這裡放一些GCD/LCM的內容

## GCD

性質:

1. $gcd(0, x) = x$

結論:

1. gcd 越多元素 g 會越小

## LCM

性質:

1. $lcm(1, x) = x$

結論:

1. $lcm(a, b) = \frac{a \times b}{gcd(a, b)}$
2. $lcm_{i = 1}^{k}(x_1, x_2, ... , x_k) = p_1^{max(q_1, q_2 ... q_k)} \times p_2^{max(q_1, q_2 ... q_k)} ... \times p_j^{max(q_1, q_2 ... q_k)} = \prod p_j^{max_{i = 1}^{k}(q_i)}$

    lcm 是每個質因數取最大的次方項。通常來說可以維護最大和次大的次方項，也可以維護次方項的陣列。

    * [abc #445 E - Many LCMs](https://atcoder.jp/contests/abc445/tasks/abc445_e)