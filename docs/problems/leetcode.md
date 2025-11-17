# Leetcode

### 2350. Shortest Impossible Sequence of Rolls

* Score: 1961

* Quality: 0 

* Date: 2025/10/03

* [link](https://leetcode.cn/problems/shortest-impossible-sequence-of-rolls/description/)

* Comment:

沒有很好理解題目，對於構造子序列的理解不夠，這題類似 #3660 ，重點應該是觀察到必須將元素進行分組 

### 3660. Jump Game IX

* Score: 2187

* Quality: 6

* Date: 2025/10/03

* [link](https://leetcode.com/problems/jump-game-ix/description/)

* Comment:

做過的題目，分析的速度不夠快

### 517. Super Washing Machines 

* Score: unkown

* Quality 0

* Date: 2025/10/07

* [link](https://leetcode.cn/problems/super-washing-machines/solutions/1023234/acmjin-pai-ti-jie-tan-xin-bian-cheng-xio-mp7n/)

* Comment:

沒有想到兩邊雙向擴散的案例吃了一個 WA，並且沒有想到如何解。可以將多的看成 正，少的看成 負，對於一個區間內若是所需 < 0 則表示需要從其他地方流入，> 0 則表示可以從這個區間流出

### 2811. 判断是否能拆分数组

* Score: 1543

* Quality 6

* Date: 2025/10/22

* [link](https://leetcode.cn/problems/check-if-it-is-possible-to-split-array/description/)

* Comment: 
沒有認真去思考，有想到關鍵點是當長度為二時，若是和小於 m 可能沒辦法切分，但是因為範例三可以透過其他陣列幫助切分，所以沒有想到是當所有長度為二的子陣列都小於二時才會無法切分，沒有觀察到問題的本質

### 1503. 所有蚂蚁掉下来前的最后一刻

* Score: 1619

* Quality: 6

* Date: 2025/10/28

* [link](https://leetcode.cn/discuss/post/3091107/fen-xiang-gun-ti-dan-tan-xin-ji-ben-tan-k58yb/)

* Comment: 

沒有掌握到問題的核心，一開始想用簡化問題的方式將連續同方向的元素看做一個整體，接著想用剝洋蔥的方式計算答案，但是中途感覺這樣太複雜了。正確的核心想法是對於兩個不同方向的元素，相撞後可以看做互相交換身分或是穿越過去。(這類問題應該都是這種套路)

### 2712. 使所有字符相等的最小成本

* Score: 1791

* Quality: 8

* Date: 2025/11/06

* [link](https://leetcode.cn/problems/minimum-cost-to-make-all-characters-equal/solutions/2286922/yi-ci-bian-li-jian-ji-xie-fa-pythonjavac-aut0/)

* Comment: 

這題我將連續的0/1分組後使用前後綴分解完成，雖然是O(n)的方法但是不是最佳解，離問題的本質還差一點。一開始有想到使用　min(i + 1, n - i)，但考慮的方式是每組連續的 0/1，若是可以拋棄分組的想法，將 0/1 交界處看成如何將這兩半部分變成同一元素的話會豁然開朗，將前半部翻轉成後半需要 i, 反之需要 n - i -> min(i, n - i)。

### 2556. 二进制矩阵中翻转最多一次使路径不连通

* Score: 2369

* Quality: 6

* Date: 2025/11/17

* [link](https://leetcode.cn/problems/disconnect-path-in-a-binary-matrix-by-at-most-one-flip/solutions/2093243/zhuan-huan-cheng-qiu-lun-kuo-shi-fou-xia-io8x/)

* Comment:

這題的核心思想是，找到所有路徑的交集的關鍵點，若是只有一個關鍵點，則可以分割。我最後使用分別從原點和終點開始的雙向dfs找到每個點的路徑數，兩個方向相乘後可以得到所有經過的路徑數，若是跟原點的路徑總數一樣則代表只有一個關鍵點，但是這個方法除了要取模，並且為了避免被卡要在模數上加上隨機數。

靈神的解法是使用將所有路徑抽象看成一個圖，當我們沿著邊邊走，分別可以走兩種路線，上輪廓以及下輪廓，當如果刪除其中一條路徑時若無法透過另一條路徑抵達終點則表示只有一個關鍵點。這兩種做法的核心想法都是找路徑的交集，第二種做法比較圖像化，並且可能可以擴展到一般圖。
