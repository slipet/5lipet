# Enumerate Skills

這裡放一些枚舉技巧


### 求移動絕對值貢獻

給定一個長為 n 的序列 $A = a_1, a_2, ..., a_n$，求 $ 1 \le j \le n$ 的最大貢獻，貢獻的計算方式為:

$$ G = max_{j = 1}^{n}(gain_j) = max_{j = 1}^{n}max_{i = 1}^{n}(a_i + |j - i|)$$

暴力計算需要 $O(n^2)$，因此先將絕對值消掉想辦法找規律，並專注在某個位置 $j$ 上:

\[
\begin{array}{ll}
    gain_j = max_{i = 1}^{n}{(a_i - (j - i))}, \text{ for } i \le j\\
    gain_j = max_{i = 1}^{n}{(a_i - (i - j))}, \text{ for } i > j
\end{array}
\]

把與 $i$ 的無關項 $j$ 提出來:

\[
\begin{array}{ll}
    gain_j = - j + max_{i = 1}^{n}{(a_i + i)}, \text{ for } i \le j\\
    gain_j = j+ max_{i = 1}^{n}{(a_i - i)}, \text{ for } i > j
\end{array}
\]

可以發現此時 $max$ 內的項只會與 $i$ 有關，接著透過前後綴分解可以得到最大值進行計算，將複雜度降到 $O(n)$

* [leetcode 1937. 扣分后的最大得分](https://leetcode.cn/problems/maximum-number-of-points-with-cost/description/)