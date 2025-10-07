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