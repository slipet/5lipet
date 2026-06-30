# Conclusion

## Bit

* $a | b \le a + b$

* $a | b = a + b - (a \& b)$

---

## Others

### Indirect

有時候不一定要直接求答案，因為直接從原點出發到答案的複雜度會太高，可以透過求複雜度比較小的中間點搭一座橋抵達答案。

* [1449. 数位成本和为目标值的最大数字](https://leetcode.cn/problems/form-largest-integer-with-digits-that-add-up-to-target/description/)

### 找中位數

樸素的作法

1. 維護兩個對頂堆(heap, priority_queue, multiset)

插入操作需要 O(log(n))


```cpp
template<typename T, typename Compare = less<T>>
class LazyHeap {
    priority_queue<T, vector<T>, Compare> pq;
    unordered_map<T, int> remove_cnt; // 每个元素剩余需要删除的次数
    size_t sz = 0; // 实际大小
    long long s = 0; // 堆中元素总和

    // 正式执行删除操作
    void apply_remove() {
        while (!pq.empty() && remove_cnt[pq.top()] > 0) {
            remove_cnt[pq.top()]--;
            pq.pop();
        }
    }

public:
    size_t size() {
        return sz;
    }

    long long sum() {
        return s;
    }

    // 删除
    void remove(T x) {
        remove_cnt[x]++; // 懒删除
        sz--;
        s -= x;
    }

    // 查看堆顶
    T top() {
        apply_remove();
        return pq.top();
    }

    // 出堆
    T pop() {
        apply_remove();
        T x = pq.top();
        pq.pop();
        sz--;
        s -= x;
        return x;
    }

    // 入堆
    void push(T x) {
        if (remove_cnt[x] > 0) {
            remove_cnt[x]--; // 抵消之前的删除
        } else {
            pq.push(x);
        }
        sz++;
        s += x;
    }

    // push(x) 然后 pop()
    T push_pop(T x) {
        apply_remove();
        pq.push(x);
        s += x;
        x = pq.top();
        pq.pop();
        s -= x;
        return x;
    }
};

// 480. 滑动窗口中位数（有改动）
// 返回 nums 的所有长为 k 的子数组的（到子数组中位数的）距离和
vector<long long> medianSlidingWindow(vector<int>& nums, int k) {
    int n = nums.size();
    vector<long long> ans(n - k + 1);
    LazyHeap<int> left; // 最大堆
    LazyHeap<int, greater<>> right; // 最小堆

    for (int i = 0; i < n; i++) {
        // 1. 进入窗口
        int in = nums[i];
        if (left.size() == right.size()) {
            left.push(right.push_pop(in));
        } else {
            right.push(left.push_pop(in));
        }

        int l = i + 1 - k;
        if (l < 0) { // 窗口大小不足 k
            continue;
        }

        // 2. 计算答案
        long long v = left.top();
        long long s1 = v * left.size() - left.sum();
        long long s2 = right.sum() - v * right.size();
        ans[l] = s1 + s2;

        // 3. 离开窗口
        int out = nums[l];
        if (out <= left.top()) {
            left.remove(out);
            if (left.size() < right.size()) {
                left.push(right.pop()); // 平衡两个堆的大小
            }
        } else {
            right.remove(out);
            if (left.size() > right.size() + 1) {
                right.push(left.pop()); // 平衡两个堆的大小
            }
        }
    }
    
    return ans;
}
```

可以在 O(1) 找到中位數

1. 二分，算 <= x 有幾個

```cpp
#define id(x) (x + 1)

class FenwickTree {
    const int high_bit;
    const vector<int>& sorted;
    vector<int> cnt;
    vector<long long> sum;

public:
    FenwickTree(const vector<int>& sorted) : 
        cnt(sorted.size() + 1), 
        sum(sorted.size() + 1), 
        sorted(sorted), 
        high_bit(1 << (bit_width(sorted.size()) - 1)) {}

    // 添加 num 个 val，其中 val 离散化后的值为 i（i 从 1 开始）
    // 如果 num < 0，表示减少 -num 个 val
    void update(int i, int num, int val) {
        for (; i < cnt.size(); i += i & -i) {
            cnt[i] += num;
            sum[i] += val;
        }
    }

    // 返回第 k 小的数（k 从 1 开始）
    int kth(int k) const {
        int i = 0;
        for (int b = high_bit; b > 0; b >>= 1) {
            int nxt = i | b;
            if (nxt < cnt.size() && cnt[nxt] < k) {
                k -= cnt[nxt];
                i = nxt;
            }
        }
        return sorted[i];
    }

    // 返回前 k 小的数之和（k 从 1 开始）
    long long pre_sum(int k) const {
        long long s = 0;
        int i = 0;
        for (int b = high_bit; b > 0; b >>= 1) {
            int nxt = i | b;
            if (nxt < cnt.size() && cnt[nxt] < k) {
                k -= cnt[nxt];
                s += sum[nxt];
                i = nxt;
            }
        }
        // 加上等于第 k 小的数
        return s + 1LL * sorted[i] * k;
    }
};

```

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

由於每次都是從右到左新增時間點，如果把連續的時間點看成閉區間，那麽從右到左新增時間點，會把若幹右側的區間合並成一個大區間，也就是從 end 倒著開始，先合並右邊，再合並左邊，因此可以用棧來優化

* Leetcode
  * [#757. 设置交集大小至少为2](https://leetcode.cn/problems/set-intersection-size-at-least-two/)
  * [#2589. 完成所有任务的最少时间](https://leetcode.cn/problems/minimum-time-to-complete-all-tasks/description/)

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


### 維護相鄰元素距離

* 使用有序集合 set, multiset 維護集合元素
* 使用二分確定相鄰元素的距離後，操作維護

* [abc #444 E - Sparse Range](https://atcoder.jp/contests/abc444/tasks/abc444_e)

### 不等式

假設有 k, 和 n - k 要作用於區間上，可以把 $L \le k \le R$ 的限制，同樣操作在 $n - k$ 上面為:


$$L \le n - k \le R \rightarrow n - R \le k \le n - L$$


### Grid/網格圖

網格圖的一小結論

* 原點到反斜線上的距離 $d$ 都會是相等:

    可以利用這個性質把多個點的資訊進行壓縮，$(r0, c0), (r1, c1) ...\rightarrow (d - c0, c0), (d - c1, c1) ...$
    * [leetcode #741](https://slipet.github.io/5lipet/problems/leetcode/2026_S2/#741-cherry-pickup)

    這也就是說在反對角線上的座標 $(r, c)$ 會有 $d = r + c$

```
id: for 4 x 4
0 1 2 3
1 2 3 4
2 3 4 5
3 4 5 6
```

* 平行於對角線的斜線上的座標 $(r, c)$ 有 $r - c = t$ 為一定值

    * 對於 r < c 也就是在對角線右上方的斜線，有 $r - c = t, t < 0$

    * 對於 r == c 對角線上的點，有 $r - c = 0$

    * 對於 r > c 也就是在對角線左下方的斜線，有 $r - c = t, t > 0$

    枚舉對角線的通常會加一個 offset，設 $n \times n$ 的網格圖:

    $$id = r - c + (n - 1)$$

```
id: for 4 x 4
3 2 1 0
4 3 2 1
5 4 3 2
6 5 4 3
```

* 網格圖的交錯性質

把網格圖的位置 $(r, c)$ 相加後 r + c 可以發現他們的奇偶會交錯的排列

若是在網格圖上的路徑一定會是黑白/奇偶交錯: $W \rightarrow B \rightarrow W ....$

```
for 4 x 4
O X O X
X O X O
O X O X
```

可以利用經過的黑白格子數，解決一些問題/得到一些結論。
