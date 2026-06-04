# 樹上滑窗

普通的滑窗。

### 求限制路徑和下的滑窗

* 窗口內容會大幅變化，所以不能直接套陣列雙指針，用前綴和處理

* 透過二分/跳表 移動窗口

    
```cpp
vector<long long> pre{0};

void dfs(int u, int fa) {
    pre.push_back(pre.back() + val[u]);

    // 找最早的 l，使得 pre.back() - pre[l] <= K
    // 若 val 都非負，可以 lower_bound
    // 現在 path 就是 root -> u
    // 可以在 path 上做滑動窗口 / 二分 / hash / prefix
    long long need = pre.back() - K;
    int l = lower_bound(pre.begin(), pre.end(), need) - pre.begin();

    int len = (int)pre.size() - 1 - l + 1;

    for (int v : g[u]) {
        if (v != fa) dfs(v, u);
    }

    pre.pop_back();
}
    
```

* [leetcdoe #3425](https://leetcode.com/problems/longest-special-path/description/)

    因為題目要求路徑上的節點元素唯一，透過紀錄上一次相同節點位置移動左端點。

    * 注意離開節點要把原本位置還原回去。
    
```cpp
    vector<int> longestSpecialPath(vector<vector<int>>& edges, vector<int>& nums) {
        const int n = nums.size();
        vector<vector<pii>> g(n);
        for(auto &e: edges) {
            int u = e[0], v = e[1], w = e[2];
            g[u].emplace_back(v, w);
            g[v].emplace_back(u, w);
        }
        pii ans = {-1, 0};//len,node
        vector<int> dis = {0};
        unordered_map<int, int> last_dep;

        auto dfs = [&](this auto&&dfs, int x, int fa, int top_dep) -> void {
            int color = nums[x];
            int old_dep = last_dep[color];
            top_dep = max(top_dep, old_dep);

            ans = max(ans, pair(dis.back() - dis[top_dep], -((int)dis.size() - top_dep)));
            last_dep[color] = dis.size();
            for(auto &[y, w]: g[x]) {
                if(y != fa) {
                    dis.push_back(dis.back() + w);
                    dfs(y, x, top_dep);
                    dis.pop_back();
                }
            }
            last_dep[color] = old_dep;
        };
        dfs(0, -1, 0);
        return {ans.first, -ans.second};
    }
```