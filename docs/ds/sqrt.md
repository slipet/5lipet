# Square root concept

### 分塊思想

透過算幾不等式優化修改/查詢的複雜度。

$$\frac{a + b}{2} \ge \sqrt{ab}$$

在 $a = b$ 時等號會成立。

假設有陣列長度為 $n$ ，同時有 $q$ 個可以修改陣列區間 $[l, r)$ 的操作或是回答 $[l, r)$ 的詢問，此時暴力的複雜度為 $O(qn)$。

若是可以將陣列劃分成 $\lceil \frac{n}{B} \rceil$ 個區塊，每塊大小為 $\le B$，此時的複雜度為 $O(q(B + \frac{n}{B}))$。

透過算幾不等式可以知道:

\[
\begin{array}{ll}
    \frac{B + \frac{n}{B}}{2} &\ge \sqrt{B \times \frac{n}{B}} \\
    B + \frac{n}{B} &\ge 2\sqrt{n}\\
    B^2 - 2B\sqrt{n} + n &\ge 0 \\
    (B - \sqrt{n})^2 &\ge 0
\end{array}
\]

可以知道 $B = \frac{n}{B}$ 時等號會成立，此時 $\frac{B + \frac{n}{B}}{2}$ 會是最小值，因此可以得到:

$$B = \sqrt{n}$$

代回上面的複雜度可以得到 $O(q\sqrt{n})$。

