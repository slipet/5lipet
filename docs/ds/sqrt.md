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