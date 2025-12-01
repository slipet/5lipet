# Conclusion

## Bit

* $a | b \le a + b$

* $a | b = a + b - (a \& b)$

## Others

### 找中位數

樸素的作法

用兩個 multiset 平均的放入元素

插入操作需要 O(log(n))

可以在 O(1) 找到中位數


### 快速計算 x 減去 y 的倍數

x = k * y + t

* t = x % y
