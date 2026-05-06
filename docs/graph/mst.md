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
vector<vector<int>> kruskal(int n, vector<vector<int>> &edges) {
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