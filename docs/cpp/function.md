### count/count_if

## count

統計某個值出現幾次。

```cpp
count(first, last, value);
```

**C++20**

```cpp
//ranges
vector<int> a = {1,2,3,2,2};
cout << ranges::count(a, 2);

//projection
vector<pair<int,int>> v = {
    {1,10},
    {2,20},
    {1,30}
};

cout << ranges::count(v, 1, &pair<int,int>::first);
//等於
count_if(v.begin(), v.end(),
    [](auto &p){
        return p.first == 1;
    });
```

## count_if

依照條件統計。

```cpp
vector<int> a = {1,2,3,4,5,6};

cout << count_if(a.begin(), a.end(),
    [](int x){
        return x % 2 == 0;
    });
```

**C++20**

```cpp
cout << ranges::count_if(a,
    [](int x){
        return x % 2;
    });
```

### clamp

將給定值限制在指定的範圍內。

```cpp
#include <algorithm>
using namespace std;

//val = [min, max]
clamp(val, min, max);
```

## C++20

### split

空白切割字串

```cpp
for (auto token : s | views::split(' ')) {
    //token 是 subrange iterator ，不是 string
    if (token.begin() == token.end()) continue;//自動忽略多個空白 -> 否則會產生空字串
    string_view sv(token.begin(), token.end());
}
```

* 切「任意空白」（space / tab / newline）

```cpp
for (auto token : s | views::split_if(isspace)) {
    ...
}
```

### sstream

[ref](https://home.gamer.com.tw/creationDetail.php?sn=4114818)

```cpp
#include <sstream>
#include <iostream>
using namespace std;
int main()
{
    ////////基本的初始化stringstream
    stringstream s1;
    s1.str("");
    s1.clear();
}
```

```cpp
istringstream ss(path);
string s;
while (getline(ss, s, '/')) {
    if (s.empty() || s == ".") {
        continue;
    }
    if (s != "..") {
        stk.push_back(s);
    } else if (!stk.empty()) {
        stk.pop_back();
    }
}
```

### 刪除重複元素
```cpp
v.erase(unique(v.begin(),v.end()),v.end());
s.erase(unique(s.begin(),s.end()),s.end(), \
				[](char a,char b){return a==' ' && a==b;});
				
//1.
sort( vec.begin(), vec.end() );
vec.erase( unique( vec.begin(), vec.end() ), vec.end() );
//2.
set<int> s( vec.begin(), vec.end() );
vec.assign( s.begin(), s.end() );
```

### nth_element

```cpp
vector<int> arr;
ranges::nth_element(arr, arr.end() - k); //k ~ end large
```

### binary search

* binary_search 用來判斷一個元素是否存在於已排序的區間
  => lower_bound + 檢查是否相等

* 時間複雜度為 O(log n)

**前提：必須排序**

```cpp
#include <algorithm>
#include <vector>

vector<int> a = {1, 3, 5, 7, 9};

cout << binary_search(a.begin(), a.end(), 5);  // 1 (true)
cout << binary_search(a.begin(), a.end(), 6);  // 0 (false)
```

**C++14**
```cpp
vector<pair<int,int>> v = {
    {1,10},
    {3,20},
    {5,30}
};

bool ok = binary_search(
    v.begin(), v.end(),
    3,
    [](const auto &p, int x){
        return p.first < x;
    }
);
```

**C++20**

```cpp
vector<int> a = {1,3,5,7,9};

bool ok = std::ranges::binary_search(a, 5);

vector<pair<int,int>> v = {{1,10}, {3,20}, {5,30}};

bool ok = std::ranges::binary_search(
    v,
    3,
    {},
    &pair<int,int>::first
);
```


### deque

* deque 可以看成 random access O(1)
* 因此可以用 binary search 算距離

```cpp
auto it = dq.begin();
it += 3;
it + k
it - k
it1 - it2
it < it2
dq[i]
```

* 跟vector 差別在不是整塊連續，deque 底層通常是很多固定大小的 block：，`dq[i]` 的時候會找到那塊在哪再 access

* 支援STL:
```cpp
sort(dq.begin(), dq.end());

nth_element(...);

lower_bound(...);

upper_bound(...);

binary_search(...);
```