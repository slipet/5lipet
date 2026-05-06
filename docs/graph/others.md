# Others

## Topological Sort/拓樸排序

常用來判環，用拓樸排序判環的好處是直觀，不用像 DFS 維護當前路徑上的點

* [atcoder abc #456-E](http://127.0.0.1:8000/contest/atcoder/2026_S2/#atcoder-beginner-contest-456)

    分層圖找環。難點會在要想到如何建邊

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

## Eulerian circuit

```cpp
/*
"模板:
先確認歐拉圖的性質
1. indeg == outdeg
2. 點之間的連通性質

ans.push_back(init);"	"每經過一條邊就要刪一條邊

沒路可走就加入答案

最後reverse順序"
*/

auto dfs = [&](this auto&& dfs, string x) -> void {
    while(g[x].size() > 0) {
        auto &y = g[x].back();
        g[x].pop_back();
        dfs(y);
        ans.push_back(y);
    }
    return;
};
dfs(start);
//用 vis 剔除路徑
dfs(x) {
    for(int y: g[x]) {
          if(vis[y]) {
                 vis.insert(y);
                 dfs(y);
                 ans.push_back(y);
          }
    }
} 
dfs(init);
```