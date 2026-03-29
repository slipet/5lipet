# Order theory

Dilwoths's theorem: Suppose that the length of the longest antichain in the poset P is r, then P can be partitioned into r chains.

Dilworth's dual theorem: Suppose that the length of the longest chain in the poset P is r , then P can be partitioned into r antichains.

Erdős-Szekeres theorem: Given n ≥ rs + 1 , any sequence of n elements has either an increasing subsequence of length r+1 or a decreasing subsequence of lengthe s+1.

推薦這個影片講解非常清楚:

[證明](https://www.bilibili.com/video/BV1VNCGY3ERK/?spm_id_from=333.337.search-card.all.click&vd_source=caaccd1459c5ece44b5e2d37804871b8)

其他相關資料:

[material 1](https://chengzhaoxi.xyz/2bffd40.html)

[material 2](https://math.stackexchange.com/questions/1284972/proof-for-erd%C5%91s-szekeres-theorem-using-dilworths-theorem)

[material 3](https://math.stackexchange.com/questions/1208735/understanding-dilworths-theorem)



### 遍歷逆序位置

在一堆上升區間中，快速的遍歷逆序的位置的優化方式。

用一個陣列儲存下次逆序位置。

```cpp
vector<int> nxt_dec(n);
nxt_dec[n - 1] = n;
int p = n;
for(int i = n - 2; i >= 0; --i) {
    if(nums[i] > nums[i + 1]) p = i;
    nxt_dec[i] = p;
}
```