* [leetcode #3943. 递增后的数对数量](https://leetcode.cn/problems/number-of-pairs-after-increment/description/)


```cpp
    static constexpr int MX = 1'000'000'001;
    vector<int> numberOfPairs(vector<int>& nums1, vector<int>& nums2, vector<vector<int>>& queries) {
        const int m = nums1.size(), n = nums2.size();
        int B = sqrt(m * n);

        //[l, r, cnt, add]
        vector<tuple<int, int, unordered_map<int, int>, int>> blocks;
        for(int i = 0; i < n; i += B) {
            int r = min(i + B, n);
            unordered_map<int, int> cnt;
            for(int j = i; j < r; ++j) {
                cnt[nums2[j]]++;
            }
            blocks.emplace_back(i, r, cnt, 0);
        }
        vector<int> ans;
        for(auto &q: queries) {
            if(q.size() == 2) {//query
                int sum = 0;
                for(auto &[_, __, cnt, add]: blocks) {
                    int tar = q[1] - add;
                    for(auto &x: nums1) {
                        auto it = cnt.find(tar - x);
                        if(it != cnt.end()) {
                            sum += it->second;
                        }
                    }
                }
                ans.push_back(sum);
            } else {
                int l = q[1], r = q[2] + 1, v = q[3];
                //[l, r), [bl, br)
                for(auto &[bl, br, cnt, add]: blocks) {
                    if (bl >= r) {
                        break;
                    }
                    if (br <= l || add >= MX) {
                        continue;
                    }
                    if (l <= bl && br <= r) {
                        add = min(add + v, MX); // 避免溢出
                        continue;
                    }
                    int L = max(bl, l);
                    int R = min(br, r);
                    for (int j = L; j < R; j++) {
                        cnt[nums2[j]]--; // 撤销旧的
                        nums2[j] = min(nums2[j] + v, MX); // 避免溢出
                        cnt[nums2[j]]++; // 添加新的
                    }
                }
            }
        }
        return ans;
    }
```

## Mo's Algo

* [莫队算法：块大小取多少合适？](https://zhuanlan.zhihu.com/p/1920472309522740969)

    $$B = \frac{n}{\sqrt{2q}}$$

* 注意 r 的遍歷

```cpp
class Solution {
public:
    vector<bool> validSubarrays(vector<int>& nums, int k, vector<vector<int>>& queries) {
        const int n = nums.size(), m = queries.size();
        vector<int> sorted = nums;
        ranges::sort(sorted);
        sorted.erase(ranges::unique(sorted).begin(), sorted.end());
        for(auto &x: nums) {
            x = ranges::lower_bound(sorted, x) - sorted.begin();
        }
        vector<int> cnt(sorted.size());
        int valid = 0;
        int sz = 0;
        auto move = [&] (int i, int op) -> void {
            int &x = nums[i];
            int &fq = cnt[x];
            if(op == 1) {
                sz += (fq == 0);
                fq++;
                if(fq & 1) valid++;
                else valid--;
                
            } else {
                if(fq & 1) valid--;
                else valid++;
                fq--;
                sz -= (fq == 0);
            }
        };
        struct Query {
            int bid;
            int l;
            int r;
            int qid;
        };
        int block_size = ceil(n / sqrt(m * 2));
        vector<Query> qs;
        vector<bool> ans(m, false);
        for(int i = 0; i < m; ++i) {
            auto &q = queries[i];
            int l = q[0], r = q[1] + 1;
            if(r - l > block_size) {
                qs.emplace_back(l / block_size, l, r, i);
                continue;
            }
            if(k * 2 > r - l) {
                continue;
            }
            for(int j = l; j < r; ++j) {
                move(j, 1);
            }
            if(sz == k && valid == 0) {
                ans[i] = true;
            }
            for(int j = l; j < r; ++j) {
                move(j, -1);
            }
        }
        ranges::sort(qs, {}, [](auto &q) { return pair(q.bid, q.r); });
        
        
        int l = 0, r = 0;
        for(auto &[_, ql, qr, qid]: qs) {
            //注意++, -- 的位置
            
            while (l < ql) move(l++, -1);
            while (l > ql) move(--l, 1);
            while (r < qr) move(r++, 1);
            while (r > qr) move(--r, -1);
            if(sz == k && valid == 0) ans[qid] = true;
        }
    
        return ans;
    }
};
```


* [3590. 第 K 小的路径异或和](https://leetcode.cn/problems/kth-smallest-path-xor-sum/description/)
* [4033. 有效 K 个不同元素子数组 I](https://leetcode.cn/problems/valid-k-unique-subarrays-i/description/)

### 回滾莫隊

```cpp
class Solution {
public:
    vector<int> subarrayMajority(vector<int>& nums, vector<vector<int>>& queries) {
        const int n = nums.size(), m = queries.size();
        int max_cnt = 0, min_val = 0;
        unordered_map<int, int> cnt;

        auto add = [&](int x) -> void {
            int c = ++cnt[x];
            if(c > max_cnt) {
                max_cnt = c;
                min_val = x;
            } else if(c == max_cnt) {
                min_val = min(min_val, x);
            }
        };
        struct Query {
            int bid;
            int l;
            int r;
            int threshold;
            int qid;
        };
        int block_size = ceil(n / sqrt(m * 2));
        vector<Query> qs;
        vector<int> ans(m, -1);
        for(int i = 0; i < m; ++i) {
            auto &q = queries[i];
            int l = q[0], r = q[1] + 1, threshold = q[2];
            
            if(r - l > block_size) {
                qs.emplace_back(l / block_size, l, r, threshold, i);
                continue;
            }

            for(int j = l; j < r; ++j) {
                add(nums[j]);
            }
            if(max_cnt >= threshold) {
                ans[i] = min_val;
            }
            cnt.clear();
            max_cnt = 0;
        }
        ranges::sort(qs, {}, [](auto &q){ return pair(q.bid, q.r);});
        int r;
        for(int i = 0; i < qs.size(); ++i) {
            auto &q = qs[i];
            int l0 = (q.bid + 1) * block_size;
            if(i == 0 || q.bid > qs[i - 1].bid) {
                r = l0;
                cnt.clear();
                max_cnt = 0;
            }
            for(; r < q.r; ++r) {
                add(nums[r]);
            }
            int tmp_max_cnt = max_cnt, tmp_min_val = min_val;
            for(int j = q.l; j < l0; ++j) {
                add(nums[j]);
            }
            if(max_cnt >= q.threshold) {
                ans[q.qid] = min_val;
            }
            max_cnt = tmp_max_cnt;
            min_val = tmp_min_val;
            for(int j = q.l; j < l0; ++j) {
                cnt[nums[j]]--;
            }
        }
        return ans;
    }
};
```
