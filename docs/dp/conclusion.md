# Conclusion

放一些 DP 的心得/小結論

## 計算方案數

* Leetcode [#2320. 统计放置房子的方式数](https://leetcode.cn/problems/count-number-of-ways-to-place-houses/description/)

    Transition function: $f(i) = f(i - 1) + f(i - 2)$
    Initial: $f(0) = 1, f(1) = 2$

    $f(0)$ 代表選擇為空的方案數，$f(1)$ 代表選擇 $\{\emptyset, 1\}$ 兩種方案。
    
    對於位置 i 來說有兩種選擇:

    1. 選擇當前位置: 此時使用 $f(i - 2)$ 的方案加上自己組合成合法的方案

    2. 不選擇當前位置



