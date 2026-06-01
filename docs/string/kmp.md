# KMP

prefix[i] 表示以 i 為結尾的 substring 和 string S 前綴相同的後綴長

舉例:

S: "abcabcd” prefix : [0,0,0,1,2,3,0]

S: "aabaaab” prefix : [0,1,0,1,1,2,3]


```cpp
vector<int> kmp(string &s) {
    const int n = s.length();
    vector<int> pi(n);
    for(int i = 1; i < n; ++i) {
        int j = pi[i - 1];
        while(j > 0 && s[j] != s[i]) {
            j = pi[j - 1];
        }
        pi[i] = j + s[i] == s[j];
    }
    return pi;
}
```