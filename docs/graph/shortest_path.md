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

### Dijkstra