Digits counting

* from high bit to low bit

```cpp
// ref: https://leetcode.cn/discuss/post/tXLS3i/
// 代码示例：返回 [low, high] 中的恰好包含 target 个 0 的数字个数
// 比如 digitDP(0, 10, 1) == 2
// 要点：我们统计的是 0 的个数，需要区分【前导零】和【数字中的零】，前导零不能计入，而数字中的零需要计入
int digitDP(int low, int high, int target) {
    string low_s = to_string(low);
    string high_s = to_string(high);
    int n = high_s.size();
    int diff_lh = n - low_s.size();
    vector memo(n, vector<int>(target + 1, -1));

    auto dfs = [&](this auto&& dfs, int i, int cnt0, bool limit_low, bool limit_high) -> int {
        if (cnt0 > target) {
            return 0; // 不合法
        }
        if (i == n) {
            return cnt0 == target;
        }

        if (!limit_low && !limit_high && memo[i][cnt0] >= 0) {
            return memo[i][cnt0];
        }

        int lo = limit_low && i >= diff_lh ? low_s[i - diff_lh] - '0' : 0;
        int hi = limit_high ? high_s[i] - '0' : 9;

        int res = 0;
        int d = lo;
        // 通过 limit_low 和 i 可以判断能否不填数字，无需 is_num 参数
        // 如果前导零不影响答案，去掉这个 if block
        if (limit_low && i < diff_lh) {
            // 不填数字，上界不受约束
            res = dfs(i + 1, cnt0, true, false);
            d = 1;
        }
        for (; d <= hi; d++) {
            // 统计 0 的个数
            res += dfs(i + 1, cnt0 + (d == 0), limit_low && d == lo, limit_high && d == hi);
            // res %= MOD;
        }

        if (!limit_low && !limit_high) {
            memo[i][cnt0] = res;
        }
        return res;
    };

    int ans = dfs(0, 0, true, true);
    return ans;
}
```

* from low bit to high bit

    * [Count No-Zero Pairs That Sum to](https://leetcode.cn/problems/count-no-zero-pairs-that-sum-to-n/)

        * [contest review](https://slipet.github.io/5lipet/contest/leetcode/2025/#weekly-contest-470)

    * [F. Jee, You See?](https://codeforces.com/problemset/problem/1670/F)