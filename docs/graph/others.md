# Others

## Topological Sort/拓樸排序

常用來判環，用拓樸排序判環的好處是直觀，不用像 DFS 維護當前路徑上的點

```cpp
vector<int> topologicalSort(int n, vector<vector<int>> &edges) {
    vector<vector<int>> g(n);
    vector<int> deg(n);//indeg
    for(auto &e: edges) {
        int u = e[0], v = e[1];
        g[u].push_back(v);
        deg[v]++;
    }
    queue<int> q;
    for(int i = 0; i < n; ++i) {
        if(deg[i] == 0) q.push(i);
    }
    vector<int> topo_order;
    while(!q.empty()) {
        auto u = q.front();
        q.pop();
        topo_order.push_back(u);
        //DP 更新位置，對點操作
        for(auto &v: g[u]) {
            //DP 更新位置，對邊操作
            deg[v]--;
            if(deg[v] == 0) {
                q.push(v);
            }
        }
    }
    //用來判環，單純判環可用 cnt 簡單計數
    if(topo_order.size() < n) {
        return {};
    }
    return topo_order;
}
```
