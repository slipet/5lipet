# Z algorithm

我們建立一個跟長度為 n 的字串 S 一樣長的array Z，Z[i] 代表著從 S[i] 開始跟 S 匹配最長的字串長度。

ex.

- S: “aaaaa”  Z: [0,4,3,2,1]
- S: “aaabaab”  Z: [0,2,1,0,2,1,0]
- S: “abacaba”  Z: [0,0,1,0,3,1,0]

**z 数组，其中 z[i] = |LCP(s[i:], s)|**

定義 [l, r] 為 s 的前綴。

```
s[l, r] = s[i - l, r - l]

s = _ _ _# # # _[l_ _i# # # r]
        ^
       i-l => z[i - l]

z[i] >= min(z[i-l], r - l + 1)
z[i] 的長度不能超過 r - l + 1 或是 z[i-l]
```

```cpp
vector<int> zfunc(string &s) {
    const int n = s.length();
    vector<int> z(n);
    int l = 0, r = 0;
    for(int i = 1; i < n; ++i) {
        if(i <= r) {
            z[i] = min(z[i - l], r - i + 1);
        }
        while(i + z[i] < n && s[z[i]] == s[i + z[i]]) {
            l = i;
            r = i + z[i];
            z[i]++;
        }
    }
    z[0] = n;
    return z;
}
```