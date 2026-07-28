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

    求 $(x_j, y_j) \prec (x_i, y_i)$ 的 $(x_j, y_j)$ 個數，先按照 $y$ 由大至小排序後用 BIT 維護 x。

* [POJ 2299 Ultra-QuickSort](https://vjudge.net/problem/POJ-2299)

    給定操作為交換相鄰元素，而 "最少交換次數（只允許相鄰交換）= 逆序對數量"
    * 每一次「相鄰交換」，只會消掉 剛好 1 個逆序對
    * 不會影響其他 pair 的順序關係

    * 如果是任意位置那就是交換環的問題

* [CF1311F Moving Points](https://codeforces.com/problemset/problem/1311/F)


#### 找區間內的一個區間/點

雖然同樣可以使用 BIT 解決，但是對於不是數點的題目，可以透過特定順序遍歷紀錄所需資訊，數點的題目通常會需要前綴和才能得到，因此會需要 BIT 這種資料結構維護。

* [atcoder #457-E Crossing Table Cloth](https://atcoder.jp/contests/abc457/tasks/abc457_e)

    使用後綴陣列表示 $<= L$ 的最靠近右端點 $R$


#### 弱遞增序列與嚴格遞增序列的互相轉換


假設序列中的元素都是**整數**，並且 index 從 `0` 開始。

---

## 1. 弱遞增轉嚴格遞增

給定弱遞增序列：

$$
x_0 \le x_1 \le \cdots \le x_{k-1}
$$

定義：

$$
y_i = x_i + i
$$

則轉換後滿足：

$$
y_0 < y_1 < \cdots < y_{k-1}
$$

### 證明

因為：

$$
x_i \le x_{i+1}
$$

所以：

$$
y_i = x_i + i
$$

而：

$$
y_{i+1} = x_{i+1} + i + 1
$$

因此：

$$
y_{i+1} - y_i
= x_{i+1} - x_i + 1
\ge 1
$$

所以：

$$
y_i < y_{i+1}
$$

### 轉換公式

$$
\boxed{y_i = x_i + i}
$$

---

## 2. 嚴格遞增轉弱遞增

給定嚴格遞增整數序列：

$$
y_0 < y_1 < \cdots < y_{k-1}
$$

定義：

$$
x_i = y_i - i
$$

則轉換後滿足：

$$
x_0 \le x_1 \le \cdots \le x_{k-1}
$$

### 證明

因為 $y_i$ 都是整數，且：

$$
y_i < y_{i+1}
$$

所以：

$$
y_{i+1} - y_i \ge 1
$$

因此：

$$
x_{i+1} - x_i
= (y_{i+1} - i - 1) - (y_i - i)
$$

$$
= y_{i+1} - y_i - 1
\ge 0
$$

所以：

$$
x_i \le x_{i+1}
$$

### 反向公式

$$
\boxed{x_i = y_i - i}
$$

---

## 3. 完整等價關係

對整數序列而言：

$$
x_0 \le x_1 \le \cdots \le x_{k-1}
$$

和：

$$
y_0 < y_1 < \cdots < y_{k-1}
$$

可以透過以下公式互相轉換：

$$
\boxed{y_i = x_i + i}
$$

$$
\boxed{x_i = y_i - i}
$$

可以快速記成：

$$
\boxed{\text{弱遞增} \xrightarrow{+i} \text{嚴格遞增}}
$$

$$
\boxed{\text{嚴格遞增} \xrightarrow{-i} \text{弱遞增}}
$$

---

## 4. 有區間限制時

假設：

$$
L \le x_0 \le x_1 \le \cdots \le x_{k-1} \le R
$$

令：

$$
y_i = x_i + i
$$

則下界為：

$$
y_0 = x_0 \ge L
$$

上界為：

$$
y_{k-1}
= x_{k-1} + k - 1
\le R + k - 1
$$

所以轉換後為：

$$
\boxed{
L \le y_0 < y_1 < \cdots < y_{k-1} \le R+k-1
}
$$

因此：

$$
\boxed{
L \le x_0 \le \cdots \le x_{k-1} \le R
}
$$

等價於：

$$
\boxed{
L \le y_0 < \cdots < y_{k-1} \le R+k-1
}
$$

其中：

$$
y_i = x_i+i
$$

$$
x_i = y_i-i
$$

---

## 5. 反方向的區間轉換

假設原本是：

$$
L \le y_0 < y_1 < \cdots < y_{k-1} \le R
$$

令：

$$
x_i = y_i-i
$$

則：

$$
x_0 = y_0 \ge L
$$

以及：

$$
x_{k-1}
= y_{k-1}-(k-1)
\le R-k+1
$$

所以：

$$
\boxed{
L \le x_0 \le x_1 \le \cdots \le x_{k-1} \le R-k+1
}
$$

也就是：

$$
\boxed{
L \le y_0 < \cdots < y_{k-1} \le R
}
$$

等價於：

$$
\boxed{
L \le x_0 \le \cdots \le x_{k-1} \le R-k+1
}
$$

---

## 6. 範例

假設：

$$
0 \le x_0 \le x_1 \le x_2 \le 4
$$

令：

$$
y_i = x_i+i
$$

則轉換後為：

$$
0 \le y_0 < y_1 < y_2 \le 6
$$

例如原序列為：

$$
(x_0,x_1,x_2)=(1,1,3)
$$

則：

$$
y_0=1+0=1
$$

$$
y_1=1+1=2
$$

$$
y_2=3+2=5
$$

因此：

$$
(y_0,y_1,y_2)=(1,2,5)
$$

反向轉換：

$$
x_0=1-0=1
$$

$$
x_1=2-1=1
$$

$$
x_2=5-2=3
$$

---

## 7. 組合計數應用

考慮：

$$
L \le x_0 \le x_1 \le \cdots \le x_{k-1} \le R
$$

這表示從區間 $[L,R]$ 中選擇 $k$ 個可以重複的整數，並按照非遞減順序排列。

轉換後為：

$$
L \le y_0 < y_1 < \cdots < y_{k-1} \le R+k-1
$$

這表示從區間：

$$
[L,R+k-1]
$$

選擇 $k$ 個互不相同的整數。

此區間包含的整數數量為：

$$
(R+k-1)-L+1=R-L+k
$$

所以方案數為：

$$
\boxed{\binom{R-L+k}{k}}
$$

如果原區間中有 $n$ 種數值，即：

$$
n=R-L+1
$$

則：

$$
R-L+k=n+k-1
$$

因此方案數為：

$$
\boxed{\binom{n+k-1}{k}}
$$

這就是重複組合公式。

---

## 8. 一般化：相鄰至少相差 $d$

若序列滿足：

$$
x_{i+1}-x_i \ge d
$$

希望轉換成普通嚴格遞增序列，可以定義：

$$
y_i=x_i-i(d-1)
$$

因為：

$$
y_{i+1}-y_i
$$

$$
=(x_{i+1}-x_i)-(d-1)
$$

$$
\ge d-(d-1)=1
$$

所以：

$$
y_i<y_{i+1}
$$

反向轉換為：

$$
x_i=y_i+i(d-1)
$$

因此：

$$
\boxed{
x_{i+1}-x_i\ge d
\quad\Longleftrightarrow\quad
y_i=x_i-i(d-1)\text{ 為嚴格遞增}
}
$$

特別地，弱遞增代表：

$$
x_{i+1}-x_i\ge 0
$$

代入 $d=0$：

$$
y_i=x_i-i(0-1)=x_i+i
$$

就是原本的轉換公式。

---

## 9. 注意事項

這個雙向等價需要序列中的元素是**整數**。

從弱遞增轉成嚴格遞增：

$$
x_i\le x_{i+1}
\Longrightarrow
x_i+i<x_{i+1}+i+1
$$

對整數和實數都成立。

但是反向轉換時，從：

$$
y_i<y_{i+1}
$$

要得到：

$$
y_{i+1}-y_i\ge1
$$

需要 $y_i$ 是整數。

例如實數序列：

$$
y_0=0.9,\qquad y_1=1
$$

雖然：

$$
y_0<y_1
$$

但是反向轉換：

$$
x_0=y_0-0=0.9
$$

$$
x_1=y_1-1=0
$$

得到：

$$
x_0>x_1
$$

因此對實數而言，反向不一定成立。

---

## 10. 快速記憶

### 弱遞增轉嚴格遞增

$$
\boxed{y_i=x_i+i}
$$

### 嚴格遞增轉弱遞增

$$
\boxed{x_i=y_i-i}
$$

### 有區間限制

$$
\boxed{
L\le x_0\le\cdots\le x_{k-1}\le R
}
$$

等價於：

$$
\boxed{
L\le y_0<\cdots<y_{k-1}\le R+k-1
}
$$

* [1621. 大小为 K 的不重叠线段的数目](https://leetcode.cn/problems/number-of-sets-of-k-non-overlapping-line-segments/description/)