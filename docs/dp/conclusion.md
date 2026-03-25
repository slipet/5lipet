# Conclusion

放一些 DP 的心得/小結論

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
    

