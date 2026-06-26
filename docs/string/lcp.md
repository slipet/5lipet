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

假設排序後的字串集合按照順序為 $s_1, s_2, ... s_i, ... , s_j ...$。

1. 相鄰兩個字串的 LCP 是最長的
2. 假設 $$


* [3485. Longest Common Prefix of K Strings After Removal](https://leetcode.cn/problems/longest-common-prefix-of-k-strings-after-removal/)