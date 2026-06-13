# Palindrome

### 回文枚舉

#### 雙指針

```cpp
bool is_palindrome(int l, int r) {
    while(l <= r) {
        if(s[l] != s[r]) return false;
        l++, r--;
    }
    return true;
}
```
#### 中心擴展法

- 中心擴展法
- 遍歷所有回文中心，然後向外擴展，一旦左右端點有不同即可以中止
- 要注意的是，起始位置會有左右兩個端點，在字串為奇數或偶數時會不相同

|i |left start|right start|
|--|----------|-----------|
|0 |         0|          0|
|1 |         0|          1|
|2 |         1|          1|
|3 |         1|          2|
|4 |         2|          2|
|5 |         2|          3|
|6 |         2|          3|

長度為奇數 → 中心為一個(**L** = **R**)

偶數 →              兩個(**L** ≠ **R**)

L 始終等於 i / 2

R 取決於  i 是否為奇數(因為 0-indexed 導致) → (i + 1) / 2

- 對於長度為 n 的字串 可以產生出 $2n - 1$ 組回文中心

```cpp
for(int i = 0; i < 2 * n - 1; ++i) {
    int l = i / 2, r = (i + 1) / 2;
    for(; l >= 0 && r < n && s[l] == s[r]; l--, r ++) {
        //...
    }
}
```

#### DP 預處理數組

* 記憶化搜索

```cpp
vector is_pal(n, vector<int>(n, -1));
auto dfs = [&](this auto &&dfs, int l, int r) -> int {
    if(l >= r) return true;
    int &res = is_pal[l][r];
    if(is_pal[l][r] != -1) return is_pal[l][r];
    res = (s[l] == s[r]) && dfs(l + 1, r - 1);
    return res;
};
```
* 迭代
  
```cpp
vector is_pal(n, vector<int>(n, 1));
for(int l = n - 1; l >= 0; --l) {
    for(int r = l + 1; r < n; ++r) {
        is_pal[l][r] = s[l] == s[r] && is_pal[l + 1][r - 1];
    }
}
```