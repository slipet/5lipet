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


### 逆序對(Inversion)

* 嚴格逆序對個數 = 總數 - 非嚴格順序對個數


### 遍歷逆序位置

在一堆上升區間中，快速的遍歷逆序的位置的優化方式。

用一個陣列儲存下次逆序位置。

* $\text{nxt\_dec} [i]$ 指向當前上升區間的尾端。

```cpp
vector<int> nxt_dec(n);
nxt_dec[n - 1] = n;
int p = n;
for(int i = n - 2; i >= 0; --i) {
    if(nums[i] > nums[i + 1]) p = i;
    nxt_dec[i] = p;
}
```

### 二為偏序/2D Partial Ordering

已知點對的序列 $(a_1, b_1), (a_2, b_2), (a_3, b_3), ... $ 並在其上定義某種偏序關系 $\prec$，現有點 $(a_i, b_i)$，求滿足 $(a_j, b_j) \prec (a_i, b_i)$ 的 $(a_j, b_j)$ 的數量。

在二維的情形，我們分別對兩個屬性定義序關系，一定能得到一種偏序關系:

$$(a_j, b_j) \prec (a_i, b_i) \overset{def}{=} a_j \lesseqgtr a_i \text{ and } b_j \lesseqgtr b_i$$


* [逆序對](https://zhuanlan.zhihu.com/p/112504092)

    逆序對本質上就是一種二維偏序

    $$(j, a_j) \prec (i, a_i) \overset{def}{=} j < i \text{ and } a_j > a_i $$

一般的二維偏序問題可以用樹狀數組解決

* CF1575L
* [leetcode #3920](https://leetcode.cn/problems/maximize-fixed-points-after-deletions/description/)

    給很多二元組 (x, y)，按一定順序選出最多的二元組，使得它們滿足第一維單調不降，第二維單調遞增。

* [leetcode #2250](https://leetcode.cn/problems/count-number-of-rectangles-containing-each-point/description/)

* [leetcode #354](https://leetcode.cn/problems/russian-doll-envelopes/description/)

* [POJ 2352 Stars](https://vjudge.net/problem/POJ-2352#author=translator:1281309:zh)

    求 $(x_j, y_j) \prec (x_i, y_i)$ 的 (x_j, y_j) 個數，先按照 $y$ 由大至小排序後用 BIT 維護 x。

* [POJ 2299 Ultra-QuickSort](https://vjudge.net/problem/POJ-2299)

    給定操作為交換相鄰元素，而 "最少交換次數（只允許相鄰交換）= 逆序對數量"
    * 每一次「相鄰交換」，只會消掉 剛好 1 個逆序對
    * 不會影響其他 pair 的順序關係

    * 如果是任意位置那就是交換環的問題

* [CF1311F Moving Points](https://codeforces.com/problemset/problem/1311/F)

