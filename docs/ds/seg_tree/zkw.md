# ZKW 線段樹

* 疊代型線段樹/bottom-up

zkw 線段樹只能用於單種運算，不能處理運算優先級的問題，比如（加法+乘法）

```cpp
#define lc ((node) << 1)
#define rc ((node) << 1 | 1)


```

