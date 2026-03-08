# Connected Components

[OI wiki](https://oi-wiki.org/graph/scc/)

## Strong Connected Components/強連通分量 (SCC)

強連通的定義是：有向圖 G 強連通是指，G 中任意兩個節點連通。

強連通分量（Strongly Connected Components，SCC）的定義是：極大的強連通子圖。

### Tarjan algorithm

* DFS 生成樹

![photo](https://oi-wiki.org/graph/images/dfs-tree.svg)

1. 樹邊(tree edge): DFS 搜索到一個還沒遍歷過節點，上圖的的黑色邊

2. 返祖邊(back edge): 指向祖先節點的邊，上圖的紅色邊。

3. 橫叉邊(cross edge): 搜索時訪問到一個遍歷過的節點，但不是祖先節點，上圖的藍色邊。

4. 前向邊(forward edge): 訪問子樹中已經遍歷過的節點，上圖綠色邊。

* SCC 的根: 搜索時在 SCC 中第一個訪問的節點。而此 SCC 的其他節點會在節點的子樹中。
