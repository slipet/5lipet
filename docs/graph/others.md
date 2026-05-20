# Others

## Topological Sort/拓樸排序

做拓樸排序首先要確定有<span style="color:red">多少起點多少終點，以及邊的連通性質</span>:

1. 多少起點
2. 多少終點
3. 邊的性質
4. 有沒有其他子圖

常用來判環，用拓樸排序判環的好處是直觀，不用像 DFS 維護當前路徑上的點

* [atcoder abc #456-E](http://127.0.0.1:8000/contest/atcoder/2026_S2/#atcoder-beginner-contest-456)

    分層圖找環。難點會在要想到如何建邊

* [1059. All Paths from Source Lead to Destination](https://leetcode.com/problems/all-paths-from-source-lead-to-destination/)

    觀察圖有哪些可能的性質:
    1. 有環
    2. 自環: 這三個特性都會使得用dfs會相對來說麻煩-> 環需要額外的參數標記當前路徑
    3. 有重邊: 重邊其實沒差
    4. 有可能有其他圖不在src 和 des 的連通分量中

    因為是確定起點至終點的節點順序->拓樸排序
    發現使用indegree的方式，因為是順序的會有可能有許多分岔

    若是從終點往回走當抵達起點則表示一定可以到達，<span style="color:red">中途有環或自環都會使得沒辦法抵達</span>
    從終點往回走還有一個好處，避免分岔，若是有從起點方向產生的分岔，則會因為outdegree 沒辦法減至0而無法抵達起點


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