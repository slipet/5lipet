# Conclusion

放一些 DP 的心得/小結論

## 狀態設計

* 當狀態數有限，但是值域很大時可以使用 hash table + encoding 

    * [3490. Count Beautiful Numbers](https://leetcode.com/problems/count-beautiful-numbers/description/)
  
* 求 subarray 的問題，或是求多個 subarray 的問題，透過 kadane 的思想，可以定義狀態為 $f_0, f_1, f_2, ...$ ，$f_0$ 表示第一個 subarray **前**的結果，$f_1$ 表示第一個 subarray **中**，$f_2$ 表示第一個 subarray **後**的結果:

    ```cpp
    |-----f0----|-----f1----|-----f2----|
    ```
    
    以 kadane 為例子，轉移方程為 $f_1 = max(f_1, f_0) + x$，因為 $f_0$ 都為空，所以普通的 kadane $f_0$ 會是 0。

    * [3434. 子数组操作后的最大频率](https://leetcode.cn/problems/maximum-frequency-after-subarray-operation/description/?envType=problem-list-v2&envId=1VhVUpsv)

        這題利用上面的想法，把狀態定義成 $f_0, f_1, f_2$ ，分別表示:

        * $f_0:$ 被修改的子數組的左邊
        * $f_1:$ 被修改的子數組
        * $f_2:$ 被修改的子數組的右邊

        轉移方程為:

        \[
        \begin{array}{ll}
            f_2 = max(f_2, max(f_1[x])) + (x == k)\\
            f_1[x] = max(f_1[x], f_0) + 1\\
            f_0 = f_0 + (x == k)
        \end{array}
        \]

    * **利用狀態定義可以推廣到 > 1 以上的 subarray 應用**

## 利用 f(i - 1) 將前綴最優狀態轉移

* Leetcode [#2320. 统计放置房子的方式数](https://leetcode.cn/problems/count-number-of-ways-to-place-houses/description/)

    Transition function: $f(i) = f(i - 1) + f(i - 2)$
    Initial: $f(0) = 1, f(1) = 2$

    $f(0)$ 代表選擇為空的方案數，$f(1)$ 代表選擇 $\{\{\emptyset\}, \{1\}\}$ 兩種方案。
    
    對於位置 i 來說有兩種選擇:

    1. 選擇當前位置: 構造出以當前位置為結尾可以產生出的合法方案，有 $f(i - 2)$ 個方案數。

    2. 不選擇當前位置: 不選當前位置可以產生出的方案，此時有 $f(i - 1)$ 個。

    $f(i-1)$ 代表不選第 $i$ 個位置的方案數（繼承前綴所有可能）；$f(i-2)$ 代表選擇第 $i$ 個位置的方案數（受限於相鄰約束）。

* Leetcode [3186. 施咒的最大总伤害](https://leetcode.cn/problems/maximum-total-damage-with-spell-casting/description/)

    Transition function: $f(i) = max(f(i - 1), f(j) + x \times n)$

    值域的打家劫舍只能取間隔超過 2 的元素。$f(i - 1)$ 的值同時也隱含著 $f(i - 2)$ 為最優的可能性。而 j 則是剛好 $< x - 2$ 的位置。
    
## 利用剩餘最優結果進行優化

也就是假設剩餘"最優"結果都沒辦法使當前結果更好，那就不繼續下去。

* [leetcode #3459. 最长 V 形对角线段的长度](https://leetcode.cn/problems/length-of-longest-v-shaped-diagonal-segment/description/)
    因為網格的大小固定，透過剩下路徑可能的長度進行減枝。

