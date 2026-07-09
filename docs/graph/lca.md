# LCA Binary Lifting

## 樹上倍增

```cpp
class LcaBinaryLifting {
    vector<int> depth;
    vector<long long> dis; // 如果是无权树（边权为 1），dis 可以去掉，用 depth 代替
    vector<vector<int>> pa;

public:
    LcaBinaryLifting(vector<vector<int>>& edges) {
        int n = edges.size() + 1;
        int m = bit_width((unsigned) n); // n 的二进制长度
        vector<vector<pair<int, int>>> g(n);
        for (auto& e : edges) {
            int x = e[0], y = e[1], w = e[2];
            g[x].emplace_back(y, w);
            g[y].emplace_back(x, w);
        }

        depth.resize(n);
        dis.resize(n);
        pa.resize(n, vector<int>(m, -1));

        auto dfs = [&](this auto&& dfs, int x, int fa) -> void {
            pa[x][0] = fa;
            for (auto& [y, w] : g[x]) {
                if (y != fa) {
                    depth[y] = depth[x] + 1;
                    dis[y] = dis[x] + w;
                    dfs(y, x);
                }
            }
        };
        dfs(0, -1);

        for (int i = 0; i < m - 1; i++) {
            for (int x = 0; x < n; x++) {
                if (int p = pa[x][i]; p != -1) {
                    pa[x][i + 1] = pa[p][i];
                }
            }
        }
    }

    int get_kth_ancestor(int node, int k) {
        for (; k; k &= k - 1) {
            node = pa[node][countr_zero((unsigned) k)];
        }
        return node;
    }

    // 返回 x 和 y 的最近公共祖先（节点编号从 0 开始）
    int get_lca(int x, int y) {
        if (depth[x] > depth[y]) {
            swap(x, y);
        }
        y = get_kth_ancestor(y, depth[y] - depth[x]); // 使 y 和 x 在同一深度
        if (y == x) {
            return x;
        }
        for (int i = pa[x].size() - 1; i >= 0; i--) {
            int px = pa[x][i], py = pa[y][i];
            if (px != py) {
                x = px;
                y = py; // 同时往上跳 2^i 步
            }
        }
        return pa[x][0];
    }

    // 返回 x 到 y 的距离（最短路长度）
    long long get_dis(int x, int y) {
        return dis[x] + dis[y] - dis[get_lca(x, y)] * 2;
    }
};
```


## 其他倍增的模板

先得到跳 $2^0$ 步的距離，再求跳 $2^k$ 的距離

需要開的空間為 $\log{D}$，$D$ 為最遠距離。

```cpp

//**step 1: init ** 
int mx = bit_width((uint32_t) n);//算最多跳幾步


//**step 2: init 2^0* 
// 双指针，从第 i 小的数开始，向左一步，最远能跳到第 left 小的数
vector pa(n, vector<int>(mx));
int left = 0;
for (int i = 0; i < n; i++) {
    while (nums[idx[i]] - nums[idx[left]] > maxDiff) {
        left++;
    }
    pa[i][0] = left;
}

//**step 3: cal [2^1, 2^k]* 
// 倍增
for (int i = 0; i < mx - 1; i++) {
    for (int x = 0; x < n; x++) {
        int p = pa[x][i];
        pa[x][i + 1] = pa[p][i];
    }
}


//**step 4: cal [2^1, 2^k]* 
///cal ans
int res = 0;
for(int k = mx - 1; k >= 0; k--) {
    if(pa[l][k] < r) {
        res |= 1 << k;
        l = pa[l][k];
    }
}
```