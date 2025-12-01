# Conclusion

## Bit

* $a | b \le a + b$

* $a | b = a + b - (a \& b)$

## Others

### 找中位數

樸素的作法

維護兩個對頂堆(heap, priority_queue, multiset)

插入操作需要 O(log(n))

可以在 O(1) 找到中位數

### 找區域中位數

1. 莫隊 + 對頂堆

2. 可持久化線段樹

[3762. 使数组元素相等的最小操作次数](https://leetcode.cn/problems/minimum-operations-to-equalize-subarrays/description/)

### 快速計算 x 減去 y 的倍數

x = k * y + t

* t = x % y
