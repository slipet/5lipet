# Greedy

## Median


### 中位數貪心

* [中位數貪心及其證明](https://zhuanlan.zhihu.com/p/1922938031687595039)

* 簡單證明 - 剝洋蔥法

* problem:
    

### Median alignment (中位數對齊問題)

    

* problem:

    * [Minimum Adjacent Swaps for K Consecutive Ones](https://leetcode.cn/problems/minimum-adjacent-swaps-for-k-consecutive-ones/description/)
    
    * [D. A and B](https://codeforces.com/contest/2149/problem/D)


## 思想/小結論

#### 替換/取代 操作

* 貢獻法

    對於取代或是替換操作，可以有兩種選擇:

    1. 不操作: 此時的貢獻是原本的 $g$

    2. 操作: 此時的貢獻是減去原本的 $g$ ，加上操作後產生的貢獻 $g'$

    $$cost = max(g, g' - g)$$

    * Leetcode [#2321. 拼接数组的最大分数](https://leetcode.cn/problems/maximum-score-of-spliced-array/description/)

