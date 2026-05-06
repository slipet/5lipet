# Shortest Path

最短路徑問題: 給定一張圖 $$G$，有 $V$ 個點， $E$ 條邊，每條邊有 $W$ 的權重，求點 $S \rightarrow T$ 路徑的最少花費。

### Floyd

求 "任意兩點" 之間的最短路的。

適用於任何圖，不管有向無向，邊權正負，但是最短路必須存在。(不能有個負環)

* 複雜度為 $O(V^3)$

```cpp
for (k = 1; k <= n; k++)
    for (x = 1; x <= n; x++)
        for (y = 1; y <= n; y++)
            f[x][y] = min(f[x][y], f[x][k] + f[k][y]);
```

* 延伸: 跟矩陣乘法的關係 [abc #445-F](https://slipet.github.io/5lipet/problems/atcoder/2026_S2/?h=floyd#atcoder-beginner-contest-445)


### Bellman–Ford/SPFA

可以求出有負權的圖的最短路

#### SPFA

```cpp
struct edge {
  int v, w;
};

vector<edge> e[MAXN];
int dis[MAXN], cnt[MAXN], vis[MAXN];
queue<int> q;

bool spfa(int n, int s) {
  memset(dis, 0x3f, (n + 1) * sizeof(int));
  dis[s] = 0, vis[s] = 1;
  q.push(s);
  while (!q.empty()) {
    int u = q.front();
    q.pop(), vis[u] = 0;
    for (auto ed : e[u]) {
      int v = ed.v, w = ed.w;
      if (dis[v] > dis[u] + w) {
        dis[v] = dis[u] + w;
        cnt[v] = cnt[u] + 1;  // 记录最短路经过的边数
        if (cnt[v] >= n) return false;
        // 在不经过负环的情况下，最短路至多经过 n - 1 条边
        // 因此如果经过了多于 n 条边，一定说明经过了负环
        if (!vis[v]) q.push(v), vis[v] = 1;
      }
    }
  }
  return true;
}
```

### Dijkstra

求 "非負權" 圖上 "單源" 最短路徑的算法

兩種寫法，視角分別為由點或邊出發。

* 稠密圖

* $O(V^2)$

```cpp
const int inf = INT_MAX / 2;
int dijkstra(int n, vector<vector<int>> &g, int start) {
    vector<int> dis(n, inf), done(n);
    dis[start] = 0;
    while(true) {
        int x = -1;
        for(int i = 0; i < n; ++i) {
            if(!done[i] && (x < 0 || dis[i] < dis[x])) {
                x = i;
            }
        }
        if(x < 0) {//finished
            return ranges::max(dis);
        }
        if(dis[x] == inf) {//can not reach
            return -1;
        }
        done[x] = true;
        for(int y = 0; y < n; ++y) {
            dis[y] = min(dis[y], dis[x] + g[x][y]);
        }
    }
}
```

* 稀疏圖

* $O(E\log{E})$

* priority_queue 可以被線段樹取代

```cpp
using pii = pair<int, int>;
const int inf = INT_MAX / 2;
int dijkstra(int n, vector<pii> &g) {//adj list: v, w
    vector<int> dis(n, inf);
    priority_queue<pii, vector<pii>, greater<>> pq;
    dis[0] = 0;
    pq.emplace(0, 0);
    while(!pq.empty()) {
        auto [du, u] = pq.top();
        pq.pop();
        if(du > dis[u]) continue;
        if(u == target) {
            return dis[u];
        }
        for(auto &[v, w]: g[u]) {
            int new_d = du + v;
            if(new_d < dis[v]) {
                dis[v] = new_d;
                pq.emplace(new_d, v);
            }
        }
    }
}
```

