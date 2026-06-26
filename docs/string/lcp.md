# Longest Common Prefix/Suffix

最長公共前綴數組，通常用來當作預處理資訊。

$lcp[i][j]$ 表示 $s[i:]$ 和 $s[j:]$ 的最長公共前綴。

* [2430. Maximum Deletions on a String](https://leetcode.cn/problems/maximum-deletions-on-a-string/solutions/1864049/xian-xing-dppythonjavacgo-by-endlesschen-gpx9/)


```cpp
vector lcp(n + 1, vector<int>(n + 1));
for(int i = n - 1; i >= 0; --i) {
    for(int j = n - 1; j >= i; --j) {
        if(s[i] == s[j])
            lcp[i][j] = lcp[i + 1][j + 1] + 1
    }
}
```

## 性質

**將一堆字串排序**

假設排序後的字串集 S 合按照順序為 $s_1, s_2, ... s_i, ... , s_j ..., s_n$。

1. 在大小為 n 有序的字串集合 S 中，全部集合的 $lcp(s_1, s_2, ..., s_i)$ 會等於第一個字串和最後一個字串的 lcp 也就是 $lcp(s_1, s_n)$
   假設集合中有 $S = \{ab, aba, abb\}$， 此時 $|lcp(s_1, s_3)| = m$ ，對於子陣列的 lcp 不會超過 m，如果超過，則表示 $|lcp(s_1, s_3)|$ 也同樣會超過 m ，因此矛盾。此外子陣列的 lcp 也不會小於 m，因為已經事先排序了，所以中間的字串 lcp 至少會是 m。
2. 
3. 在有序的字串集合中，兩個字符串相隔越遠，LCP 越短(不會變長)；兩個字符串相隔越近，LCP 越長(不會變短) $\rightarrow$ 相鄰兩個字串的 LCP 是最長的
4. 
5. 假設 $$


* [3485. Longest Common Prefix of K Strings After Removal](https://leetcode.cn/problems/longest-common-prefix-of-k-strings-after-removal/)