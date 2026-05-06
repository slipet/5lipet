# Minimum Spanning Tree(MST)

最小生成樹問題

* 定義 <span style="color:red">無向連通圖</span> 的 最小生成樹（Minimum Spanning Tree，MST）為邊權和最小的生成樹。

* 只有 "連通圖" 才有生成樹，而對於 "非連通圖" ，只存在生成森林。


### Kruskal 

貪心思想，從小到大加入邊

維護一堆集合，不斷的由小到大加入邊，查詢邊的兩端點是否同屬一個集合，合併兩個不同的集合

* 查詢兩點是否連通可以使用 DSU

* 因為需要 $O(m\log{m})$ 事先排序加上 $O(E\alpha(E, V))$ 或 $O(E\log{V})$ 的 DSU，所以時間複雜度為 $O(m\log{m})$。

```cpp
//vertex = [0, n - 1]
//edges[i] = u, v, w
vector<vector<int>> Kruskal(int n, vector<vector<int>> &edges) {
    vector<vector<int>> g(n);
    UnionFind dsu(n);
    int cnt = 0;
    ranges::sort(edges, {}, [](const auto &e) { return e[2]; });
    
    for(auto &e: edges) {
        int u = e[0], v = e[1], w = e[2];
        if(dsu.is_same(u, v)) continue;
        dsu.merge(u, v);
        g[u].push_back(v);
        g[v].push_back(u);
        cnt++;
        if(cnt == n - 1) {
            return g;
        }
    }
    //這裡回傳 {} 是假設傳入的圖為連通圖
    return {};
}
```

### Prim

視角由點出發不斷地加點(Kruskal 是不斷的加邊)。

每次選擇距離最小的一個節點，以及用新的邊更新其他節點的距離。

暴力(稠密圖): $O(n^2 + m)$

```cpp
//vertex = [0, n - 1]
//edges[i] = u, v, w
const int inf = INT_MAX / 2;
vector<int> Prim(const vector<vector<int>> &adj) {
    const int n = adj.size();
    int cnt = 0;
    vector<int> dis(n, inf), parent(n, -1), done(n);
    //dis 的意義為當前集合到這些點的距離
    dis[0] = 0;
    parent[0] = 0;
    while(true) {
        int x = -1;
        for(int i = 0; i < n; ++i) {
            if(!done[i] && (x < 0 || dis[i] < dis[x])) {
                x = i;
            }
        }
        if(x < 0) {//complete
            return parent;
        }
        if(dis[x] == inf) {//fail
            return {};
        }
        done[x] = true;
        cnt++;//check how many nodes processed
        for(int y = 0; y < n; ++y) {
            if (!done[y] && adj[x][y] < dis[y]) {
                dis[y] = adj[x][y];
                parent[y] = x;
            }
        }
    }
}
```

優先佇列優化(稀疏圖): $O((n + m) \log{n})$