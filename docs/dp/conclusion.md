# Conclusion

放一些 DP 的心得/小結論

## 計算方案數

* Leetcode [#2320. 统计放置房子的方式数](https://leetcode.cn/problems/count-number-of-ways-to-place-houses/description/)

    Transition function: $f(i) = f(i - 1) + f(i - 2)$
    Initial: $f(0) = 1, f(1) = 2$

    $f(0)$ 代表選擇為空的方案數，$f(1)$ 代表選擇 $\{\{\emptyset\}, \{1\}\}$ 兩種方案。
    
    對於位置 i 來說有兩種選擇:

    1. 選擇當前位置: 構造出以當前位置為結尾可以產生出的合法方案，有 $f(i - 2)$ 個方案數。

    2. 不選擇當前位置: 不選當前位置可以產生出的方案，此時有 $f(i - 1)$ 個。

    加上 $f(i - 1)$ 時就會把 $\{\emptyset\}$ 這種方案也會同時考慮進去。

    可以看成總共可以產生出多少個子序列，且相鄰子序列不能連續選擇。
    

