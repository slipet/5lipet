# Conclusion

## Bit

* $a | b \le a + b$

* $a | b = a + b - (a \& b)$

---

## Others

### 找中位數

樸素的作法

1. 維護兩個對頂堆(heap, priority_queue, multiset)

插入操作需要 O(log(n))

可以在 O(1) 找到中位數

2. 二分，算 <= x 有幾個

---

### 找區域中位數

1. 莫隊 + 對頂堆

2. 可持久化線段樹

[3762. 使数组元素相等的最小操作次数](https://leetcode.cn/problems/minimum-operations-to-equalize-subarrays/description/)

---

### 快速計算 x 減去 y 的倍數

x = k * y + t

* t = x % y

---

### Minimax path(最小化最大路徑)

1. 二分 + DFS/BFS

二分猜最大邊的值，DFS/BFS 走 <= upperbound，判斷能否走完

總複雜度：O((n+m)logW)，W 是權重範圍（或最大值）

適合：單次 s→t 查詢，圖不一定是樹。
2 dijkstra

把普通的 dijkstra 中 + 改成 max

主要的想法是 <span style="color:red">走到 v 的路徑最大邊權 = max(走到 u 的最大邊權, u→v 這條邊)</span>

接著只有小於當前 <span style="color:red">走到 v 的路徑最大邊權</span> 我們才需要將 v 加入堆當中嘗試能否得到更好的解

* <span style="color:red"> 更高層次的說，dis cost 表達的是走當前路徑，若是有下一次同樣走到 v 想更新 v 表示是走不同的路徑到 v </span>

複雜度：O(mlogn)
適合：單次 s→t 查詢，圖不一定是樹。

3. MST + LCA

適合：多次 pair 查詢

還沒遇過這種題目先放著

### Interval(區間問題)

對於區間 $[l_1, r_1]$ 其關係可以如下:

![range](https://codeforces.com/predownloaded/f0/bc/f0bc055b37bc44373c14a2fd0b8c09492d63c46a.png)
