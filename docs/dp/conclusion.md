# Conclusion

放一些 DP 的心得/小結論

## 計算方案數

* Leetcode [#2320. 统计放置房子的方式数](https://leetcode.cn/problems/count-number-of-ways-to-place-houses/description/)

    Transition function: $f(i) = f(i - 1) + f(i - 2)$
    Initial: $f(0) = 1, f(1) = 2$

    $f(0)$ 代表選擇為空的方案數，$f(1)$ 代表選擇 {}



