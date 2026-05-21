# Conclusion

放一些 DP 的心得/小結論

## 利用 f(i - 1) 將前綴最優狀態轉移

* Leetcode [#2320. 统计放置房子的方式数](https://leetcode.cn/problems/count-number-of-ways-to-place-houses/description/)

    Transition function: $f(i) = f(i - 1) + f(i - 2)$
    Initial: $f(0) = 1, f(1) = 2$

    $f(0)$ 代表選擇為空的方案數，$f(1)$ 代表選擇 $\{\{\emptyset\}, \{1\}\}$ 兩種方案。
    
    對於位置 i 來說有兩種選擇:

    1. 選擇當前位置: 構造出以當前位置為結尾可以產生出的合法方案，有 $f(i - 2)$ 個方案數。

    2. 不選擇當前位置: 不選當前位置可以產生出的方案，此時有 $f(i - 1)$ 個。

    $f(i-1)$ 代表不選第 $i$ 個位置的方案數（繼承前綴所有可能）；$f(i-2)$ 代表選擇第 $i$ 個位置的方案數（受限於相鄰約束）。

* Leetcode [3186. 施咒的最大总伤害](https://leetcode.cn/problems/maximum-total-damage-with-spell-casting/description/)

    Transition function: $f(i) = max(f(i - 1), f(j) + x \times n)$

    值域的打家劫舍只能取間隔超過 2 的元素。$f(i - 1)$ 的值同時也隱含著 $f(i - 2)$ 為最優的可能性。而 j 則是剛好 $< x - 2$ 的位置。
    
## 利用剩餘最優結果進行優化

也就是假設剩餘"最優"結果都沒辦法使當前結果更好，那就不繼續下去。

* [leetcode #3459. 最长 V 形对角线段的长度](https://leetcode.cn/problems/length-of-longest-v-shaped-diagonal-segment/description/)
    因為網格的大小固定，透過剩下路徑可能的長度進行減枝。

## 背包問題

* 背包問題的時間複雜度很關鍵，背包大小，物品個數
				
* 求能否構成target(true or false):
    f[i][c - w] || f[i][c] -> 可以用bitset加速
				
* 求方案數:
    f[c] + f[c - w]

* 種類
    1. 至少capacity: 方案數/最小價值和
        f[c] = (f[c] + f[max(c - w, 0)]) -> 求至少cap ，因為所有 < 0 的部分都是解，所以使用max將所有 < 0 的都當成f[0]
    2. 恰好capacity 方案數/最大/最小價值和
        只有在 $f(0)$ 是合法的初始值
    3. 最多capacity	方案數/最大價值和:
        max(f[c], f[c - w] + val/1)
        初始化的時候 $f(< 0)$ 為不合法，因為跟 c 的距離位置最多只到 0 ，$ < 0 $ 都是不合法的，而 $f(>= 0)$ 都會是合法的

* [879. 盈利计划](https://leetcode.cn/problems/profitable-schemes/description/)

    這題是二維背包問題，同時考了最多上限 n 下的最少 m 的方案數。
    
    * 對於最少 m 的限制很好處理 $max(0, c - x)$ 就可以了

    * 對於最多 n 的限制，我原本是


* [956. 最高的广告牌](https://leetcode.cn/problems/tallest-billboard/)

    相當於我們要從rods中選出兩個集合A和B，其中rods中的每一個元素，有三種選擇：

    1. 可以不選
    2. 選到A集合中
    2. 選到B集合中
    
    最後需要的是集合A和集合B的總和相等，也就是他們的差值為0，那麼這個類似於01背包問題，我們可以這樣定義：

    $f(i,j)$ 代表了從 $rods[0...i]$ 選若干元素到A，選若干元素到 $B$ 使得 $j=0$ 的可以得到的最大長度。
    轉移對應上面三種情況，這里規定到 $A$ 集合為正，到 $B$ 集合為負，那麼最大長度為只統計 $A$ 的和即可

    1. 不選: $f(i,j)=f(i−1,j)$
    2. 選到A中: $f(i,j) = rods[i]+ f(i−1,rods[i]+j)$
    3. 選到B中： $f(i,j)=f(i−1,j−rods[i])$
    最後三種情況取最大即可