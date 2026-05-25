# DP on tree

* 樹形 DP 


### 樹形背包

基本上就是選或不選當前節點以及子節點所產生的轉移，透過使用簡單的例子比較好思考:

```cpp
  p
 / \   \
L   R   X
```

核心思想是計算以 p 為父節點 L/R 為子節點的結果，接著將前面得到的結果節看成新的 L' 且將 R 的下一個節點當成新的 R'，計算得到對於全部子節點結果。

* [3939. 统计有根树中不相邻子集的数目](https://leetcode.cn/problems/count-non-adjacent-subsets-in-a-rooted-tree/description/)

```cpp
using ll = long long;
using pll = pair<vector<ll>, vector<ll>>;
class Solution {
public:

    const int mod = 1'000'000'000 + 7;
    int countValidSubsets(vector<int>& parent, vector<int>& nums, int k) {
        const int n = nums.size();
        vector<vector<int>> g(n);
        for(int i = 0; i < n; ++i) {
            if(i == 0) continue;
            g[parent[i]].push_back(i);
        }
        auto dfs = [&](this auto &&dfs, int u) -> pll {
            vector<ll> f0(k);
            vector<ll> f1(k);
            int &x = nums[u];
            f0[0] = 1;
            f1[x % k] = 1;
            for(auto &v: g[u]) {
                auto [fy0, fy1] = dfs(v);
                vector<ll> nf0(k), nf1(k);
                for(int i = 0; i < k; ++i) {
                    ll v = fy0[i] + fy1[i];
                    if(v == 0) continue;
                    for(int j = 0; j < k; ++j) {
                        int s = (i + j) % k;
                        nf0[s] = (nf0[s] + v * f0[j]) % mod;
                    }
                }
                for(int i = 0; i < k; ++i) {
                    ll v = fy0[i];
                    if(v == 0) continue;
                    for(int j = 0; j < k; ++j) {
                        int s = (i + j) % k;
                        nf1[s] = (nf1[s] + v * f1[j]) % mod;
                    }
                }
                f0 = move(nf0);
                f1 = move(nf1);
            }
            return {f0, f1};
        };
        auto [f0, f1] = dfs(0);
        return (f0[0] + f1[0] - 1 + mod) % mod;
    }
};
```