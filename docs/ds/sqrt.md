# Square root concept

### 分塊思想

透過算幾不等式優化修改/查詢的複雜度。

$$\frac{a + b}{2} \ge \sqrt{ab}$$

在 $a = b$ 時等號會成立。

假設有陣列長度為 $n$ ，同時有 $q$ 個可以修改陣列區間 $[l, r)$ 的操作或是回答 $[l, r)$ 的詢問，此時暴力的複雜度為 $O(qn)$。

若是可以將陣列劃分成 $\lceil \frac{n}{B} \rceil$ 個區塊，每塊大小為 $\le B$，此時的複雜度為 $O(q(B + \frac{n}{B}))$。

透過算幾不等式可以知道:

\[
\begin{array}{ll}
    \frac{B + \frac{n}{B}}{2} &\ge \sqrt{B \times \frac{n}{B}} \\
    B + \frac{n}{B} &\ge 2\sqrt{n}\\
    B^2 - 2B\sqrt{n} + n &\ge 0 \\
    (B - \sqrt{n})^2 &\ge 0
\end{array}
\]

可以知道 $B = \frac{n}{B}$ 時等號會成立，此時 $\frac{B + \frac{n}{B}}{2}$ 會是最小值，因此可以得到:

$$B = \sqrt{n}$$

代回上面的複雜度可以得到 $O(q\sqrt{n})$。