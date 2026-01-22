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

### 780. 到达终点

* Score: 1897

* Quality: 0

* Date: 2025/11/24

* [link](https://leetcode.cn/problems/reaching-points/solutions/1405998/by-ac_oier-hw11/)

* Comment:

這題其實跟輾轉相除法很像，但是做題的時候沒有想到使用 % 就可以快速的計算減去倍數後剩下的值，所以不知道怎麼做

### 991. 坏了的计算器

* Score: 1909

* Quality: 0

* Date: 2025/11/26

* [link](https://leetcode.cn/problems/broken-calculator/description/)

* Comment:

反向思考外，要看最後一步該如何處理，如果 target 為偶數 -> 除2，如果為奇數 -> +1 然後繼續除 2 直到 target < start。
最後將 target 加到等於 start。

如果用遞迴的方式會比較好想，target 將除 2 把問題規模縮小，遇到奇數時將奇數化為偶數繼續除 2 。

但是貪心的證明方式會比較難，

假設我們要 X -> Y，有 * 2 , - 1 兩種操作。

當 X > Y ， 只能將 X 不斷 -1 共有 X - Y 次操作。

當 X < Y ， 將 Y 用 奇偶 分別討論(這邊用奇偶討論會是一個比較需要經驗的方式，今天寫的時候也突然沒有想到這個分別討論的方式)。

若是 Y 為奇數，考慮最後一步 肯定是由 Y + 1 做 -1 得來的 可以知道 Y = op(Y + 1) + 1 ， 因為 *2 沒辦法得到奇數。

若是 Y 為偶數，則要馬是 Y = 2 * T 或是 Y = (2 * T + 1) - 1 得到

若由 Y = 2 * T 得到，則 Y = op(T) + 1

若由 Y = (2 * T + 1) - 1 得到，則 Y = op(2 * T + 1) + 1 ， 可以知道 2 * T + 1 必為奇數，所以可以從上面的那個等式得到 Y = op(2 * T + 2) + 2

此時 2 * T + 2 為一個偶數，可以由 2 * (T + 1) 或是 2 * (T + 2) - 2 得到

所以操作的序列可能是這樣 X -> .... T + K, 2(T + K), 2(T + K) - 1, 2(T + K) - 2, ... Y 此時會需要 2 K + 1

若是最後才 * 2 的序列會是這樣 X -> .... T + K, T + K - 1, T + K - 2, ... T, 2 * T, ... Y 此時會需要 K + 1

所以對於偶數來說 Y / 2 會是更好的選擇。

### 1354. 多次求和构造目标数组 

* Score: 2015

* Quality: 8

* Date: 2025/11/28

* [link](https://leetcode.cn/problems/construct-target-array-with-multiple-sums/solutions/3832109/ni-xiang-si-wei-pythonjavacgojsrust-by-e-0701/)

* Comment:

調了很多次才過，沒有清楚定義問題的本質，這題的重點在如何找出最大的元素以及如何維護陣列和 sum 。

考慮最後一步，會將其中一元素改為陣列和，所以陣列中最大元素勢必是上一次的陣列和，我們可以透過減去剩餘陣列和 $rest = sum - mx$ 還原最大值 $mx - rest$，但是這個步驟可以做不只一次。

ex. [1, 1000000000] -> 一次一次減會導致 TLE

所以要利用 mod 計算減到 > rest 。

* 小結論

把 x ≤ sum 為止（不能減成 0 或者負數），也就是計算:


\[
\left\{
\begin{array}{ll}
    x \pmod{sum} , \quad x \pmod{sum}  > 0 \\
    sum, \quad x \pmod{sum} = 0
\end{array}
\right.
\]

* <span style="color:red">這等價於 (x−1) mod sum + 1</span> 

### 2543. 判断一个点是否可以到达

* Score: 2221

* Quality: 9

* Date: 2025/11/29

* [link](https://leetcode.cn/problems/check-if-point-is-reachable/solutions/2073036/wen-ti-zhuan-huan-gcdju-ti-gou-zao-fang-0plx0/)

* Comment:

對於這種 (x, y - x), (x - y, y) 這種操作，要聯想到輾轉相除法。

* g = gcd(x, y)

這種題目都可以利用反向思考逆推，收斂問題:

把操作 (x, y - x), (x - y, x), (2 * x, y), (x, 2 * y)

反向從目標往回推: (x, y + x), (x + y, x), (x / 2, y), (x, y / 2)

證明:

* 如果 x, y 都是偶數 -> 不改變最大公因數是 $2^k$ 的性質

* 如果 x, y 其一是偶數 -> 將偶數除二也不改變最大公因數

* 如果都是奇數:
    
    * 且假設 x > y ，那可以透過兩步操作 x > (x + y) / 2 > y，使得 x 變小，根據最大公因數 gcd(x, y) = g(x + y, y) ，不會改變最大公因數

    * x < y 同上

    * 若是 x = y, 此時無法做任何操作變小 -> 判斷是否 x = y = 1


### 3307. 找出第 K 个字符 II

* Score: 2232

* Quality: 8

* Date: 2025/12/01

* [link](https://leetcode.cn/problems/find-the-k-th-character-in-string-game-ii/solutions/2934284/liang-chong-zuo-fa-di-gui-die-dai-python-5f6z/)

* Comment:

沒有認真地思考問題的本質，思考不夠清楚，然後用 try & error 的方式 wa 了很多次。

本質上相當於遍歷 k - 1 二進制的每個 bit ，累加對應的 operations[i] 。

### 3614. 用特殊操作处理字符串 II

* Score: 2011

* Quality: 8

* Date: 2025/12/02

* [link](https://leetcode.cn/problems/process-string-with-special-operations-ii/solutions/3722462/ni-xiang-si-wei-pythonjavacgo-by-endless-26al/)

* Comment:

這題只卡在產生的字串中有可能變為空字串，所以一開始計算長度的時候要加 max，才能正確的統計最終產生的長度


### 3419. 图的最大边权的最小值

* Score: 2243

* Quality: 0

* Date: 2025/12/03

* [link](https://leetcode.cn/problems/minimize-the-maximum-edge-weight-of-graph/)

* Comment:

沒想清楚問題!

由於問題問的是從其他節點到原點，一開始就可以將路徑反向從原點出發至其他節點。

對於問題的 treshold 約束，若是能夠不重複地走完所有節點，則可以滿足 threshold 的限制 -> threshold 是多餘的。

有兩種方法

1. 二分+DFS

二分猜最大邊的值，DFS走 <= upperbound，判斷能否走完

2 dijkstra

把普通的 dijkstra 中 + 改成 max

### 3609. 到达目标点的最小移动次数

* Score: 2419

* Quality: 0

* Date: 2025/12/05

* [link](https://leetcode.cn/problems/minimum-moves-to-reach-target-in-grid/solutions/3716440/ni-xiang-si-wei-fen-lei-tao-lun-yan-ge-z-m5cc/)

* Comment:

    這題同樣是逆向思維，但是想的時候沒想清楚，分類討論的部分沒處理好。

    由題目的限制可以發現，只有每次的增加量為 max(x, y)，從 (x, y) -> (x + m, y) or (x, y + m)，所以可以知道增加後的 x + m, y + m 仍然會是最大值。

    所以由反向逆推，每次都從當前最大的 x/y 想辦法往回走，而對於 x = y 的部分則要另外的分類討論。

    對於這種題目可以假設 x >= y 減少討論的情況。

    若是 x / 2 >= y 則可以知道前一個步驟是 (x' + m, y), m = x'，且 x % 2 == 0。 往回走的話則是 (x / 2, y)

    若是 x / 2 < y，則我們需要使用 m = y'。 往回走的話則是 (x - y, y)

    最後分類討論 x == y

    當 x = y = 0 則 x, y 都不可能增加 -> 無解

    若 != 0, 則可將其中一個 x or y 改為 0 後再計算 (x, 0)

### 1775. 通过最少操作次数使数组的和相等

* Score: 1850

* Quality: 0

* Date: 2025/12/08

* [link](https://leetcode.cn/problems/equal-sum-arrays-with-minimum-number-of-operations/solutions/2009786/mei-xiang-ming-bai-yi-ge-dong-hua-miao-d-ocuu/)

* Comment:

    這題是貢獻法
    
    題目的核心為如何儘快的減少兩個陣列和的差距。如果想到這，那很簡單的部分就是將其中一個陣列往上加，另一個往下減，同時兩個陣列都是使用當前可以對差距最大的貢獻。

    解法為在貢獻上的雙指針。一個陣列從 1 開始 一個陣列從 6 開始，此時對差距的貢獻皆為 -5 ，可以參考題解的寫法。

### 3440. 重新安排会议得到最多空余时间 II

* Score: 1998

* Quality: 8

* Date: 2025/12/09

* [link](https://leetcode.cn/problems/reschedule-meetings-for-maximum-free-time-ii/solutions/3061629/wei-hu-qian-san-da-de-kong-wei-mei-ju-fe-xm2f/)

* Comment:

    這題一開始看錯題意，沒看到只能移動一次，wa 了一次。

    這題的抽象思考很容易想到，把當前一個會議時間移到其他可以容納的空閒時間，若是無法移動則貪心的移到兩旁的空閒時間。

    其中有想到只需要維護前三大的空閒時間，但是同時要維護與空閒時間連接的會議 idx 資訊，邏輯變得有點複雜，索性就把所有資料放進陣列進行排序。

    觀看題解發現其實不需要這麼麻煩，就把所有空閒時間進行編號，我們會有 n + 1 個空位，其中開頭和結尾要特別計算，要是有會議占用到開頭或結尾，那就把牠們規定為 0 。
    
    其他的空閒時間 可以利用 start[i] - end[i - 1] 得到時長。
    
    對於某個會議時間兩旁的空閒時間編號只要檢查 i, i + 1 就可以了。

### 2333. 最小差值平方和

* Score: 2011

* Quality: 9

* Date: 2025/12/10

* [link](https://leetcode.cn/problems/minimum-sum-of-squared-difference/description/)

* Comment:

    這題的核心想法都有想到，唯一的問題就是實作太慢。

    核心想法:

    1. 由題目可以知道 a[i] - b[i] = d[i]，必須 $min(\sum{d_{i}^2})$

    2. 對於 $d_{i}^2$ 做一次操作可以知道會相差 $d_{i}^2 - (d_{i} - 1)^2 = 2 \times d - 1$ ，或是用圖像的方式思考，d * d 的正方形跟 (d - 1) * (d - 1) 的正方形，其面積會相差 2 * d - 1，我在寫的時候是使用正方形的方式思考。

    3. 該如何操作? 可以知道因為操作一次的貢獻會是 2 * d - 1，所以由大到小的進行操作是最好的選擇。

    4. 把相同的元素分成一組，每次一組一組的由大到小進行操作，藉由查看和下一組的差距，可以知道能不能把操作使用完，如果用不完則把當前元素改成下一組元素，並加到下一組，直到操作數用完。
    
    這個方法用圖像方式思考的話可以想像成階梯型，每次都處理當前台階的部分，看當前台階的面積可不可以減去，若可以那就減去，若不行那就必須計算要減到多少。

### 2141. 同时运行 N 台电脑的最长时间

* Score: 2265

* Quality: 9

* Date: 2025/12/12

* [link](https://leetcode.cn/problems/maximum-running-time-of-n-computers/solutions/1214440/liang-chong-jie-fa-er-fen-da-an-pai-xu-t-grd8/)

* Comment:

    這題可以看做相鄰不同貪心(配對貪心)的延伸題目，一般來說這種類型的題目是要把所有元素進行兩兩配對，並且每個配對不能有相同元素，若是有一個元素的頻率超過整體的 $\frac{1}{2}$，那可以用該元素和剩下的元素進行配對，若是最多元素不超過 $\frac{1}{2}$ 則可以保證得到 $\frac{sum}{2}$ 個不相同元素的配對。

    而這題則是要把不同電池的時間分配給 n 台電腦，可以把電池時間以一個一個時間單位做切分，用配對貪心的思路去思考，跟配對貪心的問題的差別在每個配對要 n 個不同元素，因此可以知道配對數量的上限為 $\lfloor \frac{sum}{n} \rfloor$，由此可以知道若有一個元素超過 $\lfloor \frac{sum}{n} \rfloor$ ，多出來的部分無論怎麼分配都無法加到最終的分配裡(因為最終分配只有 $\lfloor \frac{sum}{n} \rfloor$)。

    而可以知道若是一個元素超過 $\lfloor \frac{sum}{n} \rfloor$ ，那最終分配的數量會與剩下的元素能否進行分配有關，設前面所說的元素為 $x$ 最終配對數最多會是 $\lfloor \frac{sum - x}{n - 1} \rfloor$。

    * 結論: 若該電池電量超過 $\lfloor \frac{sum}{n} \rfloor$，則將其供給一台電腦，問題縮減為 n − 1 台電腦的子問題。

    * 這題與 [CSES/String Reorder](https://cses.fi/problemset/task/1743) 類似，要在子問題維護配對貪心的性質。

### 2576. 求出最多标记下标

* Score: 1843

* Quality: 8

* Date: 2025/12/22

* [link](https://leetcode.cn/problems/find-the-maximum-number-of-marked-indices/solutions/2134078/er-fen-da-an-pythonjavacgo-by-endlessche-t9f5/)

* Comment:

    這題一開始沒思考清楚，以及被題目的例題帶偏，想用同向雙指針盡可能的將比較大的元素進行配對，但是這會造成比較大的元素用完後，剩下的元素的無法配對。

    ex. 2 3 5 10 這個例子 10, 5 配對後剩下的 3,2 無法進行配對。所以顯然必須使用前半和後半元素進行配對。先由小至大排序，想像前半和後半兩條序列進行一對一配對，若是前半最小的元素 * 2 比後半最小元素還要小那就表示匹配，若是比後半還大那就把後半匹配的位置往後移一位。

### 3366. 最小数组和

* Score: 2900(貪心)

* Quality: 0

* Date: 2025/12/25

* [link](https://leetcode.cn/problems/minimum-array-sum/solutions/2998867/jiao-ni-yi-bu-bu-si-kao-dpcong-ji-yi-hua-0pc5/)

* Comment:

    這題的普通解法是 O(n * op1 * op2) 的 DP 解法，是很直觀的 DP 題型。

    [貪心解法](https://leetcode.cn/problems/minimum-array-sum/solutions/2998867/jiao-ni-yi-bu-bu-si-kao-dpcong-ji-yi-hua-0pc5/):

    首先有一個直觀的貪心方法，對於 op2 會有固定的貢獻 k，那 op1 要如何操作會使得貢獻可以優於 op2 呢? 設 nums[i] = x ， 則 
    
    \[
    \begin{array}{ll}
    x - \frac{(x + 1)}{2} \ge k \\
    \rightarrow \frac{(x - 1)}{2} \ge k \\
    \rightarrow x \ge 2k + 1
    \end{array}
    \]

    當 $[2k + 1, \infty)$ 時先做 op1 再做 op2 可以得到更好的貢獻，因此可以先有一些初步的貪心作法

    1. $[2k + 1, \infty)$ 先做 op1 再做 op2。

    2. $[k, 2k + 1)$ 先放著等等再討論。

    3. $[1, k)$ 只能使用 op1

    對於 2. 的區間，勢必只能先做 op2 再做 op1 那怎麼去操作可以有更好的複雜度呢?

    假設 $x, y \in [k, 2k + 1)\text{，且 } x \le y$ :

    1. op2 給 x ，op1 給 y: 
        
        $x - k + \frac{y + 1}{2}$

    2. op1 給 x ，op2 給 y: 

        $y - k + \frac{x + 1}{2} \ge x - k + \frac{y + 1}{2}$
    
    3. op1 和 op2 都給 x:

        $y + \frac{x - k + 1}{2} \ge x - k + \frac{y + 1}{2}$

    4. op1 和 op2 都給 y:

        $x + \frac{y - k + 1}{2} \ge x - k + \frac{y + 1}{2}$
    
    可以知道方案 a 的操作方式是最優的。

    可以得到一個貪心的做法由小到大先做 op2 ，接著排序後再由大到小做 op1。

    * 這裡會有一個陷阱當 k 為奇數時若是 x 為偶數，那此時會發現對 x 先做 op2 後做 op1，會因為上取整的關係導致不如對另一個奇數做 op2。

    -> 若是 k 為奇數 x 為偶數時，同時分配到操作 op2, op1 ，則要把 op2 分配到一個沒被操作過的奇數。

### 3782. 交替删除操作后最后剩下的整数

* Score: 2074

* Quality: 0

* Date: 2025/12/29

* Comment:

    由於每次操作後得到的數列皆為等差數列，所以可以利用首相跟等差描述整個等差數列，不斷計算操作後得到的等差數列直到最後長度為 1。

    ad-hoc:

    把數列改成 0-indexed -> 0, 1, 2, ... ... , n - 1。

    第一次操作會刪掉所有的奇數 -> 0, 2, 4, ... .. -> 最終答案會是數列中的偶數

    接著將所有數除 2，又可以得到從 0 開始的數列 -> 0, 1, 2, ... 。

    這時會需要從後面開始刪除數列中的數:

    假設數列最後一個數是偶數 -> 0, 1, 2, 3, 4 (可以知道在第一次操作前原本的數列 n - 1 = 8 or 9)，此時要刪掉所有奇數，剩餘皆為偶數，與最後一個數(此時為 n - 1 右移一位)的奇偶相同。

    假設數列最後一個數是偶數 -> 0, 1, 2, 3, 4, 5 (可以知道在第一次操作前原本的數列 n - 1 = 10 or 11)，此時要刪掉所有偶數，剩餘皆為奇數，與最後一個數(此時為 n - 1 右移一位)的奇偶相同。

    接著模擬一下 ex: n - 1 = 14, 數列 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14

    第一次操作: 0, , 2, , 4, , 6, , 8, , 10, , 12, , 14

    位移: 0, 1, 2, 3, 4, 5, 6, 7 相當於 (n - 1) >> 1

    第二次操作: , 1, , 3, , 5, , 7 相當於 ((n - 1) >> 1) & 1 => (n - 1) & 0b10

    位移: 0, 1, 2, 3 相當於 (n - 1) >> 2

    第三次操作: 0, , 2, 

    位移: 0, 1

    第四次操作: 1 => (n - 1) & 0b1010
    
    (n - 1) & 0b1010 = 10

    conclusion:

    最後可以得到一個結論，剩下的元素為 (n - 1) & 0xAAAAAAAA...

### 3772. 树中子图的最大得分

* Score: 2235

* Quality: 3

* Date: 2025/12/30

* Comment:

    換根DP，不夠熟悉。對於貢獻的改變不夠熟悉，需要仔細思考確定每個子樹可能的值域以及該怎麼做貢獻。

---

### 2071. 你可以安排的最多任务数目

* Score: 2648

* Quality: 0

* Date: 2026/01/07

* Comment:

    因為寫的是雙序列貪心的題單，所以思路一直卡在如何將兩個序列配對。

    這題是二分答案，主要的貪心想法是可以使用最小的 k 個去配對最大的 k 個。若是有需要使用操作的部分則儘可能的讓該元素去配對比較大的元素，因此使用 deque 把可以配對的元素加進佇列中，貪心的部分是這題的另一個難點。

---

### 2673. 使二叉树所有路径值相等的最小代价

* Score: 1917

* Quality: 8

* Date: 2026/01/22

* Comment:

這題我用換根想法去做，但是這題若可以緊抓其核心想法，可以有更好的寫法。

核心的想法是對於兄弟節點(相同父節點的葉子節點)由根節點至其父節點的路徑和都是相同的，唯一不同的地方是兄弟節點的部分，若是兄弟節點為 x, y， 且 $x \le y$，則可以對答案作出貢獻 y - x。這個結論沒有那麼直觀，因為可能會考慮到