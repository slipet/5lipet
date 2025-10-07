# Codeforces

## Practice Round [#1052](https://codeforces.com/contest/2146)

### A - Equal Occurrences

### B - Merging the Sets

??? note "Details"    
    
    * Thought in the contest:
        這題一開始很容易地想到如果只有一個 $S_i$ 有唯一的一個數那這個 $S_i$ 肯定是要選的，所以想到了若是把這些必須選的 $S_i$ 挑出來，那剩下的部分就看可不可以構造出答案。 -> 正向的思維

        但是這裡很快就會碰到瓶頸，花了很多時間思考如何構造出三種以上的方案，然後想到了若是有方案 A，和方案 B ，則可以使用 A + B 構造出第三種方案。
        接著是如何構造出兩種不同的方案，這裡花了很久的時間才想到，把剩下的元素剔除必選的 $S_i$，並計算剩下元素有多少可選的 $S_j$ 只要有一個元素 >= 2 則可以構造出三種以上的方案。

        <span style="color:red">花太多時間思考如何拆分成兩個以上的方案</span>

    * Solution:

        官解為計算選擇 n - 1 和 n 個 S 的方案有多少個，這個比較像是逆向思維，一開始想到如果全選的話就可以知道可不可能構造出解答，然後想到只需要構造出方案 A, B, A + B 就好，因此若是移除一個 S 就看看是否仍然可以構造出答案，計算 n - 1 個 S 能構造出答案的有幾個。

        

### C - Wrong Binary Search

### D - Max Sum OR (Easy Version)

## Practice Round [#1053](https://codeforces.com/contest/2151) (Div.2)

### A - Incremental Subarray

### B - Incremental Path

??? note "Details"

    * Thought in the contest:
        
        這題沒有很好的想法。
    
    * Solution:

        似乎是要使用模擬範例來觀察如何得到答案。關注點應該是 i-th 和 (i - 1)-th 如何變化


### C - Incremental Stay

??? note "Details"

    * Thought in the contest:
        一開始也是用貪心的想法，但是沒有找到對的方向想。
    
    * Solution:

        如果要使得答案最大，那必須盡量讓 k 個人待在博物館裡，所以前 k 個時間點必須是進入，最後 k 個時間點肯定是離開。如果有抓到這個想法，可以很容易地用前綴和計算所需時間，但是對於 k 個時間點後必須是 out in out in 這種排列，每次增加一個人都會使得 in -> out , out -> in，所以可以利用這個特性 * -1，我覺得一開始可以先看每個時間點如何對答案進行貢獻，每個時間點可以貢獻 + or - 構造出最後答案。

        可以參考這個寫法學習一下怎麼寫比較漂亮 [code](https://codeforces.com/contest/2151/submission/342031350)

### D - Grid Counting