# Base Conversion

進制轉換。

利用直式除法不斷的除 base，把得到的結果取反向，就會是進制轉換的結果。

時間複雜度為 $O(\log{U})$

```cpp
vector<int> convert(string& s, int b) {
    for (char& c : s) {
        c -= '0';
    }
    vector<int> digits;
    while (!s.empty()) {
        string nxt_s; // 用竖式除法计算 s / b 得到的商（十进制）
        int rem = 0; // s % b
        for (char c : s) {
            rem = rem * 10 + c;
            int q = rem / b; // 商
            if (q || !nxt_s.empty()) { // 忽略前导零
                nxt_s.push_back(q);
            }
            rem = rem % b;
        }
        digits.push_back(rem);
        s = move(nxt_s);
    }
    ranges::reverse(digits);
    return digits;
}
```

* 拓展從 from_base 到 to_base

```cpp
vector<int> convert(vector<int> &digits, int from_base, int to_base) {
    vector<int> res;
    while (!digits.empty()) {
        vector<int> nxt; // 用竖式除法计算 s / b 得到的商（十进制）
        int rem = 0; // s % b
        for (char c : digits) {
            rem = rem * from_base + c;
            int q = rem / to_base; // 商
            if (q || !nxt.empty()) { // 忽略前导零
                nxt.push_back(q);
            }
            rem = rem % to_base;
        }
        res.push_back(rem);
        digits = move(nxt);
    }
    ranges::reverse(res);
    return res;
}
```