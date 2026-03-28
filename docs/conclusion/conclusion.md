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


#### 區間合併

* Monotonic Stack + Binary Search 模板

暴力的做法是將所有點都放進一個有序陣列中維護，進階作法是利用單調 stack 作區間合併。

```cpp
class Solution {
public:
    int findMinimumTime(vector<vector<int>>& tasks) {
        ranges::sort(tasks, {}, [](auto& a) { return a[1]; }); // 按照右端点从小到大排序
        // 栈中保存闭区间左右端点，栈底到栈顶的区间长度的和
        vector<array<int, 3>> st = {{-2, -2, 0}}; // 哨兵，保证不和任何区间相交
        for (auto& t : tasks) {
            int start = t[0], end = t[1], d = t[2];
            //[---] [----]  start [l----r] end
            auto [_, r, s] = *--ranges::lower_bound(st, start, {}, [](auto& x) { return x[0]; });
            //[---s0] [----s1] start [s2] [s3]  end
            d -= st.back()[2] - s; // 去掉运行中的时间点(因為事先排序所以可以這樣減)
            //[---s0] [--start{--r]  [s2] [s3]  } end
            if (start <= r) { // start 在区间 st[i] 内
                d -= r - start + 1; // 去掉运行中的时间点
            }
            if (d <= 0) {
                continue;
            }
            while (end - st.back()[1] <= d) { // 剩余的 d 填充区间后缀
                auto [l, r, _] = st.back();
                st.pop_back();
                //[---] [----]  start [l----r] end
                d += r - l + 1; // 合并区间
            }
            st.push_back({end - d + 1, end, st.back()[2] + d});
        }
        return st.back()[2];
    }
};
```


### 子陣列最大 XOR sum

在陣列中找 XOR 最大的數

1. 0/1 Trie

2. 試填法


### 分配問題

1. 分配給兩組

枚舉分配個數

* leetcode:

    * [3664. 两个字母卡牌游戏](https://leetcode.cn/problems/two-letter-card-game/description/)


### 區間修改子樹(DFS 時間戳)



