# Sorting and Searching

### 1. Distinct Numbers

要注意的是如果使用 unordered_set 會因為 hash colision 導致 TLE

要用 sort 或是 set。

```cpp
void solve() {
    int n;
    cin >> n;
    vector<int> a(n);
    for(int i = 0; i < n; ++i) {
        cin >> a[i];
    }
    ranges::sort(a);
    a.erase(ranges::unique(a).begin(), a.end());
    cout<<a.size()<<endl;
}
```